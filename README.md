# Introduction 
# Set up Azure Environment
We will set up a Web App Bot as a base.

![Setup-1](./assets/Setup-1.png)

Enter a bot name, and the remaining fields should automatically populate. Use the Basic C# bot template if using the Hackfest starter project.

![Setup-2](./assets/Setup-2.png)

# Set up local dev environment

Download the starter project from [here](https://intergen1-my.sharepoint.com/:f:/r/personal/openders_intergen_org_nz/Documents/Projects/Bot-Hackfest/Base?csf=1&e=pB9FOI). 

## Test locally with the Bot Emulator
https://github.com/Microsoft/BotFramework-Emulator/wiki/Getting-Started

# Publish
Follow the steps below to deploy to Azure.

![Publish-1](./assets/Publish-1.png)

![Publish-2](./assets/Publish-2.png)

![Publish-3](./assets/Publish-3.png)

![Publish-4](./assets/Publish-4.png)

![Publish-5](./assets/Publish-5.png)

Make sure to republish after changing settings.
![Publish-6](./assets/Publish-6.png)

## Configure DirectLine channel and WebChat

![Publish-7](./assets/Publish-7.png)

![Publish-8](./assets/Publish-8.png)

Republish, and then you can navigate to https://your-bot-name.azurewebsites.net/ to view the chatbox.

You can also navigate to http://localhost:3979/default.htm to view the same page, although the bot will still be connected to the deployed version in Azure. To debug a local version of the bot, use the bot emulator.