## Can I use 2fa with my bot ?

Yes, you can use Two-Factor Authentication.

If you already have 2fa setup on your gameforge account, you will need to remove it first.

- Open a terminal
- Drag & drop the binary file into the terminal
- Add `2fa register` at the end of the command (eg: `/path/to/ninja.x.x.x.darwin.amd64 2fa register -u gameforge_email@gmail.com -p gameforge_password`)

Get the 2fa secret using:

`/path/to/ninja.x.x.x.darwin.amd64 2fa show -u gameforge_email@gmail.com`

When you create your bot, set your 2fa secret in the advanced settings.

If you already have a bot, you can update it's 2fa secret in the bot settings.

For more information about 2fa, see [https://www.ogame.ninja/2fa](https://www.ogame.ninja/2fa)