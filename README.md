# Build your own banking bot in a snap
Always wanted to know how to build your own banking chatbot? This workshop shows how to achieve this using the Watson Conversation API. You will start by:

* creating your own Watson Conversation service

* importing an existing configuration to give your bot a jump start

* extending the bot's functionality to your own discretion.

At the end of the workshop you have a bot capable of handling greetings, block your card and handle any other topic that you want your bot to be able to deal with.

## Before you begin
To use the Watson APIs, you need to have an IBM Cloud account. Your account must have enough resources available to create at least two services, one for the chatbot and one that -- under the surface -- automates the deployment of your bot.

## Step 1: Import the banking demo bot into your own cloud environment

1. Click the `Get this bot` link below to get the banking demo bot into your own cloud environment.

  <a href="https://console.bluemix.net/devops/setup/deploy?repository=https%3A//github.com/IBM/chatbot-deployer&chatbotName=Demo%20Banking%20Bot&chatbotWorkspaceURL=https://ibm.box.com/shared/static/rj2r8gf8vjdhc1o5x7rkynnsv1csjgry.json"><img src="https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/get-this-bot.png?raw=true" width="150"></a>

2. When prompted, login to IBM Cloud. Next, click **Create** to actually start the creation.

  ![Create pipeline][1]

3. To open the Conversation Tooling environment, click the conversation tile. You should now see your dashboard with a conversation service called `Bot Asset Exchange Workspaces`.

  ![Open Conversation tile][2]

4. Click this service and then click **Launch tool**.

  ![Launch tool][3]

5. In the available workspaces, select the **Banking Demo Bot** to start working with your bot.

  ![Select workspace][4]

## Step 2: Defining a new intent for the bot

An intent is a purpose or goal expressed in the user's input, e.g. the user wants to report a lost card and take action.

1. After the successful import in the previous section, you should see the following screen in front of you.

![Imported workspace][12]

2. On the Intents tab, select **Add intent** and add the following intent name: `kaart_kwijt`. This intent indicates that the user lost their card and wants the bank to block it.

![Workspace intents][5]

3. In the **User example** field, type `Ik ben mijn pas verloren` as sample sentence and press Enter.

4. Add these 4 more examples to help Watson recognize the `#kaart_kwijt` intent and hit Enter:
```
  Ik heb geen idee waar mijn bankpas is
  Ik ben mijn creditcard verloren
  Mijn pinpas is weg.
  Ik ben bang dat ik mijn credit card verloren ben.
```

Finally, click **Done** at the top right to complete the intent creation. You now defined an additional intent `#kaart_kwijt` with example utterances. These examples help train Watson to recognize the intents in the user's input.

## Step 4: Adding entities
An entity definition includes a set of entity _values_ that can be used to trigger different responses. Each entity value can have multiple _synonyms_, which define different ways that the same value might be specified in the user's input.

Create a new entity value to represent a new card type that your bot needs to recognize.

1. On your workspace page, click the Entities tab.
2. On the Entities tab, expand the `@card` entity type and click the **Add a new synonym value** to a add a new value.

The `@card` entity represents the different card types that the bot can handle.

1. Add `debitcard` as value in the **Value** field.The value represents a specific appliance that users might want to turn on.

2. Add `betaalpas` in the **Synonyms** field as another way to specify the music appliance entity.

3. Complete the synonym list by adding the following synonyms.

    * Synonym: `bank pas`.

    * Synonym: `maestro`.

    * Synonym: `pinpas`.

You defined one entity `@card` with two values -- representing the supported card types. The next section will explain how you can extend the existing dialog to use these intents and entities and to define the correct response.

## Step 5: Extending your dialog
A dialog is a set of conversational nodes that are contained in a workspace. Together the set of nodes makes a dialog tree, on which every branch is a conversation that can be held with a user. A _Welcome, Get weather info_ and _Greeting_ branch are already set up for you.

