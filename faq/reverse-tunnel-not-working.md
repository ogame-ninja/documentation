## Reverse tunnel not working?

The easiest way to access your application from another device is to enable the "reverse tunnel"
which gives you access to your application via <usename>.me.ogame.ninja  

If you have an error that looks like:  

```
client connect error: dial tcp xx.xx.xx.xx:12345: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.
```

It could be that either your firewall OR your router is blocking the connection to the reverse tunnel server.  
Make sure that your router is not blocking the connection.