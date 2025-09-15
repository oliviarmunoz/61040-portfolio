# Exercise 2

## Extending a familiar concept

### Questions

- Complete the definition of the concept state.

  ```
  state

      a set of Users with
          a username String
          a password String
  ```

- Write a requires/effects specification for each of the two actions. (Hints: The register action creates and returns a new user. The authenticate action is primarily a guard, and doesn’t mutate the state.)

  ```
  actions

  register (username: String, password: String): (user: User)
      requires: the username is unique from the currently existing usernames and only contains English characters or numbers.
      effects: creates and returns a new User that is associated with the username and password.

  authenticate (username: String, password: String): (user: User)
      requires: a valid username and password combination that exists already
      effects: returns the User with the associated username. Has no effect if the username does not exist or if it doesn't match with the password.
  ```

- What essential invariant must hold on the state? How is it preserved?

  - The usernames are all unique and have exactly one password. It is preserved by the `requires` clause for register.

- One widely used extension of this concept requires that registration be confirmed by email. Extend the concept to include this functionality. (Hints: you should add (1) an extra result variable to the register action that returns a secret token that (via a sync) will be emailed to the user; (2) a new confirm action that takes a username and a secret token and completes the registration; (3) whatever additional state is needed to support this behavior.)

  ```
  state

      a set of Users with
          a username Username
          a password String
          a verified Flag
          a secretToken String
  ```

  ```
  actions

  register (username: String, password: String): (user: User, secretToken: String)
    requires: the username is unique from the currently existing usernames and only contains English characters or numbers.
    effects: creates and returns a new User that is associated with the username and password. Also returns a unique secretToken, which is emailed to the user for verification. Sets the verified Flag for the new User to `False`.

  confirm (username: String, secretToken: String):
    requires: an existing User with the username that is associated with the secretToken
    effects: verifies the User with that username by setting the `verified` Flag to `True`. Has no effect if the token is not associated with that username or if the username is invalid.
  ```

  [Link to Exercise 3](exercise3.md)