Now we are going to extend the dialog by adding a branch that responds to the `#turn_on` intent. Because there are multiple possibilities for what the user might want to turn on, this branch represents a more complex conversation.

For this, we start by creating the root-level node:

1. Click the **options** icon ![Options][7] on the right-hand side of the `#greeting` node and select **Add node below** to create a new root-level node.

2. In the **Name this node** field, enter `Turn On`. The title does not affect the processing of the node, but it makes it easier to find.

3. In the edit view, in the **Enter a condition** field, start typing `#turn_on`.

4. Select **#turn_on** from the list. This condition is triggered by any input that matches the `#turn_on` intent.

5. Do not enter a response in this node and click ![Close][6] to continue.

### Add multiple child nodes for `#turn_on`
The `#turn_on` intent requires additional processing, because the dialog needs to determine which appliance the user wants to turn on. To handle this, we create multiple responses based on additional conditions. There are three possible scenarios, based on the intents and entities that we have defined:

1. The user wants to turn on the music, in which case we need to ask for the genre.

2. The user wants to turn on any other valid appliance, in which case we simply echo the name of the requested appliance in a message that indicates that we're turning it on.

3. The user does not specify a recognizable appliance name, in which case we need to ask for clarification.

We'll check the conditions in this order. Determining the most efficient order in which to check conditions is an important skill in building dialog trees. If you find a branch is becoming very complex, check the conditions to see whether you can simplify your dialog by reordering them. It's often best to process the most specific conditions first.

To check the input, add a child node:

1. Click the **options** icon ![Options][7] on the right-hand side of the `#turn on` node and select **Add child node** to create a new child node.

2. In the **Name this node** field, enter `Music`.

3. Under **Enter a condition**, enter `@appliance:music`. This condition is true if the value of the `@appliance` entity is `music` or one of its synonyms, as defined on the Entities tab.

4. In the **Enter a response** field, enter `What kind of music would you like to hear?`

5. Exit the edit view of this node by clicking ![Close][6].

We want to jump directly from the _Turn On_ node to the _Music_ node without asking for any more user input. To do this, we use a **Jump to** action.

1. Click the **options** icon ![Options][7] on the right-hand side of the _Turn on_ node and select **Jump to**.
2. Select the _Music_ node, and then select **If bot recognizes (condition)**. We want to process the condition of the _Music_ node.

![Jump to before][8]

Note that you had to create the target node -- the node to which you want to jump -- before you added the **Jump to** action.

After you selected the place where to jump to, you should see the following in your dialog:

![Jump to after][9]

Now we need a node to process the type of music that the user requests.

1. Click the **options** icon ![Options][7] on the right-hand side of the _Music_ node and select **Add child node** to create a child node. Enter `Genre` in the **Name this node** field. This child node is evaluated only after the user has responded to the question about the type of music. Because we need a user input before this node, there is no need to use a **Jump to** action.

2. Under **Enter a condition**, enter `@genre`. This condition is true whenever a valid value for the `@genre` entity is detected.

3. In the **Enter a response** field, enter `OK! Playing @genre.` This response uses the value that the user entered.

We also need a node to respond when the user does not specify a recognized value for `@genre`.

1. Click the **options** icon ![Options][7] on the right-hand side of the _Genre_ node and select **Add node below** to create a peer node. Name it `Genre not recognized`.

2. Under **Enter a condition**, enter `true`. This condition specifies that if the dialog flow reaches this node, it should always evaluate as true. If the user specifies a valid `@genre` value, this node will never be reached.

3. In the **Enter a response** field, enter `I'm sorry, I don't understand. I can play classical, rhythm and blues, or rock music.`

![Music branch][10]

That takes care of all the cases where the user asked us to turn on the music.

### Test the dialog for music
In this section we'll test the conversation via the chat pane.

1. Select the ![Ask Watson][11] icon to open the chat pane.

