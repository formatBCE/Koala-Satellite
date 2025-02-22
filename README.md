 # This fork is for those people who have a respeaker lite with soldered xiao esp32-s3 and wish to use the Koala case with the led light ring.
## To accomplish this:
   1. Print the newfaceplate.step file. As the esp32 does not support the use of the volume control, this removes that control and adds a home assistant logo.
   2. Solder the led strip to pin 1 of the esp32 and the 5V and ground pads on the side of the respeaker lite
   3. Use the modified koala-base.yaml file in home assistant. Change the name to anything you desire.
   4. That's it, enjoy the improved sound and HA lighting control of koala!
 <img src="/casing/images/koala1.jpg" width="400">
Watch the video of respeaker in action with music assistant: Don't be too concerned about response time, my internet is slow.... (https://www.youtube.com/shorts/Wk3J1GyNItQ)

# UPDATE 1: I have managed to include the Koala KY040 encoder to allow the use of the original casing and have full volume control. The KY040 pushbutton also allows for creating another event based home assistant automation. in combination with the existing respeaker lite pushbutton automation, this allows for up to 7 events from the pushbuttons on the media player for home assistant to respond to! (1 single press, 2 double presses, 2 triple presses and 2 long presses)

# To accomplish all this I have added a d1 mini to control the koala KY040 encoder.
The D1 mini is inside the koala case, and communicates with the koala media controller via home assistant.  
This allows the respeaker lite with embedded esp s3 to use the koala encoder for volume control!  
An additional benefit is the encoder pushbutton actions have been added to home assistant as events that can be included in automations.  
The button appears very stable and the D1 mini does not appear to have the  debouncing issues that formatbce reported with using the external ESP s3.  
An example automation to illustrate the use of the encoder pushbutton is included, see: "example ha automation.yaml"  The events are 'single_press', 'double_press' 'triple_press' and 'long_press'

the esphome config for the d1 mini is provided as: 'encoder.yaml'   
change the wifi ssid and media player name. 
The wiring is straightforward:  

Vbus - 5V  
GND - GND  
gpio0 - SW  
gpio4 - CLK  
gpio5 - DT  


 <img src="/casing/images/encoder1.jpg" width="400">
  <img src="/casing/images/encoder2.jpg" width="400">
   <img src="/casing/images/encoder3.jpg" width="400">

Simply connect the d1 mini and add it to the original case.
# important: In the esphome home assistant configuration for the d1 mini encoder, click on configure and select "Allow the device to perform Home Assistant actions" or it will not work!

# UPDATE 2: I have created a voice control example automation that might save you some time in figuring out how to do this. The file is uploaded as 'voice command for koala.yaml' and feel free to modify it for any voice commands you may wish to implement.
