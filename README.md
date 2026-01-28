# Team Presence to HA
Teams presence update on Home Assistant via webhook with Power Automate. This is usefule if you do not have acces to install software or run scripts on your work issued computer. You will need Power Automate Desktop and be able to send webhooks over your LAN to Home Assistant. 

Home Assistant Files:
The [teams_webhook.yaml](./teams_webhook.yaml ) creates an input boolean to be switched via webhook and the webhook calls to switch it. This boolean can also be added to your dashboard as an indicator and manual overide button.
