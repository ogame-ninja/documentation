## Browser is loading forever?

If you are not "self-hosting", you can skip reading this page.  

This is happening because the browser can only open 6 simultaneous connections to the same server.  
Some pages in ogame open 5 connections at the same time (fleet page), and the bot opens at least 1 for the websocket.  
So if another tab is open, the maximum connections is reached, and the browser refuses to load.  

### Solution 1: use https for multiplexing connections (for self-hosting)

Start the application with the flag `--tls` (eg: `./path/to/nja --tls`)  
Then open the browser at `https://localhost:8080`  
And you should not longer have issues with the browser loading forever  

### Solution 2: use firefox and change the configs (not the best)

Get and use firefox https://www.mozilla.org/en-US/firefox/  
Open a new tab and go to `about:config`  
Search for `network.http.max-persistent-connections-per-server` and put `20` instead of the default `6`  
And that will solve the browser not loading

### Solution 3: use reverse tunnel

Go to your admin section, and toggle on the "reverse tunnel"  
Then when you use `https://<your-username>.me.ogame.ninja` you should no longer have the browser loading forever issue.  
That being said, it should much slower than Solution 1  