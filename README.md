﻿# Kore.ai SDK
Kore.ai offers Bots SDKs as a set of platform-specific client libraries that provide a quick and convenient way to integrate Kore.ai Bots chat capability into custom applications.

With just few lines of code, you can embed our Kore.ai chat widget into your applications to enable end-users to interact with your applications using Natural Language. For more information, refer to https://developer.kore.ai/docs/bots/kore-web-sdk/

# Kore.ai Web SDK for developers

Kore.ai SDK for web enables you to talk to Kore.ai bots over a web socket. This repo also comes with the code for sample application that developers can modify according to their Bot configuration.

#Supported Browsers
1. Google Chrome version 55 & above
2. Mozilla Firefox version 51 & above
3. Internet Explorer version 11 & above 
(Web-sdk may not work properly incase compatability mode is on with IE older versions)
4. Mac Safari version 9.1 & above
5. Opera version 48 & above

#Supported JQuery version 2.1.4

# Setting up

### Prerequisites
* Service to generate JWT (JSON Web Tokens)- SDK uses this to send the user identity to Kore.ai Platform.
* SDK app credentials 
    * Login to the Bots platform
    * Navigate to the Bot builder
    * Search and click on the bot 
    * Enable *Web / Mobile Client* channel against the bot as shown in the screen below.
    ![Add bot to Web/Mobile Client channel](https://github.com/mandarudg/Tst/blob/master/channels.png)
    * create new or use existing SDK app to obtain client id and client secret
    ![Obtain Client id and Client secret](https://github.com/mandarudg/Tst/blob/master/web-mobile-client-channel.png)

* Service to generate JWT (JSON Web Tokens)- this service will be used in the assertion function injected to obtain the connection.

## Instructions
Integration of Kore.ai chat UI into your App

#### 1. Include Dependent CSS
    -   <link href="UI/libs/jquery-ui.min.css" rel="stylesheet"/>
    -   <link href="UI/chatWindow.css" rel="stylesheet"/> // chat ui design
	-   <link href="../libs/purejscarousel.css" rel="stylesheet"></link> // carousel template design

#### 2. Include Dependent JS
    -   <script src='UI/libs/jquery.js'></script>
    -   <script src='UI/libs/jquery-ui.min.js'></script>
    -   <script src='UI/libs/jquery.tmpl.min.js'></script>
    -   <script src='UI/libs/moment.js'></script>
    -   <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/2.4.1/lodash.min.js"></script>

#### 3. Include the kore-bot-sdk-client.js ,anonymousassertion.js & chatWindow.js files 
    -   <script src='../libs/anonymousassertion.js'></script>
    -   <script src='../kore-bot-sdk-client.js'></script>
    -   <script src='UI/chatWindow.js'></script> // chat ui js

#### 4. Include dependencies for recorder , emoji and carousel template support
    -   <script src="../libs/emoji.js" type="text/javascript"></script>
    -   <script src="../libs/recorder.js" type="text/javascript"></script>
    -   <script src="../libs/recorderWorker.js" type="text/javascript"></script>
	-   <script src="../libs/purejscarousel.js" type="text/javascript"></script>
    -   <script src="custom/customTemplate.js" type="text/javascript"></script>
    -   <link href="custom/customTemplate.css" rel="stylesheet"></link>

#### 5. Define the assertion function (Should be defined by the clients)
        //NOTE:clients has to define a API which should generate and return the JWT token. and do the necessary changes in the below function like change the url,type,Authorization and on success set the returned jwt.
        //fields to set in JWT:subject(emailId),issuer(clientId),algorithm(HS256 or RS256)
    -   function assertion(options, callback) {
        //client has to fill the claims and call the callback.
        $.ajax({
        url: "https://hostname/api/users/sts", //client api which should return the signed jwt token.
         type: 'post',
         headers: {
            Authorization: "bearer K*****************************8"
                  },
         dataType: 'json',
         success: function(data) {
            options.assertion = data.jwt
            callback(null, options);
            }
           }) 
        }

#### 6. Initialize the Bot
        //Define the bot options
        var botOptions = {};
        botOptions.koreAPIUrl = "https://bots.kore.ai/api/";
    botOptions.speechSocketUrl = 'wss://';
        botOptions.assertionFn = assertion;
        botOptions.koreAnonymousFn = koreAnonymousFn;
        botOptions.clientId   = "5a37bf24-fea0-4e6b-a816-f9602db08149"; // issued by the kore.ai on client app registration.
        botOptions.botInfo = {chatBot:"Kora",taskBotId :"st-*********"};  
        // Assign Bot options to chatWindow config
        var chatConfig={
            botOptions:botOptions,
            allowIframe : false, // set true, opens authentication links in popup window, default value is "false"
			isSendButton: false, // set true, to shown send button below the compose bar
			isTTSEnabled: true, // set false, to hide speaker icon
			isSpeechEnabled: true // set false, to hide mic icon
            allowGoogleSpeech : true // set false, to use KORE.ai speech engine instead Google speech engine. This feature requires valid Google speech API key. (Place it in 'web-kore-sdk/libs/speech/key.js')
        };

#### 7. Call koreBotChat instance
        var chatInstance = koreBotChat(); // get chat instance
        chatInstance.show(chatConfig); // open chat window
        chatInstance.destroy(); // for destroying chat window instance

# How to create your own ui with kore.ai chat bot api 

#### 1. Include the kore-bot-sdk-client.js & dependencies
    -   <script src='jquery.js'></script>
    -   <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/2.4.1/lodash.min.js"></script>
    -   <script src='../libs/anonymousassertion.js'></script>
    -   <script src='../kore-bot-sdk-client.js'></script>

#### 2. Initialize the Bot
        //define the bot options
        var botOptions = {}; 
        botOptions.koreAPIUrl = "https://bots.kore.ai/api/";
        botOptions.koreSpeechAPIUrl = "https://speech.kore.ai/";
		botOptions.ttsSocketUrl = 'wss://speech.kore.ai/tts/ws';
        botOptions.assertionFn = assertion;
        botOptions.koreAnonymousFn = koreAnonymousFn;
        botOptions.clientId   = "5a37bf24-fea0-4e6b-a816-f9602db08149"; // issued by the kore.ai on client app registration.
        botOptions.botInfo = {chatBot:"Kora",taskBotId :"st-*********"};  
        var bot = require('/KoreBot.js').instance(); //initialize the bot.
        bot.init(botOptions); // bot instance created.
        bot.destroy(); // Destroy bot instance 

#### 3. Send message to Bot
        var messageToBot = {};
        messageToBot["message"] = {body:"your message",attachments:[]};
        messageToBot["resourceid"] = '/bot.message';
        // Should be an uniqe id for each message, you can use timestamp as well
        messageToBot["clientMessageId"] = 232341234; 
        // Send message to Bot
        bot.sendMessage(messageToBot, function messageSent() {
        });

#### 4. Listen to a Message (Response)
        // Event occurs when you recieve any message from server
        bot.on("message",function(msg){
            console.log("Received Message::",msg.data);
            // Converting JSON string to object
            var dataObj = JSON.parse (msg.data); 
            //differ user message & bot response check message type
            if(dataObj.from === "bot" && dataObj.type === "bot_response") {
                // Bot sends a message to you
            }
            if(tempData.from === "self" && tempData.type === "user_message") {
                // If you are logged in on several devices at the same time & sent a message to same Bot, it will appear on every device
            }
        });

# How to check connection established with bot

        // Open event triggers when connection established with bot
        bot.on("open", function (response) {
            // your code
        });



```

###Release History:

V6.0.0 [Major] on 04-NOV-2017: Master branch
    Released major release with following features:
    1. Added Pie chart, Line chart, Bar chart and Table support
    2. Added Google Speech to text support. 
        This feature requires valid Google speech API key. (Place it in 'web-kore-sdk/libs/speech/key.js')  
        (Supported Browsers are Chrome, Firefox, Microsoft Edge, Safari 11.
        Firefox, Microsoft Edge, Safari 11 requires Google API key)
    3. Updated chatConfig options to turn on/off Google Speech to Text.

V5.0.0 [Major] on 12-OCT-2017: web-sdk-5.0.0 branch
    1. Added Quick Replies, List & Carosuel templates support
    2. Added in-line video playback support

# License
_Copyright © Kore.ai, Inc. MIT License; see LICENSE for further details._