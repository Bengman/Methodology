# Understand the Mechanism

- [ ] Analyze the mechanism used to manage sessions and state. Which cookies/token hold the state of the user.
- [ ] Log in as several different users at different times, and record the tokens received from the server. If self-registration is available and you can choose your username, log in with a series of similar usernames that have small variations, such as A, AA, AAA, AAAA, AAAB, AAAC, AABA, and so on. If other user-specifi c data is submitted at the login or is stored in user profiles (such as an e-mail address), perform a similar exercise to modify that data systematically and capture the resulting tokens. 
- [ ] Analyze the tokens you receive for any correlations that appear to be related to the username and other user controllable data.

- [ ] Test Tokens for Predictability, Generate and capture a large number of session tokens in quick succession. Attempt to identify any patterns within your sample of tokens.


# Test Session Termination

- [ ] Wait for some time and check for any session timeouts 
- [ ] Log out of the application and try to reuse the old session.
- [ ] Change password of the user and check that other concurrent sessions are invalidated.
- [ ] Perform a password recovery and check that other concurrent sessions are invalidated.
- [ ] Try terminating the session at the same time as performing some other functionality that is resource heavy.
  - https://hackerone.com/reports/340191
- [ ] Check that a users current session is terminated if that user gets his/hers permissions lowered.
  - https://hackerone.com/reports/794431

# Check for Session Fixation

- [ ] If the application issues session tokens to unauthenticated users, obtain a token and perform a login. If the application does not issue a fresh token following a successful login, it is vulnerable to session fi xation.
- [ ] Even if the application does not issue session tokens to unauthenticated users, obtain a token by logging in, and then return to the login page. If the application is willing to return this page even though you are already authenticated, submit another login as a different user using the same token. If the application does not issue a fresh token after the second login, it is vulnerable to session fixation.



# Test for CSRF

- [ ] Find an application function that can be used to perform some sensitive action on behalf of an unwitting user, that relies solely on cookies
for tracking user sessions, and that employs request parameters that an
attacker can fully determine in advance.
- [ ] Create an HTML page that issues the desired request without any user
interaction. For GET requests, you can place an \<img\> tag with the src
attribute set to the vulnerable URL. For POST requests, you can create a
form that contains hidden fields for all the relevant parameters required

## Bypassing protections

- [ ] Change POST-methods to GET. Some applications correctly validate the token when the request uses the POST method but skip the validation when the GET method is used.
- [ ] Remove the CSRF-token from the request. Some applications correctly validate the token when it is present but skip the validation if the token is omitted.
- [ ] Submit an empty CSRF-token.
- [ ] Change one character. Sometimes a site will only check the length of a CSRF token (silly I know..). Try change one character (so it's the same length) and see what happens.
- [ ] Try using the token from one account when logged in with another. Some applications do not validate that the token belongs to the same session as the user who is making the request. Instead, the application maintains a global pool of tokens that it has issued and accepts any token that appears in this pool.
- [ ] If the application is written in PHP, try setting empty brackets [] as the token value. 

### JSON / XML CSRF
- [ ] Check if the Content-Type is forced to be JSON or can it be changed to text/plain and used with parameter padding?
  - https://www.geekboy.ninja/blog/tag/json-csrf/
- [ ] Try using Flash to bypass CSRF-protections. 
  - https://hackerone.com/reports/44146
  - https://github.com/sp1d3r/swf_json_csrf
  - https://blog.appsecco.com/exploiting-csrf-on-json-endpoints-with-flash-and-redirects-681d4ad6b31b
  
 "Force content-Type to application/JSON and set SAMESITE cookie".

### Referer
Some sites use the Referer-header for CSRF protection. If so:
- [ ] https://www.yoursite.com/https://www.theirsite.com/ Some sites only check if it CONTAINS their website url, meaning we can just create a file/folder on our site to send the CSRF request from.
- [ ] Send a blank referer. Include the `<meta name="referrer" content="never">` tag in a CSRF poc.
- [ ] Submit a blank origin. This will only work on Firefox but if base64 encoded your form above and then submitted it inside an iframe, the Origin header is set to null, or sometimes not sent at all. Some sites will only check if the header is present ;)
 - `<iframe src=data:text/html;base64,PGh0bWw+CiAgICAgPGJvZHk+CiAgICAgICAgPGZvcm0gRU5DVFlQRT0idGV4dC9wbGFpbiIgYWN0aW9uPSJodHRwOi8vdnVsbnNpdGUuY29tL3NuaXAvc25pcHBldC5waHAiIG1ldGhvZD0icG9zdCI+IAogICAgICAgIDxpbnB1dCB0eXBlPSJoaWRkZW4iIG5hbWU9InsicGFyYW1zIjp7ImxpbWl0IjoyMCwiYW5kIjpmYWxzZSwiZmlsdGVycyI6W10sImV4Y2x1ZGVkX2NvbnRhY3RzIjpbXX0sImZpZWxkcyI6WyJGaXJzdCBOYW1lIiwiTGFzdCBOYW1lIiwiRW1haWwgQWRkcmVzcyIsIlRpdGxlIiwiTm90ZXMiLCJPcmdhbml6YXRpb24iLCJTdHJlZXQiLCJDaXR5IiwiU3RhdGUiLCJUYWdzIiwiWmlwIENvZGUiLCJQaG9uZSBOdW1iZXIiLCJHZW5kZXIiLCJFdmVudCBJRCIsIkV2ZW50IFRpdGxlIiwiVklQIiwiVHdpdHRlciBIYW5kbGUiLCJUd2l0dGVyIFVSTCIsIlR3aXR0ZXIgRm9sbG93ZXJzIiwiVHdpdHRlciBGb2xsb3dpbmciLCJGYWNlYm9vayBOYW1lIiwiRmFjZWJvb2sgVVJMIiwiRmFjZWJvb2sgRnJpZW5kcyIsIkluc3RhZ3JhbSBIYW5kbGUiLCJJbnN0YWdyYW0gVVJMIiwiSW5zdGFncmFtIEZvbGxvd2VycyIsIkluc3RhZ3JhbSBGb2xsb3dpbmciLCJXZWJzaXRlIiwiRGF0ZSBBZGRlZCIsIlVuc3Vic2NyaWJlZCJdLCJyZWNpcGllbnQiOiJteWVtYWlsKzIiIHZhbHVlPSdAZ21haWwuY29tJz4KICAgICAgICAgPGlucHV0IHR5cGU9InN1Ym1pdCIgdmFsdWU9InN1Ym1pdCI+IDwvZm9ybT4KICAgICA8L2JvZHk+CiAgPC9odG1sPg==>`
