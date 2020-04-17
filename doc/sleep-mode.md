# Sleep mode

When the bot is in sleep mode, no call will ever be made by the bot to the ogame servers.  
**WARNING:** In sleep mode, the bot will **not** check for incoming attacks.  

A random delay of 0-5min will be added to selected times.  

If a script named `!sleep.ank` exists, it will be executed before the bot goes to sleep mode.  
If a script named `!wake.ank` exists, it will be executed after the bot exit the sleep mode. 

##### Manual mode:

In this mode, you have manually put the bot to sleep whenever yoou are ready to do so.  
The bot will wake up at the time set.

##### Simple

The bot will go to and exit the sleep mode at the specified times every day.
 
##### Advanced

You can configure the sleep schedule for each day of the week.

#### Automatic fleet save

If active, the bot will find the main fleet and fleet save it for the night and make sure it
comes back for the wake up time.  

This is an experimental feature, keep an eye on it.  