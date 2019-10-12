# Brain

The brain plugin take care of evolving your planets.  
Each planets can be configured individually.  
Three modes are available **AI**/**Queue**/**None**.

If you decide to pick the buildings by yourself, you should go to the Empire tab,
select a planet, pick a building, and press "Add to queue".

## AI
When AI is selected, the brain plugin will use our algorithm to evolve your planet automatically.  
It basically:
- build what you inserted in the queue if there is anything.
- otherwise, build the cheapest mine available.
- if then energy become negative, it will build solar plant up to level 19 (then switch to solar satellite)
- if you don't have enough storage, it will build more storage.

## Queue
When queue is selected, the brain plugin will build what you inserted in the planet queue.

## None
When none is selected, the brain will just ignore this planet.
