---
title: Custom Provider for Casdoor
description: Add your custom OAuth provider to Casdoor
keywords: [Custom Provider, OAuth, Casdoor]
authors: [halozhy]
---

:::note

Casdoor supports custom providers. However, the custom providers must follow the standard process of 3-legged OAuth, and the return values of `Token URL` and `UserInfo URL` must conform to the format specified by Casdoor.

:::

To create a new custom provider, navigate to the provider page of Casdoor, and select “Custom” in the Type field. You will then need to fill in `Client ID`, `Client Secret`, `Auth URL`, `Scope`,`Token URL`, `UserInfo URL`, and `Favicon`.

![image-20220418100744005](/img/providers/OAuth/customprovider.png)

- `Auth URL` is the custom provider's OAuth login page address.

  If you fill in `https://door.casdoor.com/login/oauth/authorize` as the `Auth URL`, then, when a user logs in with this custom provider, the browser will first redirect to

  ```url
  https://door.casdoor.com/login/oauth/authorize?client_id={ClientID}&redirect_uri=https://{your-casdoor-hostname}/callback&state={State_generated_by_Casdoor}&response_type=code&scope={Scope}` 
  ```

  After authorization is completed, the custom provider should redirect to

  ```url
  https://{your-casdoor-hostname}/callback?code={code}
  ```

  After this step, Casdoor will recognize the code parameter in the URL.

- `Scope` is the scope parameter carried when accessing the `Auth URL`, and you should fill it in as per the custom provider's requirements.

- `Token URL` is the API endpoint for obtaining the accessToken.

  Once you obtain the code in the previous step, Casdoor should use it to get the accessToken.

  If you fill in `https://door.casdoor.com/api/login/oauth/access_token` as the `Token URL`, then Casdoor will access it using the following command

  ```bash
  curl -X POST -u "{ClientID}:{ClientSecret}" --data-binary "code={code}&grant_type=authorization_code&redirect_uri=https://{your-casdoor-hostname}/callback" https://door.casdoor.com/api/login/oauth/access_token
  ```

  The custom provider should return at least the following information:

  ```json
  {
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6Ixxxxxxxxxxxxxx",
    "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6xxxxxxxxxxxxxx",
    "token_type": "Bearer",
    "expires_in": 10080,
    "scope": "openid profile email"
  }
  ```

- `UserInfo URL` is the API endpoint for obtaining user information via the accessToken.

  If you fill in `https://door.casdoor.com/api/userinfo` as the `UserInfo URL`, then Casdoor will access it using the following command

  ```bash
  curl -X GET -H "Authorization: Bearer {accessToken}" https://door.casdoor.com/api/userinfo
  ```

  The custom provider should return at least the following information:

  ```json
  {
    "name": "admin",
    "preferred_username": "Admin",
    "email": "admin@example.com",
    "picture": "https://casbin.org/img/casbin.svg"
  }
  ```

- `Favicon` is the logo URL of a custom provider.

  This logo will be displayed on Casdoor's login page together with other third-party login providers.
