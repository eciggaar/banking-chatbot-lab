# Build your own banking bot in a snap
Always wanted to know how to build your own banking chatbot? This workshop shows how to achieve this using the Watson Conversation API. You will start by:

* creating your own Watson Conversation service

* importing an existing configuration to give your bot a jump start

* extending the bot's functionality to your own discretion.

At the end of the workshop you have a bot capable of handling greetings, block your card and handle any other topic that you want your bot to be able to deal with.

## Before you begin
To use the Watson APIs, you need to have an IBM Cloud account. Your account must have enough resources available to create at least two services, one for the chatbot and one that -- under the surface -- automates the deployment of your bot.

## Step 1: Import the banking demo bot into your own cloud environment

1. First you need to login to IBM Cloud. For this ctrl-click [Login to IBM Cloud](https://console.bluemix.net/login) to open the login page in a separate tab. Then, login using the userid and password provided to your team.

2. Next, ctrl-click the `Get this bot` link below to get the banking demo bot into your own cloud environment. This will open the deployment page in a separate tab.

  <a href="https://console.bluemix.net/devops/setup/deploy?repository=https%3A//github.com/IBM/chatbot-deployer&chatbotName=Demo%20Banking%20Bot&chatbotWorkspaceURL=https://ibm.box.com/shared/static/rj2r8gf8vjdhc1o5x7rkynnsv1csjgry.json"><img src="https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/get-this-bot.png?raw=true" width="225"></a>

3. Then, click **Create** to actually start the creation.

  ![Create pipeline][1]

4. To open the Conversation Tooling environment, click the conversation tile.

  ![Open Conversation tile][2]

5. You should now see your dashboard with a conversation service called `Bot Asset Exchange Workspaces`. It might take a little while to get fully instantiated so refresh the page a couple of times if it does not show.

  ![Select service][5]

6. Click this service and then click **Launch tool**. Finally, click the **Login with IBM ID** button to go to the chatbot construction area.

  ![Launch tool][3]

7. In the available workspaces, select the **Banking Demo Bot** to start working with your bot.

  ![Select workspace][4]

You now successfully imported the demo banking bot and are ready to tweak it to your own needs.

## Step 2: Defining a new intent for the bot

An intent is a purpose or goal expressed in the user's input. For example, the user wants to report a lost card. In this section you create a new intent to handle lost cards.

1. After the successful import in the previous section, you should see the following screen in front of you.

  ![Imported workspace][6]

2. On the Intents tab, select **Add intent** and add the following intent name: `kaart_kwijt`. This intent will indicate that the user lost their card and wants the bank to block it. Click **Create intent** to create the intent.

  ![Create intent][7]

3. Next, in the **Add user examples** field, type `Ik ben mijn pas verloren` as sample sentence and press Enter.

  ![Add user examples][8]

4. Add these 4 more examples to help Watson recognize the `#kaart_kwijt` intent and hit Enter:
```
  Ik heb geen idee waar mijn bankpas is
  Ik ben mijn creditcard verloren
  Mijn pinpas is weg.
  Ik ben bang dat ik mijn credit card verloren ben.
```

5. Finally, click close at the top left of the intent creation page.

  ![Close intent creation][9]

You now defined an additional intent `#kaart_kwijt` with example utterances. These examples help train Watson to recognize the intents in the user's input.

## Step 3: Adding entities
An entity definition includes a set of entity _values_ that can be used to trigger different responses. Each entity value can have multiple _synonyms_, which define different ways that the same value might be specified in the user's input.

Create a new entity value to represent a new card type that the bot needs to recognize.

1. On your workspace page, click the Entities tab.

2. On the Entities tab, click the ` @card` entity type. The `@card` entity represents the different card types that the bot can handle.

  ![Select entity][11]

3. Add `debitcard` as value in the **Value name** field and click **Add value**.

  ![Add synomym value][10]

4. Add `betaalpas` in the **Synonyms** field as another way to specify the music appliance entity.

5. Complete the synonym list by adding the following synonyms.

    * Synonym: `bank pas`.

    * Synonym: `maestro`.

    * Synonym: `pinpas`.

You defined one entity `@card` with two values representing the supported card types. The next section explains how you can extend the existing dialog to use these intents and entities and how to define an appropriate response.

## Step 4: Extending your dialog
A dialog is a set of conversational nodes that are contained in a workspace. Together the set of nodes makes a dialog tree, on which every branch is a conversation that can be held with a user. A _Welkom, Begroeting_ and _In andere gevallen_ branch are already set up for you.

Now we are going to extend the dialog by adding a branch that responds to the `#kaart_kwijt` intent.

For this, we start by creating the root-level node:

1. First, switch to the dialog section by clicking the dialog tab.

2. Next,click the **options** icon ![Options][12] on the right-hand side of the `#greeting` node and select **Add node below** to create a new root-level node.

3. In the **Name this node** field, enter `Kaart kwijt`. The title does not affect the processing of the node, but it makes it easier to find.

4. In the edit view, in the **If bot recognizes** field, start typing `#kaart_kwijt`.

5. Select **#kaart_kwijt** from the list. This condition is triggered by any input that matches the `#kaart_kwijt` intent.

6. Next, in the **Then respond with** field, type the sentence the bot needs to respond with when this intent is recognized. For example, type `Dat is vervelend om te horen zeg....we zullen direct de kaart voor u blokkeren.`.

7. Click ![Close][13] to continue.

### Test the dialog
In this section we'll test the conversation via the chat pane.

1. Select the ![Ask Watson][18] icon to open the chat pane.

2. Type `Ik kan mijn pas nergens meer vinden.`. The bot recognizes the `#kaart_kwijt` intent and will respond with the message that the bank will take action and block the card.

3. To try greet the bot by typing for example `Hallo, hoe gaat het?`. Again, the bot will recognize the intent and respond as defined in the greeting node of the dialog.

4. Finally, type something completely off topic, like `Tegen wie moeten we morgen spelen?`.

The bot responds that it does not understand.

### Add multiple responses to the root node for `#kaart_kwijt`
Imagine the steps to block a credit card differ from the steps of blocking a debit card. To support this in the bot we will change the node to use so-called multiple responses. This approach allows us to change the response based on the type of card that is detected in the user's input.

1. Go to the browser tab that serves the Watson Conversation Tooling environment and select the **Dialog** tab. Then, click the `#kaart_kwijt` node to open it in edit mode. Click on **Customize** to open the node's customization settings.

  ![Node in Edit mode][14]

2. Enable the Multiple responses and click **Apply**.

  ![Node custom settings][15]

3. Now in the **If bot recognizes** field enter the `@card` entity with value `debitcard`. In the **Respond with** field enter the response that needs to be given in case a debit card is lost. For example `Wat vervelend dat u uw pinpas kwijt bent. Wij hebben uw kaart geblokkeerd. Met een paar dagen ontvangt u uw nieuwe pas.`.

  ![Conditional responses][16]

4. Click **Add response** to add a second response that handles lost credit cards. The response sentence could be something like `Gelukkig bent u hiervoor verzekerd. Uw kaart is per direct geblokkeerd.`.

5. Finally it could be that no card entity is detected in the user input. In that case we need more input and as the user for the type card that is lost. So for this add another response, provide no condition and define the following output `Dat is erg vervelend voor u. Om wat voor type kaart gaat het?`. The node should now look similar to

  ![Customized node][17]

6. Click ![Close][13] to continue.

### Test the dialog
Now that the dialog has been updated to handle different kind of lost cards, it is time to test it again. For this,

1. Select the ![Ask Watson][18] icon to open the chat pane.

2. Type `Ik kan mijn pinpas nergens meer vinden.`. The bot recognizes the `#kaart_kwijt` intent and the `@card` entity with value `debitcard`. It will respond with the defined message to block a lost debit card.

3. Now test again by saying that you've lost your credit card. The bot will recognize both the `#kaart_kwijt` intent and the `@card` entity with value `creditcard`. It should respond as defined for this part of the `#kaart_kwijt` node.

4. Finally, you can test the bot again for other purposes like greetings or something completely off topic. It might be that sentences are mapped to the incorrect intent due to the minimum amount of sample sentences. In that case, be aware that you can train your bot directly by mapping the sentence to the correct intent -- or mark it as _Irrelevant_ in case it is off topic. An example screenshot is given below.

  ![Train bot][19]

**Congratulations!!** You've built your own bot in a snap!! :smiley: :smiley:

### What to do next

Now that your bot is complete, you can experiment by enhancing it with new functions. For example:

* Define new entities for additional activities within the bank.
* Add new synonyms for existing entity values.
* Add a new intent (e.g. to reset your password or change your address details).

## Additional Links

* Botmaster Documentation : [Botmaster Documentation](http://botmasterai.com/)
* Watson Conversation Documentation : [Watson Conversation](http://www.ibm.com/watson/developercloud/doc/conversation/index.html)
* Watson Conversation Tutorial: [Watson Tutorial](https://www.ibm.com/watson/developercloud/doc/conversation/tutorial.html)

[1]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/create-pipeline.png?raw=true
[2]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/open-conversation.png?raw=true
[3]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/launch-tool.png?raw=true
[4]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/select-workspace.png?raw=true
[5]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/select-service.png?raw=true
[6]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/imported-workspace.png?raw=true
[7]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/create-intent.png?raw=true
[8]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/add-user-examples.png?raw=true
[9]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/close-intent.png?raw=true
[10]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/add-new-value.png?raw=true
[11]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/select-entity.png?raw=true
[12]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/options.png?raw=true
[13]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/close.png?raw=true
[14]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/node-in-edit-mode.png?raw=true
[15]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/node-custom-settings.png?raw=true
[16]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/conditional-responses.png?raw=true
[17]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/customized-node.png?raw=true
[18]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/ask-watson.png?raw=true
[19]: https://github.com/eciggaar/banking-chatbot-lab/blob/master/readmeimages/train-bot.png?raw=true
