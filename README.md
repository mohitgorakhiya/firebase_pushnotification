# firebase_pushnotification

Here I am going to explain step by step implementation for the Push Notification.

1) iTunes Connect Side:

- For push notification it requires to create push certificate, for that you need to have Developer account.
— First create your iOS app Id.
- Create Push certificate private key from Developer Portal and download it to PC.

2) Google Firebase Side: 

First you have to create your new iOS app with Proper Bundle Identifier on Google Firebase https://console.firebase.google.com

-> Then it will display steps to integrate Firebase into our app and Need to Download “GoogleService-Info.plist” file and add that file to your iOS Project. Make sure that file name is “GoogleService-Info”.

-> In this file all configuration are already added by Google Firebase only so no any change require from our side.

-> Upload push certificate private key to Google Firebase account under your app. (Path: Your iOS App  Cloud Messaging Tab) At bottom you will find option to add Private Key file.
For Development and Distribution need to upload private certificate separately.

3) From Xcode Project Side:

- Create Swift Project
- Install cocoa pod for Firebase
pod 'Firebase/Core'
pod 'Firebase/Messaging'

- If you are new to cocoa pod then you can do using 
https://cocoapods.org/

Add below line
import UserNotifications
import Firebase
in AppDelegate file

-> FirebaseApp.configure()
   Messaging.messaging().delegate = self

self.RequestNotificationAuthorization(application: application)
Add above 3 lines in didFinishLaunchingWithOptions method.
First 2 lines are Firebase default methods
And 3rd method is where we register for the push notification

Here is implementation of the “RequestNotificationAuthorization” method

func RequestNotificationAuthorization(application: UIApplication) 
{
        if #available(iOS 10.0, *) 
        {
            UNUserNotificationCenter.current().delegate = self
            let authOptions: UNAuthorizationOptions = [.alert, .badge, .sound]
            UNUserNotificationCenter.current().requestAuthorization(options: authOptions, completionHandler: {_, _ in })
        } 
        else 
        {
            let settings: UIUserNotificationSettings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
            application.registerUserNotificationSettings(settings)
        }
}

For iOS 10 and Above there is new framework available to register for push notification so we have differentiate Registration in this method.

-> When push notification Registeration will completes then “didRegisterForRemoteNotificationsWithDeviceToken” method will be called and set “deviceToken” in Firebase Messaging apnsToken token like below line

Messaging.messaging().apnsToken = deviceToken

->  I have taken 1 variable in AppDelegate “pushToken” which will be used throughout the app where we require push token

-> There is Firebase MessagingDelegate method available 

“func messaging(_ messaging: Messaging, didRefreshRegistrationToken fcmToken: String)”

In this method we need to set fcmtoken into our variable

“self.pushToken = fcmToken”

But this method will be called only once and when Token will refresh so next time we must get it using 

if (Messaging.messaging().fcmToken != nil)
{
     self.pushToken = Messaging.messaging().fcmToken
}

in AppDelgate DidfinishLaunching Method.

4) Test Push notification using Firebase:

-> Get push token by running the app
-> Firebase also provides to test push notification from their portal, you will find that option on left side bar at bottom “ Notification”
-> Click on New Message and it will give popup
-> Message text : Enter Message Text
-> Message label (optional) : it is optional
-> Delivery date: Send Now
-> Target : Single Device
-> FCM registration token: Enter registration token that we get after running app.
-> Click on Send Message at right bottom corner.

-> Wait for the notification to display on your Device.

Note: For push notification to test it requires real iOS Device, According to apple document push notification not works with Simulator.
