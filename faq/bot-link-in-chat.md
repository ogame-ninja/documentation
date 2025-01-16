## Bot link in chat

Someone sent me a link that contains the bot information in the chat, how is that possible?

The bot rewrite all URLs in the html from

`https://s123-en.ogame.gameforge.com/...`

to

`http://127.0.0.1:8080/bots/1/browser/html/s123-en?...` (or `https://username.ogame.ninja/bots/1/browser/html/s123-en?...`)

Which means that you will see in the chat messages that someone sent information related to the bot, while in reality, they sent an ogame link, and they themselves see the ogame link.

They do not see or know anything about the bot.