
DecisionFlex

Simple to use, powerful human-like decisions.

---

DecisionFlex can help model complex decisions, where multiple considerations must be weighed before chosing a final action.

It uses familiar unity tools to do this, including the gameobject hierarchy and animation curves.

It is easy to connect to your game.

It is suitable for situations like:
- Sims-like simulated human, deciding to drink, work, or exercise.
- soldiers selecting the most dangerous target to attack, and with what weapon
- grunts deciding if they should go pick up some health
- modeling player behaviour to one of several archetypes

---

detailed breakdown

Your game has a guard with three states: patrol, search, and rest. You've already got a Guard script that performs the states, and DecisionFlex will help him decide when to change between them.

To help DecisionFlex make a decision, you'll create a gameobject hierarchy representing the **actions** you could do and the **considerations** that inform those actions, driven by **context**.

**Actions** are simple scripts you write to enact the decision. Our guard will have BeginPatrol, BeginRest and BeginSearch actions.

**Context** is the view of the world from this decision. It's normally a bunch of values. For instance, our guard might care about how long it is since the player was last seen, how far away that sighting was from his position, and how long since he last rested.

**Considerations** live under actions. They allow you to compare apples to oranges! You set up considerations to tell DecisionFlex how much influence one value from the context has over an action.

Despite the simplicity of the setup, it can express emergent subtleties that would be hard to capture if coded by hand. For instance, with DecisionFlex, well-rested guards far from the player may still help out with the search. 

[ COMPLETED GUARD HIERARCHY ]

Let's say a player has been recently spotted, giving a value of 0.8, but it's far away from the guard, giving 0.4. This gives a BeginSearch score of 0.8 x 0.4 = 0.32.

It's been a little bit since the guard rested, giving 0.6, but the LowPriority scales it down by 0.2 giving a final score of 0.12.

Finally the patrol gives the score it always gives, 0.1.

BeginSearch is the highest-scoring action and the guard begins his new state.

A hand-coded approach would also be much harder to iterate and tweak. At any time, from the unity editor and without touching script, you could change the behaviour of the guard. You can adjust the priorities as we've set them up, add more considerations, or add more actions. You can change how often decisions are made, and how action scores are evaluated. 

And this approach works for many other situations as well:
- Sims-like simulated human, deciding to drink, work, or exercise.
- soldiers selecting the most dangerous target to attack, and with what weapon
- grunts deciding if they should go pick up some health
- modeling player behaviour to one of several archetypes