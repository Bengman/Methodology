For every api call:

- [ ] Test for IDOR when an id is referenced.
- [ ] If we see an id parameter and we are not allowed to list other id's data, try to add another ID param.
```
/api/v1/users/profile?id=MYID -> HTTP 200 with my data
/api/v1/users/profile?id=ANOTHERUSERID -> HTTP 401
/api/v1/users/profile?id=MYID&id=ANOTHERUSERID -> HTTP 200 with my data
/api/v1/users/profile?id=[myId,victimId]
```
 - https://twitter.com/intigriti/status/1256183450622668802/photo/1
 
- [ ] Test other HTTP methods, GET, POST, PATCH, PUT, DELETE etc.
- [ ] Test for 'Mass assignments'. Find a GET-request that returns all endpoints and try to add these to other requests.
- [ ] Test older versions of the API, for example `/api/v3/login` -> `/api/v2/login`.
- [ ] Test the endpoint unauthenticated