2. Type `Play music`. The bot recognizes the `#turn_on` intent and the `@appliance:music` entity, and it responds by asking for a musical genre.

3. Type the name or a synonym for a valid `@genre` value (for example, `pop`). The bot recognizes the `@genre` entity and responds appropriately.

4. Type `Play music` again, but this time specify an invalid response for the genre.

The bot responds that it does not understand.

### Create nodes for other appliances
Next, we'll create a node that is used when the user specifies any other valid value for `@appliance`. For these other values of `@appliance`, we don't need to ask for any more input. We just give a positive response.

1. Select the _Music_ node, so that the options to create child and peer nodes are displayed.

2. Click the **options** icon ![Options][7] on the right-hand side of the _Music_ node and select **Add node below** to create a peer node.

3. Under **Enter a condition**, enter `@appliance`. This condition is triggered if the user input includes any recognized value for the `@appliance` entity, except music.

4. In the **Enter a response** field, enter `OK! Turning on the @appliance.` This response uses the value that the user entered.

Now add a peer node that will be triggered if the user input did not specify a valid appliance:

1. Click the **options** icon ![Options][7] on the right-hand side of the `@appliance` node and select **Add node below** to create a peer node.

2. Under **Enter a condition**, enter `true`. This condition specifies that if the dialog flow reaches this node, it should always evaluate as true. If the user specifies a valid `@appliance` value, this node will never be reached.

3. In the **Enter a response** field, enter `I'm sorry, I don't know how to do that. I can turn on music, headlights, or air conditioning.`.

### Test the dialog with other appliances
We will now test the conversation again with the other appliances.

1. Select the ![Ask Watson][11] icon to open the chat pane.

2. Type `Lights on`.

  The bot recognizes the `#turn_on` intent and the `@appliance:headlights` entity, and it responds with `OK, turning on the headlights`.

3. Type `Turn on the airco`.

  The bot recognizes the `#turn_on` intent and the `@appliance:(air conditioning)` entity, and it responds with `OK, turning on the air conditioning.`

4. Try variations on all of the supported commands based on the example utterances and entity synonyms you defined.

If the bot fails to recognize the correct intent, you can retrain it directly from the chat window. Select the incorrect intent and type the correct one in the field.

**Tip**: Don't include the `#` character when you type the intent name.

**Congratulations!!** You've built your own bot in a snap!! :smiley: :smiley:

### What to do next

Now that your bot is complete, you can experiment by enhancing it with new functions. For example:

* Define entities for additional appliances and musical genres
* Add synonyms for entities
* Add a new intent to turn off appliances
* Add capability for turning on music and specifying a musical genre with a single command

## Additional Links

* Botmaster Documentation : [Botmaster Documentation](http://botmasterai.com/)
* Watson Conversation Documentation : [Watson Conversation](http://www.ibm.com/watson/developercloud/doc/conversation/index.html)
* Watson Conversation Tutorial: [Watson Tutorial](https://www.ibm.com/watson/developercloud/doc/conversation/tutorial.html)

[1]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/create-pipeline.png?raw=true
[2]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/open-conversation.png?raw=true
[3]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/launch-tool.png?raw=true
[4]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/select-workspace.png?raw=true


[5]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/intents.png?raw=true
[6]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/close.png?raw=true
[7]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/options.png?raw=true
[8]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/jump-to-before.png?raw=true
[9]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/jump-to-after.png?raw=true
[10]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/music-branch.png?raw=true
[11]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/ask-watson.png?raw=true
[12]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/imported-workspace.png?raw=true
[13]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/terms.png?raw=true
[14]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/create-org.png?raw=true
[15]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/create-space.png?raw=true
[16]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/create-us-space.png?raw=true
[17]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/region.png?raw=true
[18]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/delivery-pipeline.png?raw=true
[19]: https://github.com/eciggaar/chatbot-lab/blob/master/readmeimages/view-app.png?raw=true
