# goipelastix
###Use GoIP4 GSM Gateway with Elastix VoIP PBX (Asterisk based) /w automatic GSM provider channel selection (Least Cost Routing, LCR)

This project explains the implementation of a [GoIP4 GSM Gateway] (http://www.hybertone.com/en/products.asp?Id=65&FId=65) in combination with an [Elastix VoIP PBX](http://www.elastix.org/) system. I'll explain the setup and configuration of the GoIP4 and specific settings for the Elastix PBX to assign the PBX to act as a Least Cost Router (LCR) for the cellphone network. The outbound routes at the Elastix PBX will automatically select the correct GSM provider depending on the dialed cellphone/provider area code assuring only network-network connections.

This setup is valid for the Philippines, but I assume the idea of LCR via selected provider SIM channel would be working for many parts of the world.

The goal is, that the user just dials the cellphone number and must not care about which carrier to use. For example 0917... is the area code for the provider Globe here in PH, which should be dialed over CH1 or CH2 SIM in my sample setup. 

I found many other howto's for GoIP devices, which always let the users select the channel by dialing a specific prefix, for example 71,xxxx for channel 1 and 72,xxxx for channel 2 etc. But I found this solution not practical because most users simply don't know which provider has which number.

####In few words:
I use the 'prepend' function of Elastix's outbound dial rules to add a channel selector (1-4) to the dialed number. The user dials 091785001234... Elastix prepends a 1 for CH1 SIM, sends for example 1091785001234 to the GSM gateway. This channel selector (first digit) is read by the dial-out rule of the GoIP device, selects the outbound channel and is then being removed. This makes it possible to create a list of dial rules in Elastix which will simply select the outbound GSM channel depending on the dialed cellphone network (area code).

###First the GoIP Setup:
I do not explain the entire setup of the GoIP, will more concentrate on the specific settings for the dial rules. If you need further information about getting GoIP working with Elastix, visit [this link](http://nerdvittles.com/?p=7581) or [this one](http://zr6aic.blogspot.com/2014/11/how-to-setup-goip-148-gsm-voip-gateway.html). Two youtube videos [here](https://www.youtube.com/watch?v=gsF_XznKsnk) and [here](https://www.youtube.com/watch?v=QD-06eZ7JuQ) may also help, but sadly they are partly in Portugese.

In this case I setup the GoIP Voip link to 'Single Server Mode'. The IP of my Elastic server is 192.168.85.10. 
The password will be set at the Trunk setting on Elastix, see further down.

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001953.jpg)

Some more changes at 'Advanced Voip', mainly regarding DTMF tones.

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001954.jpg)

Call out settings: Here it is important to set a Dial Plan for each channel, for CH1 --> 1:-1  CH2 --> 2:-2 .... This setting will recognize the selected outbound channel and then remove the prefix before dialling the destination.

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001955.jpg)

Call in settings are quite straight forward, I use extension 951 at the Elastix PBX to route incoming GSM calls via follow me to the internal destination queue. Directly targeting the queue - without follow me - didn't work because then the caller will get a ring instead of our waiting announcements.

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001956.jpg)

Elastix configuration

First setup a SIP trunk like below:

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001948.jpg)

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001949a.jpg)

Create an outbound route for the selected GSM provider

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001950.jpg)

Important here the 'prepend' value, which selects the channel on the GoIP for the provider area code:

![alt tag](https://raw.githubusercontent.com/chrleyer/goipelastix/master/SCR_20161019_001951.jpg)






