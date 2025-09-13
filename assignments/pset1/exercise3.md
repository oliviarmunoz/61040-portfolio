# Exercise 3

### Comparing concepts

```
concept PersonalAccessToken [User]
  purpose
    allow limited access to known users without using the account password

  principle
    after a user registers with a username and password,
    they can create a personal access token (PAT).
    Then they can authenticate with that same username and the token string,
    and be treated each time as the same user,
    but only with the access permitted by the token’s scopes.

  state
    a set of Users with
      a username String
      a password String
      a set of Tokens

    a set of Tokens with
      a token String
      an expiration DateTime
      a set of scopes String

  actions
    createToken (user: User, scope: String, expiration: DateTime): (token: Token)
      requires: a valid existing user
      effects: create a new token for the user with the given scope that expires in expiration time

    authenticate (user: User, tokenString: String)
      requires: user has a valid token with matching token string
      effects: grant access with the permissions defined by the token's scope

    removeToken (user: User, tokenString: String)
      requires: user has a valid token with this token string
      effects: removes the token from the user's set of Tokens

```

The main difference between the PersonalAccessToken and the PasswordAuthentication is in their use case. Passwords grant unrestricted access to the user's account, while a personal access token (PAT) grants limited access to a specific part of the account. In addition, PATs are typically more secure because of their time limit, so they cannot be used after a certain period of time. Finally, PATs are mostly used in the command line while passwords are typically used on some sort of web interface.

I would change the GitHub documentation to make these differences more explicit. Upon first glance, the differences between a PAT and a password are hard to understand and it's not immediately obvious when you would use one over the other. The first change to make would be to address these differences at the beginning of the document and then follow it with how to create/delete/use it.
