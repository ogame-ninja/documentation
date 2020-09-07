## How to configure telegram

If you wish to use our Telegram notifier, you'll have to get your "Current Chat ID" by messaging the word `/start` to an account
named `@getmyid_bot`, which will reply to you with your Telegram Chat ID.  
You need to place this chat id in your bot notifications settings page:  
`/bots/<BOT-ID>/settings/notifications`

### Cloud
Simply send the word `/start` to the account named `@OGameAttackBot`
(there is no confirmation message, don't panic).

### Self-host
You will have to set up your own telegram bot. ([detailed instructions](https://www.siteguarding.com/en/how-to-get-telegram-bot-api-token))

- Find telegram bot named `@botfather`
- To create a new bot type `/newbot` and follow instructions.
- Put your telegram bot token ID in [http://127.0.0.1:8080/admin/settings](http://127.0.0.1:8080/admin/settings). 
- Type `/start` to your newly created bot.
    