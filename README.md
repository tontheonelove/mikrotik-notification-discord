# mikrotik-notification-discord

## Steps to set up Link Down notifications to Discord:
### 1. Create a Webhook in Discord
- Go to the Discord Server where you want to receive notifications
- Right-click on the Text Channel where you want notifications to appear
- Select Edit Channel > Integrations > Webhooks
- Create a new Webhook by clicking New Webhook
- Give it a name and select the Text Channel
- Click Copy Webhook URL to copy the Webhook URL

### 2. Write a script in Mikrotik
- A script in Mikrotik will check the status of the connection and send a notification message to Discord when there is a Link Down event.

- Log in to Mikrotik RouterOS via Winbox or WebFig
- Go to System > Scripts and add a new script

Example Script :

```
/interface
:foreach i in=[find where running] do={
    :local interfaceName [/interface get $i name]
    :local interfaceStatus [/interface get $i running]
    
    :if ($interfaceStatus = false) do={
        /tool fetch url="https://discord.com/api/webhooks/YOUR_WEBHOOK_URL" \
            http-method=post \
            body="{\"content\":\"Alert: Interface $interfaceName is DOWN!\"}"
    }
}

```

### In this script:
- Check the status of the currently running interface.
- If the status of the interface is down ($interfaceStatus = false), the script will send a notification message to the Discord Webhook you created.
- Replace YOUR_WEBHOOK_URL with the Webhook URL copied from Discord.

### 3. Schedule the script to run automatically
- You can schedule Mikrotik to run this script every desired time (e.g. every 1 minute) using the Scheduler in Mikrotik

- Go to System > Scheduler and add a new entry
- Set the Name and Interval (e.g. 1m for every 1 minute)
- In On Event, enter the name of the script you created (e.g. CheckLinkStatus)

### Example of scheduling:
```
/system scheduler add name="Check Link Status" interval=1m on-event="/system script run CheckLinkStatu
```

### 4. Test the system
- Turn off some interfaces on the Mikrotik (such as the LAN or WAN interface) to test the script's functionality.
- When the specified interface is down, the system sends a message to the specified Discord channel.
### 5. Customize the message in Discord
- If you want to customize the message sent to Discord (e.g., inserting icons, using Markdown symbols, or naming the message differently), you can do so in the body field of this script.

### Example of customized message:
```
body="{\"content\":\"**Alert:** Interface $interfaceName is **DOWN**. Please check immediately! 🚨\"}"
```

### Example of how it works:

- When the interface you set in Mikrotik is down, you will receive a notification in the Discord Channel like this:

**Alert:** Interface ether1 is **DOWN**. Please check immediately! 🚨

## Additional tips:
- If you want to check multiple interfaces or multiple states (e.g. Up, Down, Warning), you can customize your script to support checking multiple states.
- You can set up additional notifications in Discord by using Embeds to send data in a beautiful format.

