# Team Presence to HA
Teams presence update on Home Assistant via webhook with Power Automate. This is usefule if you do not have acces to install software or run scripts on your work issued computer. You will need Power Automate Desktop and be able to send webhooks over your LAN to Home Assistant. 

**Home Assistant Setup:
**


The [teams_webhook.yaml](./teams_webhook.yaml ) creates an input boolean to be switched via webhook and the webhook calls to switch it. This boolean can also be added to your dashboard as an indicator and manual overide button.

<img width="206" height="59" alt="Screenshot 2026-01-27 at 9 24 39 PM" src="https://github.com/user-attachments/assets/38d6448c-2907-4a6e-987a-eb101a493be3" /><img width="206" height="59" alt="Screenshot 2026-01-27 at 9 24 47 PM" src="https://github.com/user-attachments/assets/fe26822b-ad02-4dfe-a068-39090d4bdc25" />

You can upload the teams_webhook.yaml file directly to the /packages directory in Home Assistant as long as you have this line in your configurations.yaml file 

```
homeassistant:
  packages: !include_dir_named packages
```

There are two light_automation_*.yaml files [light_automation_then_off.yaml](./light_automation_then_off.yaml) will set your indicator light to red when triggered and turn it off on the off trigger. [light_automation_then_restore.yaml](./light_automation_then_restore.yaml) will set your indicator light to red when triggered and return it to its previous state on the off trigger (or manual overide via dashboard button). It saves the current state as a seen when "On a Call" is triggered. These automations should be coppied to your automations.yaml file and they will be triggered via the input boolean set up in teams_wbhook.yaml. You will need to edit the device IDs for your specific lights.

**Power Automate Flow
**

Start by creating two subflows, one for the webhook "On" and one for the webhook "Off"
