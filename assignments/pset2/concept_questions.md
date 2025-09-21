# Concept Questions

- The NonceGeneration concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what will a context end up being in the URL shortening app?

  - A context represents the collection of unique strings within a given scope. In the URL shortening app, the context corresponds to the base URL. This is useful because it means that the same suffix string can be reused across different base URLs without conflict, since each base URL maintains its own context for uniqueness.

- Why must the NonceGeneration store sets of used strings? One simple way to implement the NonceGeneration is to maintain a counter for each context and increment it every time the generate action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an abstraction function.)

  - The set of used strings acts similarly to the counter in that it guarantees that every new string generated is unique. The used string set keeps track of what has already been defined and doesn't allow new strings to use any of the ones in it. AF(usedString) --> The set of unusable strings

- One option for nonce generation is to use common dictionary words (in the style of yellkey.com, for example) resulting in more easily remembered shortenings. What is one advantage and one disadvantage of this scheme, both from the perspective of the user? How would you modify the NonceGeneration concept to realize this idea?

  - One advantage of using shortenings with common dictionary words is that it is easier for the user to remember (which is one of the purposes of UrlShortening) so they are less likely to make a typo and get redirected somewhere else. One of the disadvantages is that with several URLs, the set of valid shortenings decreases. In other words, the universe of possible shortenings is smaller. This would mean that the user would expect more rare/infrequently used words as the shortening as more URLs are made, which is harder to remember. 
