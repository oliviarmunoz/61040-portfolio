# Extending Design Questions

- Design a couple of additional concepts to realize this extension, and write them out in full (but including only the essential actions and state). It should not be necessary to make any changes to the existing concepts.

```
    concept CountAnalytics [ShortUrl]
    purpose track how many times a short URL is accessed; only the owner of a URL can see this value
    principle each time a short URL is accessed, its count increases
    state
        a set of ShortUrls with
            an accesses int
    actions
        increment (shortUrl: ShortUrl)
            requires shortUrl exists and is not expired
            effect increases the count associated with shortUrl by 1

        getCount (shortUrl: ShortUrl): (count: int)
            requires shortUrl exists and is not expired
            effect returns the number of times shortUrl has been accessed

```

```
    concept UrlOwner [ShortUrl, User]
    purpose provide a notion of ownership over a short URL
    principle every short URL is owned by one user
    state
        a set of ShortUrls with
            an owner User
    actions
        assignOwner (shortUrl: ShortUrl, user: User)
            requires shortUrl exists and is not expired
            effect associates that user as the owner of that shortUrl

        checkOwner (shortUrl: ShortUrl, user: User): (owner: boolean)
            requires shortUrl exists and is not expired
            effect returns True if user is the owner of shortUrl
```

- Specify three essential synchronizations with your new concepts: one that happens when shortenings are created; one when shortenings are translated to targets; and one when a user examines analytics.

```
    sync create
        when
            Request.shortenUrl(user: User)
            UrlShortening.register(): (shortUrl)
        then
            UrlOwner.assignOwner(shortUrl, user)
```

```
    sync translateShortening
        when
            UrlShortening.lookup(shortUrl)
        then
            CountAnalytics.increment(shortUrl)
```

```
    sync getAnalytics
        when
            Request.getAnalytics (shortUrl, user)
            UrlOwner.checkOwner(shortUrl, user): (owner)
        where
            owner is True
        then
            CountAnalytics.getCount(shortUrl): (count)

```

- As a way to assess the modularity of your solution, consider each of the following feature requests, to be included along with analytics. For each one, outline how it might be realized (eg, by changing or adding a concept or a sync), or argue that the feature would be undesirable and should not be included:

  - Allowing users to choose their own short URLs;

    - This could be realized by changing the `register` action in `UrlShortening` to include an optional personalized `shortUrl`. If this argument is provided, then you can skip the nonce generation if it is still unique in the context.

  - Using the “word as nonce” strategy to generate more memorable short URLs;

    - This could be realized by modifying how the generate action creates nonces, which could be done as a separate concept that generates memorable suffixes rather than random strings. You would then change the sync to use this nonce generation instead of the original.

  - Including the target URL in analytics, so that lookups of different short URLs can be grouped together when they refer to the same target URL;

    - This could be realized by extending `CountAnalytics` to also track accesses related to the `targetUrl`. Then, the translation sync would update both the short URL’s count and the aggregated count for its target URL.

  - Generate short URLs that are not easily guessed;

    - This could be realized by strengthening the `generate` action in `NonceGeneration` to use stronger randomness when creating the nonces. All of the other syncs would be the same.

  - Supporting reporting of analytics to creators of short URLs who have not registered as user.
    - This is not desirable because it introduces many safety/security risks and defeats the purpose of the UrlOwner concept.
