<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Dev Session Notes](#dev-session-notes)
  - [Goals and outcomes](#goals-and-outcomes)
  - [Groups](#groups)
  - [Content to present](#content-to-present)
  - [Plan for the afternoon](#plan-for-the-afternoon)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Dev Session Notes
Max 20-30 minute session

## Goals and outcomes
The goal of the dev session is to demonstrate the high level jump start and just verbally explain what is happening. It is following the get started instructions on Github, but just demonstrating it. Secondly, run through the comments 1-14 in the `DemoDialog.cs` and `NameDialog.cs` to brifly show the **flow**. Get familiar with the base solution and familiar with the bot framework. The end goal is to NOT build a production ready product, but rather to prototype and experiement an idea.

## Groups
Get a show of hands of how many people have an idea. If there are enough, then give them all a maximum 30 seconds to pitch/explain their idea to the group (and who they need to). Then allow people to split off into groups. **Mention at the start that there will be a time to pitch at the end of the 20min session so people start thinking**.
- Ideally groups of 4-6
- Choose any group, try to ensure atleast one or two Modern Apps developers (proficient in C#) in each group. Try to have multi-diciplinary 
- Start with a timeboxed brainstorm of ideas
- Decide what you want to build
- Download/run the project, and start hacking

Its up to us as facilitators to ensure the groups are reasonable but its just a guideline - not all MA/strong C# devs in one group. 

## Content to present
- Always refer to the reference doc on Github, information presented is extracted from there
- Navigate to the [demo site](https://hackfestbotbase.azurewebsites.net/) and briefly demonstrate
- Run through getting started - download and run the bot locally through the emulator and demonstrate the same deployed version running in an emulator
- Run through the comments 1-14 in the `DemoDialog.cs` and `NameDialog.cs` to brifly show the **flow**.
- Explain the mindset shift when designing a bot ([dialogs, conversation flow](https://github.com/develohpanda/Bot-Hackfest#dialogs-conversation-flow))
![dialogs-screens](./assets/dialogs-screens.png)
- Explain the [dialog lifecycle](https://github.com/develohpanda/Bot-Hackfest#dialog-lifecycle)
- Explain the three data stores, explain/demonstrate how to add a new key-value to the bot store using the [helper class](https://github.com/develohpanda/Bot-Hackfest#data-storagestate-helpers)
- Refer to Autofac examples in the code, but try to keep that standard. Don't forget the donotserialize key for anything you don't want serialized but instantiated each time.
- Explain the [dialog builder](https://github.com/develohpanda/Bot-Hackfest#dialog-builder) and how to resolve a class from autofac. Use the examples in code.
- Explain briefly the [user persistence](https://github.com/develohpanda/Bot-Hackfest#user-persistence)
- Refer to Cognitive Services [link](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-concept-intelligence). This will give examples of additional services that the devs can use.
- Refer to the C# Bot framework [samples](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp)


## Plan for the afternoon
After the streams have split into separate rooms:
- 03 min - demo website and local emulator
- 03 min - run through 1-14 comments in code to explain flow
- 12 min - dialog flow/lifecycle, data stores, autofac, dialog builder
- 02 min - mention bot society, cognitive services/growth, samples
- 02 min - join yammer, brainstorm and start building, submission on Monday, details to come
- 05 min - allow people to quickfire pitch ideas
- 02 min - split into groups
- START BUILDING!
