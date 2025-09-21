# Synchronization Questions

- In the first sync (called generate), the Request.shortenUrl action in the when clause includes the shortUrlBase argument but not the targetUrl argument. In the second sync (called register) both appear. Why is this?

  - In the first sync, we are only concerned with creating a unique suffix, so we don't actually need to know what the target URL is. The nonce can be generated before we know what it will map to. In the second sync, we need to know what is being mapped where, so we need to know both the base and target URL to be able to associate the nonce with the target URL.

- The convention that allows names to be omitted when argument or result names are the same as their variable names is convenient and allows for a more succinct specification. Why isn’t this convention used in every case?

  - The convention is used to keep the specification concise, but there are cases where this could cause some confusion or ambiguity (like question 1). If it is ambiguous as to what is being referred to, this convention should not be used to ensure that there is no confusion. Additionally, if arguments are not explicitly included in the function/sync, the order in which they appear could also cause confusion, which is why it isn't always used.

- Why is the request action included in the first two syncs but not the third one?

  - In the first two syncs, the user is making a request to shorten the URL, and the syncs are triggered directly by the user's action. The third sync isn’t triggered by the user, but rather as a consequence of the `register` action, so there is no request being made.

- Suppose the application did not support alternative domain names, and always used a fixed one such as “bit.ly.” How would you change the synchronizations to implement this?

  - You wouldn't have to pass in a context to the generate action (or you could just use a static parameter like bit.ly). The updated sync would look like...

  ```
   sync generate
    when Request.shortenUrl (shortUrlBase)
    then NonceGeneration.generate ()
  ```

  and the rest of the syncs would be the same.

- These synchronizations are not complete; in particular, they don’t do anything when a resource expires. Write a sync for this case, using appropriate actions from the ExpiringResource and URLShortening concepts.

  ```
   sync expire
    when ExpiringResource.expireResource(): (resource: shortURL)
    then URLShortening.delete(resource)
  ```
