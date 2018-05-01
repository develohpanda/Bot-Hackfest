# Introduction 
Microsoft has a barebones C# starter project for bots, however it can require some plumbing to get running properly. By all means, if you wish to use one of those example projects, you can. For the purposes of the Hackfest, I have extracted patterns and helpers developed and used in a very recent bot.

I have also added a conversation to demonstrate how a conversation may flow, employing some of the helpers included in the [HackfestBotBase](https://intergen1-my.sharepoint.com/:f:/g/personal/openders_intergen_org_nz/Et7L8EqkBWxCk6pK78_8UrUBgeKqr1vaoywMF38NjKxTEw).

## Demo

The purpose is to identify the users name and persist it. If the name is already saved, the user is not asked and it loads from memory. It then prompts the user to type a phrase, provides suggested responses, and uses the response to determine the next step. This demo implemented in `dialogs/DemoDialog.cs` of the HackfestBotBase incorporates all of the bot concepts and features described before, and serves as an example. You can experiment with it [here](https://hackfestbotbase.azurewebsites.net/).

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

# Bot Concepts
This section will primarily link to the [Bot Service](https://docs.microsoft.com/en-us/azure/bot-service/) and [Bot Builder SDK](https://docs.microsoft.com/en-us/azure/bot-service/dotnet/bot-builder-dotnet-overview) documentation on MSDN as the main source of information. At the very least, I suggest reading through the following short documents to understand the [key concepts](https://docs.microsoft.com/en-us/azure/bot-service/dotnet/bot-builder-dotnet-concept).

## First interaction: Language vs Menus
[MSDN](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-design-first-interaction#language-versus-menus)

Starting the bot with an open-ended question such as "How can I help you?" is generally not recommended. If your bot has a hundred different things it can do, chances are users won’t be able to guess most of them. Your bot didn’t tell them what it can do, so how can they possibly know? **Menus provide a simple solution to that problem.**

## Dialogs, conversation flow
[Design](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-design-conversation-flow), [Implementation](https://docs.microsoft.com/en-us/azure/bot-service/dotnet/bot-builder-dotnet-manage-conversation-flow)

Dialogs enable the bot developer to logically separate areas of bot functionality and guide conversation flow. 

When one dialog invokes another, the Bot Builder adds the new dialog to the top of the dialog stack. The dialog that is on top of the stack is in control of the conversation. Every new message sent by the user will be subject to processing by that dialog until it either closes or redirects to another dialog. When a dialog closes, it's removed from the stack, and the previous dialog in the stack assumes control of the conversation. 

![dialogs-screens](./assets/dialogs-screens.png)

## Dialog lifecycle
[MSDN](https://docs.microsoft.com/en-us/azure/bot-service/dotnet/bot-builder-dotnet-manage-conversation-flow#dialog-lifecycle)

When a dialog is invoked, it takes control of the conversation flow. Every new message will be subject to processing by that dialog until it either closes or redirects to another dialog.

In C#, you can use `context.Wait()` to specify the callback to invoke the next time the user sends a message. To close a dialog and remove it from the stack (thereby sending the user back to the prior dialog in the stack), use `context.Done()`. You must end every dialog method with `context.Wait()`, `context.Fail()`, `context.Done()`, or some redirection directive such as `context.Forward()` or `context.Call()`. A dialog method that does not end with one of these will result in an error (because the framework does not know what action to take the next time the user sends a message).

## Channel inspector

[MSDN](https://docs.botframework.com/en-us/channel-inspector/channels/WebChat)

This will allow you to see all the different types of cards that can be attached to messages to add rich data to your conversation.

## Additional links
- **[MAKE BOTS SMARTER](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-concept-intelligence)** with Cognitive Services
- [Bot scenarios](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-scenario-overview)
- [BotBuilder-Location](https://github.com/Microsoft/BotBuilder-Location)
- [C# samples](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp) 

# Features of the HackfestBotBase
All of the features below, posting and recieving messages, and handling basic conversation flow, are implemented in the `DemoDialog.cs` example class. When you first run the project, this is the dialog that will power the conversation (as configured in `MessageControler.cs`).

This project is built on top of the base bot project by Microsoft, so all of the documentation on MSDN is still relevant. However some patterns and helpers have been developed while building another prototype and developing internal IP, which are included within this project.

**_This is one of the first iterations of the internal base project, and it will improve over time._**

## Data storage/state helpers
The bot framework has three data stores. ([MSDN](https://docs.microsoft.com/en-us/azure/bot-service/dotnet/bot-builder-dotnet-concepts#state))
- `User`: data associated with a specific user (across all channels and conversations)
- `Conversation`: data associated with a specific conversation with a specific user
- `PrivateConversation`: data associated with a specific user within the context of a specific conversation

These are stored in an in-memory store, but if you set the `AzureWebJobsStorage` config value, it will use Table Storage. This is automatically configured once you deploy to Azure, but locally the in-memory store will be used.

These are key-value stores and can get unwieldy to manage when data is written to one store with a certain key, and read from a different store with a different key. This can especially happen when there are many key-value pairs being stored. It is welcoming bugs and is a trigger for rip-your-hair-out syndrome.

There are two main classes that enable a cleaner method of managing the data stores. 
- `models/DataStoreKey.cs` is an enum, defining keys for the key-value pairs. Each enum value has an attribute identifying the data store to use (User, Conversation, PrivateConversation)
- `services/BotDataService.cs` contains logic pertaining to reading and writing from the data store

The implementation in the `BotDataService.cs` and `SetValue()`/`GetValueOrDefault()` extension methods ensure data is read/written against the correct key in the correct store, and is used as per the examples below. In order to use this bot service, inject the interface `IBotDataService` to your class.

```cs
public enum DataStoreKey
{
    [DataStoreEntry("Preferred name", DataStore.User)]
    PreferredFirstName
}
```
```cs
// Snippet from class BotDataService
...
public void SetPreferredName(IBotData botData, string name) 
{
    botData.SetValue(DataStoreKey.PreferredFirstName, name);
}

public string GetPreferredName(IBotData botData) 
{
    return botData.GetValueOrDefault<string>(DataStoreKey.PreferredFirstName);
}
...
```
## Autofac/IoC
A conversation and all of its instances/resources will get serialized and saved to the data store by the bot framework, so when creating services to integrate with the bot, we need to ensure they don't get serialized and are resolved each time. Serialization can cause unnecessary issues.

When registering a service with Autofac, use the the `FiberModule.Key_DoNotSerialize` key. 
```cs
builder.RegisterType<MessageService>()
    .Keyed<IMessageService>(FiberModule.Key_DoNotSerialize)
    .AsImplementedInterfaces()
    .InstancePerLifetimeScope();
```

Use the examples in the `IoC` folder for registrations.

## Dialog builder
The dialog builder simplifies resolution of a dialog through an Autofac registration. Because of the way Autofac requires the creation of a lifetimescope each time a registered services needs to be resolved, the code can get cluttered with unnecessary plumbing. 

For this reason, all logic pertaining to dialog creation/resolution should be contained within the `dialogs/DialogBuilder.cs` class. 

Use the example implementations and notes in the files below.
- IoC registration: `IoC/ApplicationDialogsModule.cs`
- IoC resolution: `dialogs/DialogBuilder.cs`
- Usage: `dialogs/DemoDialog.cs`

As in the examples below, the CreateDialog method handles all the lifetime scope creation/deletion duties when resolving an instance.

#### Example: Register and resolve dialog without no custom parameters
```cs
// Snippet from DialogBuilder.cs
public NameDialog BuildNameDialog(IMessageActivity message)
{
    return CreateDialog(message, scope => scope.Resolve<NameDialog>());
}
```
```cs
// Snippet from NameDialog.cs
...
private readonly IBotDataService _botDataService;

public NameDialog(IBotDataService botDataService)
{
    SetField.NotNull(out _botDataService, nameof(botDataService), botDataService);
}
...
```
```cs
// Snippet from ApplicationDialogsModule.cs
...
builder.RegisterType<NameDialog>().AsSelf().InstancePerDependency();
...
```
#### Example: Register and resolve dialog with custom parameters
```cs
// Snippet from DialogBuilder.cs
public ShowSuggestedActionsDialog BuildShowSuggestedActionsDialog(IMessageActivity message, string prompt, List<string> options)
{
    return CreateDialog(message, scope => scope.Resolve<ShowSuggestedActionsDialog>(TypedParameter.From(prompt), TypedParameter.From(options)));
}
```
```cs
// Snippet from ShowSuggestedActionsDialog.cs
...
private readonly IMessageService _messageService;

public ShowSuggestedActionsDialog(string prompt, List<string> options, IMessageService messageService)
{
    SetField.NotNull(out _messageService, nameof(messageService), messageService);
    SetField.NotNull(out _options, nameof(options), options);
    SetField.NotNull(out _prompt, nameof(prompt), prompt);
}
...
```
```cs
// Snippet from ApplicationDialogsModule.cs
...
builder.Register((c, p) =>
    new ShowSuggestedActionsDialog(
        p.TypedAs<string>(),
        p.TypedAs<List<string>>(),
        c.Resolve<IMessageService>()))
    .AsSelf()
    .InstancePerDependency();
...
```

## Message service
The purpose of this service is to send multiple messages from the bot to the user. This helper service will split a string into separate chat messages, using newline characters `\n` in the original string.

For example, `_messageService.PostAsync("Hello!\nI'm on a new line!");` will send messages as shown in the following image.

![MessageService](./assets/MessageService.png)

Reading separate concise messages is nicer than reading a big paragraph, in a conversational context. Think about how to effectively communicate using shorter messages.

## User persistance
When you use any channel other than a webchat, such as Messenger, a user id is returned unique to each channel. When using a webchat, if the web app has a concept of user accounts, then that account id is used to identify an existing user and load their data from state.

In a web app without the concept of a user account, there is no way of automatically identifying a return user. The [BotFramework-WebChat](https://github.com/Microsoft/BotFramework-WebChat) control by Microsoft is open-source, so on [my fork](https://github.com/develohpanda/BotFramework-WebChat) I have added a new boolean property named `persistUser`. The compiled version of this fork exists [here](https://github.com/develohpanda/BotFramework-WebChat), and can be used to embed an app using the CDN links (https://cdn.rawgit.com/develohpanda/Bot-Hackfest/master/botchat.css, https://cdn.rawgit.com/develohpanda/Bot-Hackfest/master/botchat.js).

If this flag is set, the modification is enabled. On launching the chat, it will generate a new user id and persist it to localstorage. If an id already exists, that id will be used to identify a return user. A timeout can be added in the future, but at the moment there is no timeout. This means in the demo above, if you have already provided your name, because the bot identifies a return user with the saved id, it knows your name and doesn't ask you again.

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

# Having issues?
- Is each dialog class taggged with the `[Serializable]` attribute?
- Do the service registrations have the `FiberModule.Key_DoNotSerialize` key added? 
```cs
builder.RegisterType<MessageService>()
    .Keyed<IMessageService>(FiberModule.Key_DoNotSerialize)
    .AsImplementedInterfaces()
    .InstancePerLifetimeScope();
```
