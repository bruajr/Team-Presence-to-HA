# Team Presence to HA
Teams presence update on Home Assistant via webhook with Power Automate. This is usefule if you do not have acces to install software or run scripts on your work issued computer. You will need Power Automate Desktop and be able to send webhooks over your LAN to Home Assistant. 

**Home Assistant Setup:**


The [teams_webhook.yaml](./teams_webhook.yaml ) creates an input boolean to be switched via webhook and the webhook calls to switch it. This boolean can also be added to your dashboard as an indicator and manual overide button.

<img width="206" height="59" alt="Screenshot 2026-01-27 at 9 24 39 PM" src="https://github.com/user-attachments/assets/38d6448c-2907-4a6e-987a-eb101a493be3" /><img width="206" height="59" alt="Screenshot 2026-01-27 at 9 24 47 PM" src="https://github.com/user-attachments/assets/fe26822b-ad02-4dfe-a068-39090d4bdc25" />

You can upload the teams_webhook.yaml file directly to the /packages directory in Home Assistant as long as you have this line in your configurations.yaml file 

```
homeassistant:
  packages: !include_dir_named packages
```

There are two light_automation_*.yaml files [light_automation_then_off.yaml](./light_automation_then_off.yaml) will set your indicator light to red when triggered and turn it off on the off trigger. [light_automation_then_restore.yaml](./light_automation_then_restore.yaml) will set your indicator light to red when triggered and return it to its previous state on the off trigger (or manual overide via dashboard button). It saves the current state as a seen when "On a Call" is triggered. These automations should be coppied to your automations.yaml file and they will be triggered via the input boolean set up in teams_wbhook.yaml. You will need to edit the device IDs for your specific lights.

**Power Automate Flow**

Create a new flow in Power Automate

Create two new subflows called SendOn and SendOff

<img width="416" height="35" alt="image" src="https://github.com/user-attachments/assets/09624c40-5c73-46da-a1aa-82b9cffa55aa" />

In SendOn use the Invoke web service action, the URL will be `http://YOUR-HA-IP:8123/api/webhook/teams_on_call_on` be sure to replace YOUR-HA-IP with the local IP address of your Home Assistant instance.

<img width="468" height="56" alt="image" src="https://github.com/user-attachments/assets/657c30e0-56ec-49ef-b8af-99c89796fd01" />
 
In SendOff use the Invoke web service action, the URL will be `http://YOUR-HA-IP:8123/api/webhook/teams_on_call_off` be sure to replace YOUR-HA-IP with the local IP address of your Home Assistant instance.

<img width="468" height="54" alt="image" src="https://github.com/user-attachments/assets/a5b11a65-6186-4951-9c43-55659a2a8bce" />

These are the two webhooks created in teams_webhook.yaml file and will turn your input boolen on or off.
In the main flow, create two Set variable actions.
The first variable should be `LastState` and the value should be `off`

<img width="468" height="283" alt="image" src="https://github.com/user-attachments/assets/21a97c12-b178-4a91-8ab5-adcafc76d477" />
 
The second variable should be `CurrentState` and the value should be `off`

<img width="468" height="284" alt="image" src="https://github.com/user-attachments/assets/5f606ed2-3e59-4a69-a7be-2dac6000f08e" />

**Note** all of the variables are case sensitive 
 
Next, create a Loop action, start from should be ‘1’, end to should be ‘2147483647’, and increment by should be ‘1’.
 
Inside of that Loop bracket start with the If Image action. This action will check your screen for specific images. Open a Teams call and then click select images while editing the action then use the image capture option to capture the Leave button on Teams
 
You can also capture the Teams taskbar icon showing the busy indicator 
 
**Note** The if image action searches your visible screen for a specific image, if you minimize your call window while on a call it will not be able to see the leave icon and will send an off command to Home Assistant. Using the busy indicator and leave button will send an on command to Home Assistant when your status is set to busy as well. You can adjust as needed for your setup.
The If Image setup should look something like this
 
Inside of the If Image bracket add set variable action, the variable should be ‘CurrentState’ and the value should be ‘on’
 
Next, still inside the If Image bracket add an Else action. Between Else and the end of the If Image bracket add another Set Variable the variable should be ‘CurrentState’ and the value should be ‘off’
 
Your entire If Image bracket inside of the Loop bracket should look like this
 
Next, still inside the Loop bracket add an If action First operand should be ‘%CurrentState%’, Operator should be Not equal to, Second operand should be ‘%LastState%’
 
Next, inside of that If bracket, add another If action. First operand should be ‘%CurrentState%’, Operator should be Equal to, Second operand should be ‘on’
 
Still inside of that second If bracket add a Run subflow action for the SendOn subflow, then an Else action, Then a Run subflow action for the SendOff subflow. 
The entire second If bracket inside of the first If bracket should look like this 
 
Outside of the second If bracket but inside the first If bracket add a set variable action the variable should be ‘LastState’ and the value should be ‘%CurrentState%’
 
Outside of the If Brackets but still inside the Loop bracket add a Wait and set the value to how often you want the flow to check if you are in a meeting. 
The entire Loop bracket should look like this. 
 
You can save this flow and publish it now. Once published you can create a desktop shortcut and use that to start it when you are WFH or add that shortcut to the Startup folder, so it starts every time you start your PC. 
