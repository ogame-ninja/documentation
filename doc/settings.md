# Settings

#### General Options

##### [Homeworld]
Celestial that will be considered the homeworld by the bot.  
Default value will be the first planet in your planets list in ogame.  

##### [Slots reserved for player]
Is this amount of fleet slots that the bot will keep available for the player to use.    
**Exception:** Defender will use them if it needs it. 

##### [Max farmer slots]
Maximum slots that the farmer will use. 0 means unlimited.

##### [Max brain slots]
Maximum slots that the brain will use. 0 means unlimited.

##### [Max repatriate slots]
Maximum slots that the repatriate will use. 0 means unlimited.

##### [Max script slots]
Maximum slots that the scripts will use. 0 means unlimited.

##### [Sleep mode]
When the bot is in sleep mode, no call will ever be made by the bot to the ogame servers.  
**WARNING:** In sleep mode, the bot will **not** check for incoming attacks.  

A random delay of 0-15min will be added to selected times.  

If a script named `!sleep.ank` exists, it will be executed before the bot goes to sleep mode.  
If a script named `!wake.ank` exists, it will be executed after the bot exit the sleep mode.  

##### [Email for notifications]
Email that will be used for notifications for this bot.

##### [Telegram chat id for notifications]
Telegram chat ID that will be used for notifications for this bot.

##### [Forward in-game messages to telegram]
If selected, the in-game messages will be automatically forwarded to your telegram.

##### [User-Agent]
The browser user-agent that the bot will used.  
Keep default value if you don't know what this is.


#### Browser augments

##### [Commander UI]
Inject a "max" link:
[<img src="/public/views/documentation/img/feature_browser2.jpg" alt="" width="500" />](/public/views/documentation/img/feature_browser2.jpg)

##### [Enable browser augments]
Inject many features in the browser
- List of players planets in galaxy page
- Color players in Highscore page
- Add resources production / time to fill storage in overview
- Improve message box UI
- And many more...

##### [Enable TopRaider]
Inject [TopRaider](http://topraider.eu/) script. (works in browser fullscreen mode only)  
If the email & password are set, spy reports that are imported in Nja are going to be automatically send to topraider.  

##### [Enable InfoCompte3]
Inject InfoCompte3 script. (works in browser fullscreen mode only)

##### [Enable RecursosAmpliados]
Inject RecursosAmpliados script. (works in browser fullscreen mode only)

#### Share account

If you have a license with more than 1 user, you can share access to your bot with other users.

#### Change proxy

If you want the bot to use a proxy, you can configure it here.

#### Change ogame credentials

If you changed the gameforge credentials, you can make sure the bot uses the new credentials using this form.

#### Delete bot

Deletes the bot.