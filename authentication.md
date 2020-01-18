# Password policy

- [ ] Review the website for any description of the policy.
- [ ] If self-registration is possible, attempt to register several accounts with different kinds of weak passwords to discover what rules are in place.
- [ ] If you control a single account and password change is possible, attempt to change your password to various weak values.

# Login Form

(Rate-limit)

- [ ] Manually submit several bad login attempts for an account you control, monitoring the error messages you receive.
- [ ] After about 10 failed logins, if the application has not returned a message about account lockout, attempt to log in correctly. If this succeeds, there is probably no account lockout policy.
- [ ] If the account is locked out, try repeating the exercise using a different account. This time, if the application issues any cookies, use each cookie for only a single login attempt, and obtain a new cookie for each subsequent login attempt.
- [ ] Also, if the account is locked out, see whether submitting the valid pass- word causes any difference in the application’s behavior compared to an invalid password. If so, you can continue a password-guessing attack even if the account is locked out.

(username enumeration)

- [ ] If you already know one valid username (for example, an account you control), submit one login using this username and an incorrect password, and another login using a random username.
- [ ] Record every detail of the server’s responses to each login attempt, including the status code, any redirects, information displayed on- screen, and any differences hidden in the HTML page source. Use your intercepting proxy to maintain a full history of all traffic to and from the server.
- [ ] Attempt to discover any obvious or subtle differences in the server’s responses to the two login attempts.

(Scanning)

- [ ] Scan login form with automation (sqlmap etc)
- [ ] Test for default credentials

(insecure transmission)
- [ ] Test for Weak Login Implementation - HTTPS not Available or HTTP by Default / HTTP and HTTPS Available

# Registration Form

(insecure transmission)

- [ ] Test for Weak Registration Implementation - Over HTTP

(blind xss)

- [ ] Submit a blind xss payload as the username, adress or any other potential information during registration.

(username uniqueness)

- [ ] If self-registration is possible, attempt to register the same username twice with different passwords.
- [ ] If the application blocks the second registration attempt, you can exploit this behavior to enumerate existing usernames even if this is not possible on the main login page or elsewhere. Make multiple registration attempts with a list of common usernames to identify the already registered names that the application blocks.
- [ ] If the registration of duplicate usernames succeeds, attempt to register the same username twice with the same password, and determine the application’s behavior:
   -  a. If an error message results, you can exploit this behavior to carry out a brute-force attack, even if this is not possible on the main login page. Target an enumerated or guessed username, and attempt to register this username multiple times with a list of common passwords. When the application rejects a specific password, you have probably found the existing password for the targeted account.
   -  b. If no error message results, log in using the credentials you speci- fied, and see what happens. You may need to register several users, and modify different data held within each account, to understand whether this behavior can be used to gain unauthorized access to other users’ accounts.


# Test account recovery function

- [ ] Understand how the forgotten password function works by doing a complete walk-through using an account you control.
- [ ] Check if Password reset Token not expiring after use
- [ ] Check if session is invalidated after password reset / change
- [ ] Check if user is logged in before changing password
    - https://hackerone.com/reports/809
    - https://hackerone.com/reports/23363
- [ ] Check for Password Reset link hijacking via Host Header Poisoning Filter bypass: Host: example.com?.target.com 
    - https://hackerone.com/reports/226659 
    - https://lightningsecurity.io/blog/host-header-injection/
- [ ] Test password resets for IDOR!
    - http://yasserali.com/how-i-could-change-your-ebay-password/
    - http://yasserali.com/microsoft-careers-com-remote-password-reset/
- [ ] Check if the token is leaked
    - https://footstep.ninja/posts/password-reset/

(insecure transmission)
- [ ] Test if Password Reset Token Sent Over HTTP
- [ ] Test for Sensitive Token in URL
- [ ] Test for Token Leakage via Referer - Over HTTP or 3rd party

A good acccount recovery flow looks like this:
```
When a user wants to reset a password 
- send him a link with a long and unpredictable token. 
- Make this link valid for short period of time. 
- When the token has already been used, make this link invalid.. 
- The user enters new password twice (add a CSRF token to the form). When successfully done, the user gets an information that the password has been changed (no need to disclose the login of the user). 
- Don't automatically log in a user - the user should provide a username and a new password to log in.
```

# Test any "remember me" function
- [ ] Activate any “remember me” functionality, and determine whether the functionality indeed does fully “remember” the user or whether it remem- bers only his username and still requires him to enter a password on sub- sequent visits. If the latter is the case, the functionality is much less likely to expose any security flaw.
- [ ] Closely inspect all persistent cookies that are set, and also any data that is persisted in other local storage mechanisms, such as Internet Explorer’s userData, Silverlight isolated storage, or Flash local shared objects. Look for any saved data that identifies the user explicitly or appears to contain some predictable identifier of the user.
- [ ] Even where stored data appears to be heavily encoded or obfuscated, review this closely. Compare the results of “remembering” several very similar usernames and/or passwords to identify any opportunities to reverse-engineer the original data. Here, use the same techniques that are described in Chapter 7 to detect meaning and patterns in session tokens.
- [ ] Attempt to modify the contents of the persistent cookie to try to con- vince the application that another user has saved his details on your computer.
- [ ] Check if a token is generated despite no checkbox checked.
    - https://hackerone.com/reports/105991


# Test any multi-stage mechanisms
- [ ] Brute force OTP
    - https://hackerone.com/reports/121696
    - https://hackerone.com/reports/202425
    - https://hackerone.com/reports/128777

- [ ] Check for Logic flaws leading to bypass 
    - https://hackerone.com/reports/178293
    - https://hackerone.com/reports/7369
    - https://hackerone.com/reports/418767
- [ ] Check if OTP code does not expire - https://hackerone.com/reports/67660
- [ ] Check that active sessions are terminated when MFA is enabled - https://hackerone.com/reports/667739
- [ ] Repeat the login process numerous times with various malformed requests:
   - a. Try performing the login steps in a different sequence.
   - b. Try proceeding directly to any given stage and continuing from there.
   - c. Try skipping each stage and continuing with the next.
   - d. Use your imagination to think of other ways to access the different stages that the developers may not have anticipated.
- [ ] If any data is submitted more than once, try submitting a different value at different stages, and see whether the login is still successful. It may be that some of the submissions are superfluous and are not actually processed by the application. It might be that the data is validated at one stage and then trusted subsequently. In this instance, try to provide the credentials of one user at one stage, and then switch at the next to actu- ally authenticate as a different user. It might be that the same piece of data is validated at more than one stage, but against different checks. In this instance, try to provide (for example) the username and password of one user at the first stage, and the username and PIN of a different user at the second stage.
- [ ] Pay close attention to any data being transmitted via the client that was not directly entered by the user. The application may use this data to store information about the state of the login progress, and the application may trust it when it is submitted back to the server. For example, if the request for stage three includes the parameter stage2complete=true, it may be possible to advance straight to stage three by setting this value. Try to modify the values being submitted, and determine whether this enables you to advance or skip stages.

# Test for Authentication bypass
- [ ] If /admin is blocked /Admin, /aDmin, /ADMIN
    - http://c0rni3sm.blogspot.se/2017/08/accidentally-typo-to-bypass.html
