# Exercise 2

### Extending a familiar concept

_Questions_

- Complete the definition of the concept state.

  ```
  state

      a set of Users with
          a username Username
      a set of Usernames with
          a username String
          a password String
  ```

- Write a requires/effects specification for each of the two actions. (Hints: The register action creates and returns a new user. The authenticate action is primarily a guard, and doesn’t mutate the state.)

  ```
  actions

  register (username: String, password: String): (user: User)
      requires: a valid and unique username (from the currently existing usernames)
      effects: creates and returns a new User with the username and password. Has no effect if the user is already registered.

  authenticate (username: Username): (user: User)
      requires: a valid Username (username and password combination that exists)
      effects: returns the User with the associated Username. Has no effect if the username does not exist or if it doesn't match with the password.
  ```

- What essential invariant must hold on the state? How is it preserved?

  - The usernames are all unique and have exactly one associated password. It is preserved by the `requires` clause for register.

- One widely used extension of this concept requires that registration be confirmed by email. Extend the concept to include this functionality. (Hints: you should add (1) an extra result variable to the register action that returns a secret token that (via a sync) will be emailed to the user; (2) a new confirm action that takes a username and a secret token and completes the registration; (3) whatever additional state is needed to support this behavior.)

  ```
  state

      a set of Users with
          a username Username
          a verified String
      a set of Usernames with
          a username String
          a password String
          a token String
  ```

  ```
  actions

  register (username: String, password: String): (user: User, token: String)
    requires: a valid and unique username
    effects: creates and returns a new User with the username and password. Emails the token to the user for verification. Has no effect if the user is already registered.

  confirm (username: Username, token: String):
    requires: an existing Username that has the token
    effects: verifies the User with that username by setting the `verified` flag to true. Has no effect if the token is not associated with that username or if the username is invalid.
  ```
