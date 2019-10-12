# Defender

##### [Email alert when attacked]<a name="email-alert"></a>

An email will be sent to you when you are attacked.
For this feature to work:  
- **(Self-host only)** you need to first <a href="/admin/settings">setup your SMTP settings</a> in the admin panel.  
- Set the "Email for notifications" in bot settings tab.  
- The defender checkbox must also be checked (Defender active).  

##### [Telegram alert when attacked]<a name="telegram-alert"></a>

An telegram message will be sent to you when you are attacked.  
For this feature to work:  
- **(Self-host only)** you need to first <a href="/admin/settings">setup your telegram bot api token</a> in the admin panel.  
- Set the "Telegram chat id for notifications" in the bot settings tab.  
- The defender checkbox must also be checked (Defender active).   

##### [Repatriate resources]<a name="repatriate-resources"></a>

If you active this feature, all the planets are going to send their resources to the selected planet/moon.  
The repatriation process will happen at interval defined by **Repatriate interval** settings.  
If the **Repatriate moons** setting is ticked, the moons resources will be repatriated as well.  
If you want to keep a minimum amount of deuterium on your moons, you can configure it with **Min deut on moon**.  

##### [Defender active]<a name="defender-active"></a>

The bot will check for attacks every X interval of time and evacuate the attacked planet right before the attack.  
If you set the check interval to 10 - 20, the bot will pick a random number in that range, and wait X minutes before it check for attack again.  
When an attack is detected, you can see what evacuation are planed on the bot home page.  
On the home page, you can "cancel" an evacuation if you want. Or rescheduled a cancel evacuation (if it was previously cancelled).  


**The evacuation process:**  
The bot will try to build the most expensive building, and the most expensive research available.  
Then it takes the remaining resources and fit as much as possible in the available cargo.  
It fills the cargo with the deuterium first, then crystal and then metal.  
The fleet is now evacuated.  
(opt) If any resources remains, the bot will dump it in the defenses.  
Once the attack is done, the bot cancel the building and research that he started.  
(opt) Then the fleet is recalled.  


When one of your moon is being attack with "destroy" mission, and different logic is used for evacuation.  
The bot will send your fleet on another moon if you have one, or a different planet.  
You can choosed the speed of the fleet with **Evacuation speed on destroy attacks**.  

##### [Evacuate seconds before attack]<a name="evacuate-secs-before-attack"></a>
Defines how much time before the attack we want to evacuate the planet.  

##### [Recall seconds after attack]<a name="recall-after-attack"></a>
If ticked, defines how much time after the attack before the fleet is recalled.  

##### [Dump remaining resources in defenses]<a name="dump-resources"></a>
If ticked, the bot will spend remaining resources on defenses.

###### [activate for moon ?]<a name="moon-dump-resources"></a>
moon can take a very long time to build defenses, so you have to activate the feature for moon if you want it.  

##### [Send message to attackers]<a name="send-message-to-attackers"></a>
When a new attack is detected, the bot will verify that the same player is not already attacking us.  
If that's the case, the bot will send him a chat message of the like: "Hey bro, I'm online"  
The message is randomly selected from an hardcoded list. (the messages are going to be configurable in the future)  

##### [Randomize planet/moon that checks for activity]<a name="randomize-planet"></a>
Every time the defender look for attacks, it generates activities on the current active planet/moon.
If this option is selected, the bot will select a random planet/moon to check for incoming attacks. 
