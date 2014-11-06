
DecisionFlex

Simple to use, powerful human-like decisions.

---

Your game has a guard with three states: patrol, search, and rest. You've already got a Guard script that performs the states, and DecisionFlex will help him decide when to change between them. 

Add a DecisionMaker script to the guard object.

Change your guard script to derive from SingleConsiderationContextFactory instead of MonoBehaviour. Add a new function, SingleContext(). It only needs a few lines:

    var guardContext = new ConsiderationContextDictionary();
    guardContext.SetContext("timeSincePlayerSpotted", GetTimeSincePlayerLastSpotted());
    guardContext.SetContext("distanceToPlayer", GetDistanceToPlayer());
    guardContext.SetContext("timeSinceRest", GetTimeSinceRest());
    return guardContext;

The DecisionMaker script knows to call this function on Guard whenever it needs to understand the guard's view of the world. Later we'll tell it how to prioritise that information.

Add child gameobjects to the decision object for each action we might do: StartPatrol, StartSearch, StartRest.

For each child action object, add a script that derives from Action, then implement Perform() to do the action:

    GetComponentInParent<Guard>().ChangeToSearch();

Searches should start when the player was recently spotted nearby. Add two gameobjects to the StartSearch object - PlayerRecentlySpotted and PlayerNearby.

For PlayerRecentlySpotted, add a FloatConsideration script. Set the contextName to "timeSincePlayerSpotted" and open the animation curve window. Give it a curve something like this:

    [CURVE]

This curve takes timeSincePlayerSpotted, on the x axis, and converts it into a value between 0 and 1. When time is very low, the value is high, because it's important to search when a player was recently spotted. As time passes, the value drops as it becomes less important to search.

Do the same thing for the PlayerNearby object, connecting the consideration to "distanceToPlayer" and setting the graph to something like this:

   [CURVE]

This curve says as we're closer to the player, it's more important for us to search. If we're far away, it's less important.

When DecisionFlex runs, all the consideration objects of StartSearch will generate their values, and these values will be multiplied together. This will be the score for the StartSearch decision.

We'll add two considerations to the StartRest action: NotRestedRecently and LowPriority. The first hooks up to timeSinceRest and looks like this:

   [CURVE]

Where it becomes more and more important to rest as the time since resting gets longer.

The LowPriority object has a ConsiderationScalar script. This needs a single float between 0 and 1, and no graph. It always returns that value when asked. We set this low, say 0.3.

This way, even if NotRestedRecently returns a very large value, when it's multiplied with LowPriority the final score will never exceed 0.2. The score for StartRest will always be lower than even a moderately-scored StartSearch, so we will never be in the situation where a guard right next to the player decides to rest, just because it's been a long time. 

The StartPatrol action is our default, if neither of the other actions are interesting. That will have a Fallback object, with another ConsiderationScalar script, with an even lower value than StartRest, eg 0.1. The score for StartPatrol will always be this value, since there are no other considerations.

Next we'll add a SelectHighestScore script to the top-level guard object. This tells the DecisionMaker to always go with the action with the biggest score. We want the guard to be predictable.

Finally, we have to decide how often to decide to change state. Let's change at most once a second. Add a MakeDecisionAtRegularIntervals script to the top-level Guard object, and set it's tickEvery property to 1.

Every time DecisionMaker runs, it will get a fresh context dictionary from Guard, then evaluate the scores of each action.

Let's say a player has been recently spotted, giving a value of 0.8, but it's far away from the guard, giving 0.4. This gives a StartSearch score of 0.8 x 0.4 = 0.32.

It's been a little bit since the guard rested, giving 0.6, but the LowPriority scales it down by 0.2 giving a final score of 0.12.

Finally the patrol gives the score it always gives, 0.1.

SelectHighestScore finds that StartSearch is the highest score, and so calls Perform() on its action. This calls guard.ChangeToSearch() and the guard begins his new state.

Despite the simplicity of this setup, it can express emergent subtleties that would be hard to capture if coded by hand. For instance, with DecisionFlex, well-rested guards far from the player may still help out with the search. 

A hand-coded approach would also be much harder to iterate and tweak. At any time, from the unity editor and without touching script, you could change the behaviour of the guard. You can adjust the priorities as we've set them up, add more considerations, or add more actions. You can change how often decisions are made, and how action scores are evaluated. 

And this approach works for many other situations as well:
- Sims-like simulated human, deciding to drink, work, or exercise.
- soldiers selecting the most dangerous target to attack, and with what weapon
- grunts deciding if they should go pick up some health
- modeling player behaviour to one of several archetypes






