# Extending Design Questions

- Design a couple of additional concepts to realize this extension, and write them out in full (but including only the essential actions and state). It should not be necessary to make any changes to the existing concepts.

```
    concept CountAnalytics [ShortUrl]
    purpose track how many times a short URL is accessed
    principle each time a short URL is accessed, its count increases
    state
        a set of ShortUrls with
            an accesses Number
    actions
        increment (shortUrl: ShortUrl)
            requires a valid shortUrl (not expired)
            effect increases the count associated with shortUrl by 1
        getCount (user: User, shortUrl: ShortUrl): (count: Number)
            requires user is the owner of the shortUrl
            effect returns the number of times shortUrl has been accessed

```

```
    concept UrlOwner [ShortUrl, User]
    purpose provide scoped viewing for analytics only to the user that registered the short URL
    principle every short URL is owned by a user, and they are the only one able to view its analytics
    state
        a set of ShortUrls with
            an owner User
    actions
        assignOwner (shortUrl: ShortUrl, user: User)
            requires shortUrl is registered and not expired
            effect associates that user as the owner of that shortUrl


```

- Specify three essential synchronizations with your new concepts: one that happens when shortenings are created; one when shortenings are translated to targets; and one when a user examines analytics.

```
    sync create
        when
            Request.UrlOwner(user: User)
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
        then
            CountAnalytics.getCount(user, shortUrl): (count)

```

- As a way to assess the modularity of your solution, consider each of the following feature requests, to be included along with analytics. For each one, outline how it might be realized (eg, by changing or adding a concept or a sync), or argue that the feature would be undesirable and should not be included:

  - Allowing users to choose their own short URLs;
    - This could be realized by changing the register action in UrlShortening to include an optional personalized shortUrl (and ensuring that it is still unique).
  - Using the “word as nonce” strategy to generate more memorable short URLs;
    - This could be realized by modifying how the generate action creates nonces, which could be done as a separate concept that generates memorable suffixes rather than random strings.
  - Including the target URL in analytics, so that lookups of different short URLs can be grouped together when they refer to the same target URL;
    - This could be realized by extending CountAnalytics to also track accesses related to the targetUrl. Then, a sync from UrlShortening.lookup would update both the short URL’s count and the aggregated count for its target URL.
  - Generate short URLs that are not easily guessed;
    - This should not be included because "what is not easily guessed" is vague and introduces more limits on valid URLs that are not necessarily useful. If the goal is for stronger security, it can be handled by modifying the generate action of NonceGeneration to be more random, but what is more random is subjective.
  - Supporting reporting of analytics to creators of short URLs who have not registered as user.
    - This is not desirable because it introduces many safety/security risks and defeats the purpose of the UrlOwner concept.
    
