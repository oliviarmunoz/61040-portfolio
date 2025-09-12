# Exercise 1

### Reading a concept

_Questions_

- **Invariants**. What are two invariants of the state? (Hint: one is about aggregation/counts of items, and one relates requests and purchases). Say which one is more important and why; identify the action whose design is most affected by it, and say how it preserves it.

  - The count of each Item across all Purchases does not exceed the count in the Request, and each Purchase must be associated with an Item that currently exists in the Requests of a given Registry.

  - The first invariant seems to be more important because it ensures that users don't purchase more items than originally requested. `purchase` is the action most affected by this invariant and preserves it by requiring that the request for the item has at least count.

- **Fixing an action**. Can you identify an action that potentially breaks this important invariant, and say how this might happen? How might this problem be fixed?

  - `removeItem` could break this invariant because it removes a Request from the Registry. There could be Purchases without an associated Request, so there is no way of ensuring that the total count of items in the Purchases is less than or equal to the count that was in the original Request. This could be fixed by disallowing users from removing an item if any purchases have already been made for it.

- **Inferring behavior**. The operational principle describes the typical scenario in which the registry is opened and eventually closed. But a concept specification often allows other scenarios. By reading the specs of the concept actions, say whether a registry can be opened and closed repeatedly. What is a reason to allow this?

  - Yes, given the current spec, it is possible to allow a registry to be opened and closed repeatedly. There is no requirement in `create` or `open` and `close` to prevent this from happening since there is no "final state". One reason this could be useful is if someone cancels an event and later reschedules it.

- **Registry deletion**. There is no action to delete a registry. Would this matter in practice?

  - This could confuse users who want a confirmation that their registry is permanently closed. This could also clutter up the page with outdated/unused registries.

- **Queries**. What are two common queries likely to be executed against the concept state? (Hint: one is executed by a registry owner, and one by a giver of a gift.)

  - Getting the set of Requests for the Registry (who purchased what) and getting the Count of an Item from a Request (which items are still available for puchase).

- **Hiding purchases**. A common feature of gift registries is to allow the recipient to choose not to see purchases so that an element of surprise is retained. How would you augment the concept specification to support this?

  - Don't allow purchases to be visible if they do not want to see it. This could be a new flag to the Requests and only show the details of a purchase if this flag is set to true.

- **Generic types**. The User and Item types are specified as generic parameters. The Item type might be populated by SKU codes, for example. Explain why this is preferable to representing items with their names, descriptions, prices, etc.
  - By having these types be generic, the implementation is flexible and easier to change if needed. This concept is also only concerned with the registry system, so exact details of how to represent users and items do not need to be defined in this concept.
