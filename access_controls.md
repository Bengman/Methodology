# Test access controls between different roles (vertical access control)

- [ ] To perform the most effective testing for access control vulnerabilities, you should ideally obtain a number of different accounts with different vertical and horizontal privileges.
- [ ] If the application enforces vertical privilege segregation, first use a powerful account to locate all the functionality it can access. Then use a less-privileged account and attempt to access each item of this functionality.



# Test access controls between organisations (lateral access control)

- [ ] If the application enforces horizontal privilege segregation, perform the equivalent test using two different accounts at the same privilege level, attempting to use one account to access data belonging to the other account. This typically involves replacing an identifier (such as a document ID) within a request to specify a resource belonging to the other user.
- [ ] Always verify if the IDOR action worked, even if the response is "403 Forbidden". "the funny part that the page response was “403 forbbiden” but the credit card was actually deleted from the account"
  - https://secgeek.net/twitter-vulnerability/


- [ ] If we see an id parameter and we are not allowed to list other id's data, try to add another ID param.
```
/api/v1/users/profile?id=MYID -> HTTP 200 with my data
/api/v1/users/profile?id=ANOTHERUSERID -> HTTP 401
/api/v1/users/profile?id=MYID&id=ANOTHERUSERID -> HTTP 200 with my data
/api/v1/users/profile?id=[myId,victimId]
```
 - https://twitter.com/intigriti/status/1256183450622668802/photo/1
