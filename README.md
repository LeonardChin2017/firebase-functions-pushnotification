# firebase-functions-pushnotification

## How?
1. Set up Node.js 
   
   Download Node.js from https://nodejs.org/en/

2. Set up Firebase CLI

        $ npm install -g firebase-tools
  
3. Initialize Firebase SDK for Cloud Functions

        $ firebase login
        $ firebase init functions
        
4. Paste the following code to functions/index.js

        //import firebase functions modules
        const functions = require('firebase-functions');

        //import admin module
        const admin = require('firebase-admin');
        admin.initializeApp(functions.config().firebase);


        // Listens for new messages added to messages/:pushId
        exports.pushNotification = functions.database.ref('/messages/{pushId}').onWrite( event => {

            console.log('Push notification event triggered');

            //  Grab the current value of what was written to the Realtime Database.
            var valueObject = event.data.val();

            if(valueObject.photoUrl != null) {
              valueObject.photoUrl= "Sent you a photo!";
            }

          // Create a notification
            const payload = {
                notification: {
                    title:valueObject.msgUser,
                    body: valueObject.msgText || valueObject.photoUrl,
                    sound: "default"
                },
            };

          //Create an options object that contains the time to live for the notification and the priority
            const options = {
                priority: "high",
                timeToLive: 60 * 60 * 24
            };


            return admin.messaging().sendToTopic("pushNotifications", payload, options);
        });
        
5. Deploy the function
   
       $ firebase deploy

