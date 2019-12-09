# Defender

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

##### Activities generted on
Every time the defender look for attacks, it generates activities on the current active planet/moon.
###### Randomize planet/moon that checks for activity
If this option is selected, the bot will select a random planet/moon to check for incoming attacks.
###### Checks for activity from homeworld
If this option is selected, the bot will select HomeWorld check for incoming attacks.
###### Checks for activity from latest active planet/moon
If this option is selected, the bot will use the last active planet/moon to check for incoming attacks.

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

##### [Desktop notification when attacked]<a name="desktop-notification"></a>
If the browser is open, an html5 desktop notification will be sent when a new attack is detected.

##### [Play sound when attacked]<a name="sound-notification"></a>
If the browser is open, an html5 audio notification will be sent when a new attack is detected.  
The sound will play for 21 seconds.  
**WARNING**: This is **LOUD**  

##### [Notify small fleet attacks (< 1/3 firepower)]<a name="notify-small-fleet"></a>
By default the bot will not send email/telegram notifications for small attacking fleet.  
If enabled, the bot will send notifications for non dangerous attacking fleet. Fleet that have less than 1/3 of our firepower.

##### [Send message to attackers]<a name="send-message-to-attackers"></a>
When a new attack is detected, the bot will verify that the same player is not already attacking us.  
If that's the case, the bot will send him a chat message of the like: "Hey bro, I'm online"  
The message is randomly selected from the textbox. Each line is a message.  

## Evacuation configs<a name="evacuation-configs"></a>
Evacuation mode can be set per planet.  
**Dangerous mode**: Will only evacuation when the attacking fleet is considered dangerous. (has more than 1/3 of our firepower)  
**Always mode**: Will always evacuate the planet/moon no matter the size of the attacking fleet.  
**Never mode**: Will never evacuation the planet/moon.


## Repatriate configs<a name="repatriate-configs"></a>

If you active this feature, all the "active" planets/moons are going to send their resources to the selected destination.  
The repatriation process will happen at interval defined by **Repatriate interval** settings.  
If **Active** checkbox is ticked, the celestial will repatriate it's resources to the selected destination.  
You can choose which resources to repatriate.  
If you want to keep a minimum amount of deuterium on the celestial, you can configure it with **Min deut**.  

