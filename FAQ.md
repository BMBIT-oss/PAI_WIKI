## Errors
### Authentication Failed. Wrong Password
1. Make sure you know your PC password. In Babyware: `Right click on your panel -> Properties -> PC Communication (Babyware) -> PC Communication (Babyware) Tab.`
   1. EVO: Section 3012
2. Make sure you filled `PASSWORD` config parameter in the right config. See run log where config file is located.
3. If your password has `0` try to replace it with `a`. Some panels want `0` encoded this way. `0306` -> `a3a6`.

### No handler for message 5
Real reason is unknown.

This error means that a status request failed. Do not panic. It is normal if it is infrequent. 5-6 status requests are sent every 10 seconds. Probably when a panel sends a live event payload at the same time when PAI waits for a status request response, panel hangs and responds much later. We do not want to wait for more than 10 seconds for this reply. So we dispose handler and go further. Next batch will surely succeed.

## Other Questions
### Which panel is best for using with PAI
We consider EVO192/HD are the best panels available to use with PAI. Why? Answer is simple. They have the fastest serial port with baud rate 57600(38400 default) vs 9600 on SP/MG panels.