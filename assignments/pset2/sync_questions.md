# Synchronization Questions

- In the first sync (called generate), the `Request.shortenUrl` action in the when clause includes the `shortUrlBase` argument but not the `targetUrl` argument. In the second sync (called `register`) both appear. Why is this?

  - In the first sync, we are only concerned with creating a unique suffix, so we don't actually need to know what the target URL is. We only need to know `shortUrlBase` because the nonce just needs to be unique in this context, so `targetUrl` is irrelevant at this point. However, in the second sync, we need to know what is being mapped where, so we need to know both the base and target URL to be able to associate the nonce with it.

- The convention that allows names to be omitted when argument or result names are the same as their variable names is convenient and allows for a more succinct specification. Why isn’t this convention used in every case?

  - This convention is only safe when the argument (or result) name and the bound variable name are identical, so there is no ambiguity about what is being referred to. It isn’t used in every case because sometimes the variable must be called something different from the argument name. In those cases, writing both explicitly (e.g.`argName: varName`) avoids confusion. Even though this convention might make the spec more concise, explicit names are required when omitting it would come at the cost of correctness.

- Why is the `request` action included in the first two syncs but not the third one?

  - In the first two syncs, the user is making a request to shorten the URL, so these syncs are triggered directly by the user's action. The third sync isn’t triggered by the user, but rather internally after the `register` action. Setting the expiry date is automatic and not explicitly requested by the user, so there is no `request` action in the third sync.

- Suppose the application did not support alternative domain names, and always used a fixed one such as `bit.ly.` How would you change the synchronizations to implement this?

  - You wouldn't have to pass in a context to the `generate` action (or you could just use a static parameter). By removing it completely, this would require the `register` action to hard-code in the domain name, but it would still be functionally the same. The updated syncs would look like...

  ```
    sync generate
    when
        Request.shortenUrl ()
    then
        NonceGeneration.generate ()

    sync register
    when
        Request.shortenUrl (targetUrl)
        NonceGeneration.generate (): (nonce)
    then
        UrlShortening.register (shortUrlSuffix: nonce, targetUrl)

    sync setExpiry
    when
        UrlShortening.register (): (shortUrl)
    then
        ExpiringResource.setExpiry (resource: shortUrl, seconds: 3600)

  ```

- These synchronizations are not complete; in particular, they don’t do anything when a resource expires. Write a sync for this case, using appropriate actions from the ExpiringResource and URLShortening concepts.

  ```
    sync expire
    when
        ExpiringResource.expireResource(): (resource: String)
    then
        URLShortening.delete(resource: String)
  ```

[Next: Extending Design](extending_design.md)
