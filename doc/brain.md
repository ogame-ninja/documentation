# Brain

The brain plugin take care of evolving your planets.  
Each planets can be configured individually.  
Three modes are available **AI**/**Queue**/**None**.

If you decide to pick the buildings by yourself, you should go to the Empire tab,
select a planet, pick a building, and press "Add to queue".

##### AI
When AI is selected, the brain plugin will use our algorithm to evolve your planet automatically.  
It basically:
- build what you inserted in the queue if there is anything.
- otherwise, build the cheapest mine available.
- if the energy become negative, it will build solar plant (or other energy producers according to your settings)
- if you don't have enough storage, it will build more storage.

For moons:
- If only one field is available, it will build a Lunar Base.
- Build Robotic Factory up to level 8.
- Build Jump Gate if available (up to level 1).
- Otherwise, build Sensor Phalanx up to level 6.

##### Queue
When queue is selected, the brain plugin will build what you inserted in the planet queue.

##### None
When none is selected, the brain will just ignore this planet.

##### Importer?
If a planet/moon is tagged as importer, it will import resources from exporters, to build the next goal more rapidly.  

##### Exporter?
If a planet/moon is tagged as exporter, it will make it's resources available to other planets.

##### Export within same galaxy only
If enabled, the bot will only import resources from exporters that are in the same galaxy.

##### Prevent importing if come back during sleep time
If enabled, the bot will prevent importing resources if the cargo fleet was to come back during the sleep time. 

## AI configs

##### Max metal mine level to build
In AI mode, the bot will build metal mine up this level.

##### Max crystal mine level to build
In AI mode, the bot will build crystal mine up this level.

##### Max deut synth level to build
In AI mode, the bot will build deuterium synthesizer up this level.

##### Max solar plant level to build
In AI mode, the bot will build solar plant, when energy becomes negative, up this level.

##### Build fusion reactor up to
If enabled, the bot will build fusion reactor up to the selected level, once the solar plant are done.

##### Build solar satellites
If enabled, the bot will keep the energy positive, by building solar satellites, once the solar plants/fusion reactors are build.