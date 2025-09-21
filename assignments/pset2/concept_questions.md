# Concept Questions

- The `NonceGeneration` concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what will a context end up being in the URL shortening app?

  - A context represents a scope for uniqueness. They keep track of which nonces have already been generated so they aren't used again within that scope.
  - In the URL shortening app, the context corresponds to the `shortUrlBase`. By having this as the context, each base URL maintains its own set of used strings, allowing the same suffix to be reused under different bases.

- Why must the `NonceGeneration` store sets of used strings? One simple way to implement the NonceGeneration is to maintain a counter for each context and increment it every time the `generate` action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an abstraction function.)

  - `NonceGeneration` needs to have a way to keep track of previously used strings to ensure the uniqueness of newly generated values. This means that every string in the used set is excluded from future nonce generations.
  - The counter acts as the used string set by implicitly representing all smaller values (everything from `0` up to `counter - 1`), so that future nonce generations don’t reuse any of the strings these numbers map to. This does not keep track of each string individually, but encodes the entire used set in the value of the counter.

  `AF(counter) = { encode(i) | 0 ≤ i < counter }, where encode is the scheme that maps integers to strings`

- One option for nonce generation is to use common dictionary words (in the style of yellkey.com, for example) resulting in more easily remembered shortenings. What is one advantage and one disadvantage of this scheme, both from the perspective of the user? How would you modify the NonceGeneration concept to realize this idea?

  - One advantage of using shortenings with common dictionary words is that it makes the shortenings more memorable (one of the purposes of the `UrlShortening` concept). This means that users are less likely to mistype/misremember them and get redirected somewhere else, improving the overall user experience by having fewer misdirects.
  - One of the disadvantages is that because dictionary words are more predictable, malicious users may be able to guess shortenings, which could be a privacy concern for users sharing links to sensitive information.
  - You could modify `NonceGeneration` by extending the state to include a dictionary of available words for each context rather than randomly choosing letters/numbers to generate a memorable nonce. The `generate` action would then select an unused word from this dictionary, mark it as used, and return it.

  [Next: Synchronization Questions](sync_questions.md)
