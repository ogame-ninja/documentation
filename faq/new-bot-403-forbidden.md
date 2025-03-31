## 403 forbidden when creating a new bot?

The `403 forbidden` error can occurs for 3 main reasons.

1. You are using the wrong gameforge email/password, and the bot fails to login.

2. If your username/password is correct, sometimes, creating a new "browser fingerprint" will fix the issue.

3. Otherwise, Gameforge sometimes **temporarily** blocks IP addresses when the server automatically flag an IP as being suspicious.  
In this scenario, you might get a `403 forbidden` even with the right email/password.  

  - You can either wait a few hours and retry once your IP is no longer blocked by the gameforge server.  

  - Alternatively, you can fill the form to reset your password in ogame.  
  Once done you will be logged in on ogame's website.  
  You can then use your `gameforge bearer token` (from your browser cookies)  
  to create the bot, and provide the token in the "advanced" section.