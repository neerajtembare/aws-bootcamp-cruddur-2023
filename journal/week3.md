# Week 3 — Decentralized Authentication

- **Amazon Cognito Security Best Practices**
    - Common types of App Authentication
    
    ![IMG](assets1/week3/Untitled%2043.png)
    
    What is DeCentralized Authentication
    
    - all user creds and passwords are stored in a single application which can then provide them when needed for auth
    
    ### What is Amazon Cognito
    
    - Amazon Cognito is a service which allows authentication with users that it stores locally on the account
    - Its basically a user directory with the context of AWS services
    
    -There are two types of amazon cognito
    
    1. **Cognito user pool** - *Add user directories to your app*
        - Cognito user pool does authentication using oAuth and get auth from user to use their credentials
        - manages user data
    2. **Cognito Identity Pool** - *Grant access to AWS services*
        - it does User Authorization and provides roles using IAM
        - manages user access
        - it provides temporary credentials
    
    ![](assets1/week3/Untitled%2044.png)
    
    ### Using AWS Cognito
    
    - In cognito you can either add user to the pool
    - Or you can grant access to them
    
    Using Cognito
    
    - search cognito in aws console
    - go to the add user directories and click on add
    - select email and click next
    - select Cognito Defaults , optional MFA, auth apps because SMS needs money
    - enable self service, and email only
    - dont change anything step 3 (you can additional attribuites if your application requires it
    - step 4 select send email with cognito (chargesdd beyond a certain point)
    - step 5 - give random pool name, tick cognito hosted UI
    - give same pool name to cognito domain ‘pool’
    - put URL as [https://localhost:80/callback](https://localhost:80/callback)
    - click next and reviews the changes and then click on create pool
    
    federated identities is where we would have identity pool
    
    - go back to cognito and you can see the userpool you created
    - delete the user-pool when you dont need it anymore
    - Cognito pool remains in the reason you have created it in
    
    ### Why use AWS Cognito
    
    ![](assets1/week3/Untitled%2045.png)
    
    ### User/token c**ycle management**
    
    ![](assets1/week3/Untitled%2046.png)
    
    ![](assets1/week3/Untitled%2047.png)
    
    ## Amazon Cognito services
    
    ![](assets1/week3/Untitled%2048.png)
    
    ![](assets1/week3/Untitled%2049.png)
    
- **Live Session**
    
    Creating a user Pool 
    
    - created a user pool
    
    ![](assets1/week3/Untitled%2050.png)
    
    managed to configure the signin page and seee the error message in the console
    
    ![](assets1/week3/Untitled%2051.png)
    
- **Cognito Custom pages**
    - after creating a user we get the forced change password status
    
    ![](assets1/week3/Untitled%2052.png)
    
    - so we need to allow the passwoed to be set
    - so that they can add the user and update status and then access the cognito pool
    
    - go to terminal and open aws console and enter the folowwing command
    
    ```json
    aws cognito-idp admin-set-user-password --username neerajt --password Neeraj@161 --user-pool-idus-east-1_SXTgvVPsP --permanent
    ```
    
    ********************Successfull signin with the credentials********************
    
    ![](assets1/week3/Untitled%2053.png)
    
    Managed to change the user name 
    
    ![](assets1/week3/Untitled%2054.png)
    
    - now we need to setup the signup page so delete the cognito user
    - then paste this in signUP.js
    
    ```jsx
    import { Auth } from 'aws-amplify';
    ```
    
    and then make the following changes
    
    ```jsx
    const onsubmit = async (event) => {
        event.preventDefault();
        setErrors('')
        try {
          const { user } = await Auth.signUp({
            username: email,
            password: password,
            attributes: {
              name: name,
              email: email,
              preferred_username: username,
            },
            autoSignIn: { // optional - enables auto sign in after user is confirmed
                enabled: true,
            }
          });
          console.log(user);
          window.location.href = `/confirm?email=${email}`
        } catch (error) {
            console.log(error);
            setErrors(error.message)
        }
        return false
      }
    ```
    
    ![](assets1/week3/Untitled%2055.png)
    
    now add the resend code 
    
    ```jsx
    const resend_code = async (event) => {
      setCognitoErrors('')
      try {
        await Auth.resendSignUp(email);
        console.log('code resent successfully');
        setCodeSent(true)
      } catch (err) {
        // does not return a code
        // does cognito always return english
        // for this to be an okay match?
        console.log(err)
        if (err.message == 'Username cannot be empty'){
          setCognitoErrors("You need to provide an email in order to send Resend Activiation Code")   
        } else if (err.message == "Username/client id combination not found."){
          setCognitoErrors("Email is invalid or cannot be found.")   
        }
      }
    }
    ```
    
    then change the onsubmit code
    
    ```jsx
    const onsubmit = async (event) => {
      event.preventDefault();
      setCognitoErrors('')
      try {
        await Auth.confirmSignUp(email, code);
        window.location.href = "/"
      } catch (error) {
        setCognitoErrors(error.message)
      }
      return false
    }
    ```
    
    <aside>
    💡 I changed code for resend_code by using ``` setErrors(' ')``` instead of setCognitoErrors('')  on line 25,36,38 in confirmationpage.js
    
    </aside>
    
    Signup and confirmating configured 
    
    ![](assets1/week3/Untitled%2056.png)
    
    **************************************************user Confirmed in Cognito**************************************************
    
    ![](assets1/week3/Untitled%2057.png)
    
- **Backend Implementation for Cognito**
    - in this we pass along our access token which was earlier created in the signin page stored in the local storage
    - we pass aliong that token to the backend
    
    - use the below code to pass along that token to the backend
    - enter the code in homefeed.js
    
    ```jsx
    headers: {
        Authorization: `Bearer ${localStorage.getItem("access_token")}`
      }
    ```
    
    enter the following code into [app.py](http://app.py) as shown
    
    ```jsx
    app.logger.debug("AUTH-HEADER")
      app.logger.debug(
        request.headers.get('Authorization')
      )
    ```
    
    - after entering the code we can refresh the site and see that teh access token is passed along in the terminal
    
    ```
    app.logger.debug("AUTH-HEADER")
      app.logger.debug(
        request.headers.get('Authorization')
      )
    ```
    
    ![](assets1/week3/Untitled%2058.png)
    
    <aside>
    💡 You will get access token only if you are signedin on cruddur
    
    </aside>
    
    **CORS changes** 
    
    - in [app.py](http://app.py) line 85
    
    ```jsx
    cors = CORS(
      app, 
      resources={r"/api/*": {"origins": origins}},
      headers=['Content-Type', 'Authorization'], 
      expose_headers='Authorization',
      methods="OPTIONS,GET,HEAD,POST"
    )
    ```
    
    ![](assets1/week3/Untitled%2059.png)
    
    Add in requirements.txt annd 
    
    ```jsx
    Flask-AWSCognito
    ```
    
    - then cd to backendflask and then run
    
    ```jsx
    pip install -r requirements.txt
    ```
            
        
     [Go to this link and copt the below code from example app](https://github.com/cgauge/Flask-AWSCognito)
        
        ```
        AWS_COGNITO_USER_POOL_ID: 'us-east-1_tRVIWaqYy'
        AWS_COGNITO_USER_POOL_CLIENT_ID: '5oqn5hsa1j9ehjbn4ihdf4i3bn'
        ```
        
        - add to docker compose as shown
        
        ![Untitled](Untitled%2060.png)
        
        - add to app.py
        
        ```jsx
        from flask_awscognito import AWSCognitoAuthentication
        
        app.config['AWS_COGNITO_USER_POOL_ID'] = os.getenv("AWS_COGNITO_USER_POOL_ID")
        app.config['AWS_COGNITO_USER_POOL_CLIENT_ID'] = os.getenv("AWS_COGNITO_USER_POOL_CLIENT_ID")
        
        aws_auth = AWSCognitoAuthentication(app) 
        ```
        
     ![](assets1/week3/Untitled%2061.png)
        
    
    - ******************************************Cognito token service******************************************
        - create a new folder in backend.flask and then create a python file in it
        - in the file copy the following code
        
        ```jsx
        import time
        import requests
        from jose import jwk, jwt
        from jose.exceptions import JOSEError
        from jose.utils import base64url_decode
        
        class FlaskAWSCognitoError(Exception):
          pass
        
        class TokenVerifyError(Exception):
          pass
        
        def extract_access_token(request_headers):
            access_token = None
            auth_header = request_headers.get("Authorization")
            if auth_header and " " in auth_header:
                _, access_token = auth_header.split()
            return access_token
            
        class CognitoJwTToken:
            def __init__(self, user_pool_id, user_pool_client_id, region, request_client=None):
                self.region = region
                if not self.region:
                    raise FlaskAWSCognitoError("No AWS region provided")
                self.user_pool_id = user_pool_id
                self.user_pool_client_id = user_pool_client_id
                self.claims = None
                if not request_client:
                    self.request_client = requests.get
                else:
                    self.request_client = request_client
                self._load_jwk_keys()
        
            def _load_jwk_keys(self):
                keys_url = f"https://cognito-idp.{self.region}.amazonaws.com/{self.user_pool_id}/.well-known/jwks.json"
                try:
                    response = self.request_client(keys_url)
                    self.jwk_keys = response.json()["keys"]
                except requests.exceptions.RequestException as e:
                    raise FlaskAWSCognitoError(str(e)) from e
        
            @staticmethod
            def _extract_headers(token):
                try:
                    headers = jwt.get_unverified_headers(token)
                    return headers
                except JOSEError as e:
                    raise TokenVerifyError(str(e)) from e
        
            def _find_pkey(self, headers):
                kid = headers["kid"]
                # search for the kid in the downloaded public keys
                key_index = -1
                for i in range(len(self.jwk_keys)):
                    if kid == self.jwk_keys[i]["kid"]:
                        key_index = i
                        break
                if key_index == -1:
                    raise TokenVerifyError("Public key not found in jwks.json")
                return self.jwk_keys[key_index]
        
            @staticmethod
            def _verify_signature(token, pkey_data):
                try:
                    # construct the public key
                    public_key = jwk.construct(pkey_data)
                except JOSEError as e:
                    raise TokenVerifyError(str(e)) from e
                # get the last two sections of the token,
                # message and signature (encoded in base64)
                message, encoded_signature = str(token).rsplit(".", 1)
                # decode the signature
                decoded_signature = base64url_decode(encoded_signature.encode("utf-8"))
                # verify the signature
                if not public_key.verify(message.encode("utf8"), decoded_signature):
                    raise TokenVerifyError("Signature verification failed")
        
            @staticmethod
            def _extract_claims(token):
                try:
                    claims = jwt.get_unverified_claims(token)
                    return claims
                except JOSEError as e:
                    raise TokenVerifyError(str(e)) from e
        
            @staticmethod
            def _check_expiration(claims, current_time):
                if not current_time:
                    current_time = time.time()
                if current_time > claims["exp"]:
                    raise TokenVerifyError("Token is expired")  # probably another exception
        
            def _check_audience(self, claims):
                # and the Audience  (use claims['client_id'] if verifying an access token)
                audience = claims["aud"] if "aud" in claims else claims["client_id"]
                if audience != self.user_pool_client_id:
                    raise TokenVerifyError("Token was not issued for this audience")
        
            def verify(self, token, current_time=None):
                """ https://github.com/awslabs/aws-support-tools/blob/master/Cognito/decode-verify-jwt/decode-verify-jwt.py """
                if not token:
                    raise TokenVerifyError("No token provided")
        
                headers = self._extract_headers(token)
                pkey_data = self._find_pkey(headers)
                self._verify_signature(token, pkey_data)
        
                claims = self._extract_claims(token)
                self._check_expiration(claims, current_time)
                self._check_audience(claims)
        
                self.claims = claims
                return claims
        ```
        
        - Then make the following changes in app.py
        
        ![](assets1/week3/Untitled%2062.png)
        
        ![](assets1/week3/Untitled%2063.png)
        
   
    #### Authenticated Claim message recieved
    - when signed in the secret message is visible (by @lore)

        ![](assets1/week3/Untitled%2064.png))

    - After signing out the secret message disappers

        ![](assets1/week3/Untitled%2065.png))
