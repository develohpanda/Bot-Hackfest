# Introduction 
Microsoft has a barebones C# starter project for bots, however it can require some plumbing to get running properly. By all means, if you wish to use one of those example projects, you can. For the purposes of the Hackfest, I have extracted patterns and helpers developed and used in a very recent bot.

I have also added a conversation to demonstrate how a conversation may flow, employing some of the helpers included in the [HackfestBotBase](https://intergen1-my.sharepoint.com/:f:/g/personal/openders_intergen_org_nz/Et7L8EqkBWxCk6pK78_8UrUBgeKqr1vaoywMF38NjKxTEw).

## Features of the HackfestBotBase
### Autofac/IoC
### Data storage helpers
The bot framework has three data stores - UserData, ConversationData, UserConversationData. These are key-value stores, and can get difficult to manage if the keys, deseriali

### Dialog builder
The dialog builder simplifies resolution of a dialog through an Autofac registration. Because of the way Autofac requires the creation of a lifetimescope each time a registered services needs to be resolved, the code can get cluttered with unnecessary plumbing. 

For this reason, all logic pertaining to dialog creation/resolution should be contained within the `dialogs/DialogBuilder.cs` class. 

Use the example implementations and notes in the files below.
- IoC registration: `IoC/ApplicationDialogsModule.cs`
- IoC resolution: `dialogs/DialogBuilder.cs`
- Usage: `dialogs/DemoDialog.cs`

### Message service
The purpose of this service is to send multiple messages from the bot to the user. This helper service will split a string into separate chat messages, using newline character `\n` in the original string.

For example, `_messageService.PostAsync("Hello!\nI'm on a new line!");` will send messages as shown in the following image.

![MessageService](./assets/MessageService.png)

Reading separate concise messages is nicer than reading a big paragraph, in a conversational context. Think about how to effectively communicate using shorter messages.

### Suggested Actions dialog


- Autofac/IoC configured, with examples of how to register and use dialogs/services
- Standard settings model to load from web.config
- Data storage helper (`services/BotDataService.cs`, `models/DataStoreKey.cs`)
- DialogBuilder to resolve dialogs through Autofac in a clean manner (`dialogs/DialogBuilder.cs`)
- `services/MessageService.cs` to split a string into separate chat messages. Each newline character `\n` will 
- `dialogs/ShowSuggestedActionsDialog.cs`

## Demo

This demo conversation is implemented in `dialogs/DemoDialog.cs`. The purpose is to identify the users name and persist it. If the name is already saved, the user is not asked and it loads from memory. It then prompts the user to type a phrase, provides suggested responses, and uses the response to determine the next step.

![Conversation](./assets/Conversation.png)

# Get started
1. Ensure you have Visual Studio 2017 installed.
2. Download the base solution from [here](https://intergen1-my.sharepoint.com/:f:/g/personal/openders_intergen_org_nz/Et7L8EqkBWxCk6pK78_8UrUBgeKqr1vaoywMF38NjKxTEw). _Suggestion: create a git repository and host it somewhere to collaborate and track changes._
3. Open the solution
4. Right click on the solution > Restore nuget packages
5. Build solution
6. Start debugging the API project (F5)
7. Open the Bot Emulator
8. Navigate to http://localhost:3978/api/messages and press connect.

## Test locally with the Bot Emulator
1. Install the bot emulator using the instructions [here](https://github.com/Microsoft/BotFramework-Emulator/wiki/Getting-Started).
2. Once your project has started, open the bot emulator, navigate to http://localhost:3978/api/messages and press connect.

![Setup-3](./assets/Setup-3.png)

3. If successful, you should see 200 in the bot emulator logs.

![Setup-4](./assets/Setup-4.png)

4. **SAY HI!**

![Setup-5](./assets/Setup-5.png)

# Set up Azure Environment
We will set up a Web App Bot as a base.

![Setup-1](./assets/Setup-1.png)

Enter a bot name, and the remaining fields should automatically populate. Use the Basic C# bot template if using the Hackfest starter project. ([MSDN](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-quickstart))

![Setup-2](./assets/Setup-2.png)

# Publish
Follow the steps below to deploy to Azure.

![Publish-1](./assets/Publish-1.png)

![Publish-2](./assets/Publish-2.png)

Select the app service created when you created the bot resource.

![Publish-3](./assets/Publish-3.png)

![Publish-4](./assets/Publish-4.png)

![Publish-5](./assets/Publish-5.png)

Make sure to republish after changing settings.
![Publish-6](./assets/Publish-6.png)

## Configure Channels

MSDN documentation for configuring channels, speech priming etc [here](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-manage-channels).

### DirectLine
![Publish-7](./assets/Publish-7.png)

![Publish-8](./assets/Publish-8.png)

Republish, and then you can navigate to https://your-bot-name.azurewebsites.net/ to use the chatbox.

You can also navigate to http://localhost:3979/default.htm to view the same page, although the bot will be connected to the deployed version in Azure. To debug a local version of the bot, use the bot emulator.