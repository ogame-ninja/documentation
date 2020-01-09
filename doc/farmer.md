# Farmer

Scan the galaxy range provided and spy inactive players as we go.<br />
The bot will attack only safe planets. Planets for which we have information and have no ships & defences.<br />

## New session

##### [Origin]

This option allows you to choose the source planet where all your espionage and farming missions are launched from and come back to.

##### [Range]

By default this is set to 50 solar systems left and right of the specified planet.
You may increase or decrease the range of the planets you want to scan, Note that If you do increase
the range it will increase the time it takes the NJA bot to scan and attack so many planets.
That of course depends on the number of inactive planets that are included in this range.

##### [Nb probes]

This is the number of probes that the bot will send each time an espionage mission is started in order to get
a detailed report of the resources, fleet and defenses on the inactive planet. Make sure that the number
you use is sufficient so that you will get ALL the information from the planet. If you are only able to see
the number of resources in a report, but cannot see the fleet and/or defenses, the Bot will not attack,
since it does not know the safe amount of ships to send.

##### [Use pathfinders]

The bot will attack using pathfinders to save time. If not enough pathfinders are available, it will fallback on small & large cargos.

##### [Additional cargo]

Additional cargo to send when attacking a target.  

##### [Min. resources to attack]

Minimum amount of resources a planet must have to be attacked.

##### [Min. defenses to ignore]

If the planet has the minimum defenses points, it will automatically be added to the ignore list for future farming sessions.  
If the "min. defenses to ignore" is set to 0, no planet will be added.

##### [Min. player rank]
If the value is set to 1000, only the top 1 to 1000 ranked (inactive) players will be considered for attacks.  
If set to 0, all players are valid targets.

##### [Minimum level of storage]
If set (greater than 0), a planet must have at least the minimum level of storage to be considered as a target.

##### [Ignore targets with activity]

If set, a planet must have no recent activity to be considered as a target.

##### [Fast attacking]

This is another option that will force the bot to start attacking sooner than the end of the scanning range.
When you check this option the bot will send an attack as soon as a planet goes into "spied" state.

##### [Attack from nearest planet]
When selected, the bot will send the attack from the moon OR planet that is closest to the target.  
The moon is preferred to the planet if they have the same distance.   

##### [Attack from nearest moon]
Same as "Attack from nearest planet", but only moons are considered and the farming origin if it is a planet.  

##### [Delete combat reports]
Either or not you want the bot to delete the combat reports.

##### [Priority calculation]
This option is only useful when "fast-attack" is **not** selected.  
The bot will prioritize targets with the higher amount of resources using these factors.