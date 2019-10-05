# Defender

##### [Email alert when attacked]<a name="email-alert"></a>

An email will be sent to you when you are attacked.  
For this feature to work, you need to first <a href="/admin/settings">setup your SMTP settings</a> in the admin panel.  
The defender checkbox must also be checked (Defender active).  
You can save your email address and toggle the email feature by toggeling the checkbox next to your email.

##### [Repatriate resources]<a name="repatriate-resources"></a>

If you active this feature, all the planets are going to send their resources to the selected planet/moon every X hours.

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


##### [Send message to attackers]<a name="send-message-to-attackers"></a>

When a new attack is detected, the bot will verify that the same player is not already attacking us.  
If that's the case, the bot will send him a chat message of the like: "Hey bro, I'm online"  
The message is randomly selected from an hardcoded list. (the messages are going to be configurable in the future)  
