# Concept Design

## Concept Specifications

```late
    concept UserPreferences [User]
    purpose track and update user preferences and dietary restrictions
    principle each user has a unique profile to store their preferences
    state
        a set of Users with
            a set of preferences set(string)

    actions
        createProfile (userID: number, preferences: set(string)): (user: User)
            requires: userID is not already registered
            effects: creates and returns a new user with the given preferences

        addPreference (userID: number, preference: string): (preferences: set(string))
            requires: user exists
            effects: add the preference to those associated with the user and return new preferences

        removePreference (userID: number, preference: string): (preferences: set(string))
            requires: user exists and preference from those associated with the user
            effects: remove the preference from those associated with the user and return new preferences

        getPreferences (user: User): (preferences: set(string))
            requires: user exists
            effects: returns the preferences associated with the user

```

```late
    concept RestaurantMenu [Dish]
    purpose store and manage menu items for each restaurant
    principle each restaurant has its own menu and maintains an up-to-date set of dishes
    state
        a set of RestaurantMenus with
            a restaurantID number,
            a set of dishes set(Dish)

    actions
        registerMenu(restaurantID: number, dishes: set(Dish)):
            requires: restaurantID is not already registered
            effects: creates a RestaurantMenu for the restaurant with the given dishes

        addDish (restaurantID: number, dish: Dish): (dishes: set(Dish))
            requires: restaurant exists, dish not in restaurant’s menu
            effects: adds dish to restaurant’s menu and returns updated menu

        deleteDish (restaurantID: number, dish: Dish): (dishes: set(Dish))
            requires: restaurant exists, dish in restaurant’s menu
            effects: removes dish from restaurant’s menu and returns updated menu

        getMenu (restaurantID: number): (dishes: set(Dish))
            requires: restaurant exists
            effects: returns the menu for that restaurant

```

```late
    concept Recommend [Item, Policy]
    purpose select an element from a set according to a policy
    principle based on the specified policy, return a single element from a set
    state
        a set of Recommenders with
            a policy Policy
            a set of candidates set(Item)
    actions
        recommend (candidates: set(Item), policy: Policy): (item: Item)
            requires: candidates is non empty
            effects: returns one element from candidates according to the given policy

        updatePolicy (oldPolicy: Policy, newPolicy: Policy)
            requires: oldPolicy exists, oldPolicy is not the same as newPolicy
            effects: updates the policy from oldPolicy to newPolicy

        updateCandidates (oldCandidates: set(Item), newCandidates: set(Item))
            requires: newCandidates is non empty, oldCandidates is not the same as newCandidates
            effects: updates the list of candidates from oldCandidates to newCandidates

```

## Syncs

```late

    sync recommendation
    when
        Request.getRecommendation(user, restaurant)
        UserPreferences.getPreferences(user): (preferences: set(string))
        RestaurantMenu.getMenu(restaurant): (dishes: set(Dish))
    then
        Recommend.recommend (dishes, preferences)

```

```late

    sync updatePreferences (add)
    when
        UserPreferences.getPreferences(userID:number): (oldPreferences: string)
        UserPreferences.addPreference(userID: number, preference: string): (newPreferences: string)
    then
        Recommend.updatePolicy (oldPreferences, newPreferences)

```

```late

    sync updatePreferences (remove)
    when
        UserPreferences.getPreferences(userID:number): (oldPreferences: string)
        UserPreferences.removePreference(userID: number, preference: string): (newPreferences: string)
    then
        Recommend.updatePolicy (oldPreferences, newPreferences)

```

```late

    sync updateCandidates (add)
    when
        RestaurantMenu.getMenu(restaurantID:number): (oldCandidates: string)
        RestaurantMenu.addDish(restaurantID:number): (newCandidates: string)
    then
        Recommend.updateCandidates (oldCandidates, newCandidates)

```

```late

   sync updateCandidates (remove)
    when
        RestaurantMenu.getMenu(restaurantID:number): (oldCandidates: string)
        RestaurantMenu.removeDish(restaurantID:number): (newCandidates: string)
    then
        Recommend.updateCandidates (oldCandidates, newCandidates)

```

## Concept Roles

UserPreferences manages each user's dietary/taste preferences, which provide the basis for personalization. RestaurantMenu represents the menu at each restaurant as a collection of dishes, which defines the pool of possible recommendations. Recommend provides a general mechanism for selecting one element from a candidate set according to a policy, allowing the app to recommend a specific dish for a user.

The generic parameters are instantiated as follows:

- In UserPreferences, the generic type User is bound to the app’s registered users.
- In RestaurantMenu, the generic type Dish is bound to menu items.
- In Recommend, Item is instantiated as Dish, while Policy represents a filtering/selection rule informed by user preferences

User preferences and a restaurant’s menu are combined to generate a candidate set of dishes, evaluated against a policy created by the user’s preferences. This is passed into Recommend to select a single dish. Updates to user preferences update the policy, while updates to menus update the candidate set, ensuring that recommendations always reflect current information.

[Next: UI Sketches](sketches.md)
