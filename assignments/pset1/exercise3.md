# Exercise 3

## Comparing concepts

### Personal Access Tokens

```
concept PersonalAccessToken
  purpose
    allows a user to authenticate to a service (with limited/scoped permissions) without using their account password

  principle
    after a user registers with a username and password,
    they can create a personal access token (PAT), each with a defined scope of permissions and an expiration date;
    then, they can authenticate using their username and a token string instead of their password (if the token is valid);
    on successful authentication, the user is treated as themselves, but limited to the access permitted by the token’s scope;
    a token becomes invalid if it is revoked or after it expires.

  state
    a set of Users with
      a username String
      a password String
      a set of Tokens

    a set of Tokens with
      a tokenString String
      a scope String
      an expiration DateTime

  actions
    createToken (user: User, scope: String, expiration: DateTime): (token: Token)
      requires: an existing user. The scope must not define new privileges; only existing privileges can be assigned. Expiration must be in the future.
      effects: create a new Token for the user with an auto-generated tokenString with the given scope that expires in expiration time

    authenticate (user: User, tokenString: String)
      requires: the user exists and has a non-expired Token with the same tokenString associated with it
      effects: grant access to the user with the permissions defined by the token's scope

    removeToken (user: User, tokenString: String)
      requires: the user exists and has a non-expired Token with the same tokenString associated with it
      effects: removes the Token from the User's set of Tokens

```

The main difference between the PersonalAccessToken and PasswordAuthentication is in their use case. A password grants full, unrestricted access to the User’s account but a personal access token (PAT) grants limited (scoped) access to specific resources or operations within the account. In addition, PATs offer some security benefits. The first is that PATs become invalid after an expiration date, so users must keep them updated. Also, for each User, multiple tokens can coexist, each with different scopes and lifespans, so access can be more flexible.

The GitHub documentation could be improved by clearly differentiating PATs from passwords at the start of the article. Currently, the page describes PATs as “an alternative to using passwords” and advises readers to “treat them like passwords,” which may create confusion on when to use one over the other. I would suggest adding more information highlighting the differences between the two and then following that with details on how to implement/use PATs.

[Link to Exercise 4](exercise4.md)
