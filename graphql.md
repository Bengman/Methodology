# GraphQL

- [ ] Check if Introspection is enabled. If so determine if it is a vulnerability or by design. Is there any dev/staging environments exposeed that has introspection enabled?
- [ ] Extract all available queries and mutations.
- [ ] Test all these for authentication/authorization and injection issues.

## Authentication / Authorization issues (Information disclosure)

- [ ] Pull out nested queries and send them as root objects instead.
  - https://www.youtube.com/watch?v=NPDp7GHmMa0 (~33 min)
- [ ] Check if any queries mark "isDepricated still return data.
- [ ] Check if we can send mutations without authorization.
  
- [ ] Check if debugging is enabled by appending `&debug=1` as a parameter.
- [ ] Look for verbose errors or Stack Traces.

## Injections
- [ ] Check for SQL-Injection.
- [ ] Check for NoSQL Injections.

## Dos

- [ ] Check for issues with nested malicious queries.
 - https://blog.apollographql.com/securing-your-graphql-api-from-malicious-queries-16130a324a6b
