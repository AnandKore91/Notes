# Local and Remote Notification
**Local notifications and remote notifications are ways to inform users when new data becomes available for your app, even when your app is not running in the foreground.**

* With **Local Notifications**, your app configures the notification details locally and passes those details to the system, which then handles the delivery of the notification when your app is not in the foreground. Local notifications are supported on iOS, tvOS, and watchOS.

* With **Remote Notifications**, you use one of your company’s servers to push data to user devices via the Apple Push Notification service. Remote notifications are supported on iOS, tvOS, watchOS, and macOS.

* Both local and remote notifications require you to add code to support the scheduling and handling of notifications in your app.

* For remote notifications, you must also provide a server environment that is capable of receiving data from user devices and sending notification-related data to the Apple Push Notification service (APNs), which is an Apple-provided service that handles the delivery of remote notifications to user devices.

## When to Use Local and Remote Notifications
**Local notifications** provide a way to alert the user when your app has new information to present.
* Example:
    Local notifications are also well suited for apps such as calendar and to-do list apps that need to alert the user at a specific time or when a specific geographic location is reached.

**Remote notifications** are appropriate when some or all of the app’s data is managed by your company’s servers. With remote notifications, you decide when you want to push a notification to the user’s device.
* Example:
    A messaging app would use remote notifications to let users know when new messages arrive.

## Apple Push Notification service (APNs)    
**Apple Push Notification service (APNs)** is the centerpiece of the remote notifications feature. It is a robust, secure, and highly efficient service for app developers to propagate information to iOS (and, indirectly, watchOS), tvOS, and macOS devices.

To be able to receive and handle remote notifications, your app must:

1. Enable remote notifications.
2. Register with Apple Push Notification service (APNs) and receive an app-specific device token.
3. Send the device token to your notification provider server.
4. Implement support for handling incoming remote notifications.

**NOTE**
* The ability of APNs to deliver remote notifications to a nonrunning app requires the app to have been launched at least once.
* On an iOS device, if a user force-quits your app using the app multitasking UI, the app does not receive remote notifications until the user relaunches it.


## Enabling the Push Notifications Capability
1. Open project in Xcode.
2. From project navigator, select project.
3. Then, select `project target`.
4. Then, select `Capabilities`.
5. Enable `Push Notifications`.
*Apps without required entitlements are rejected during the App Store review process. During testing, trying to register with APNs without the proper entitlement returns an error.*

## Registering to Receive Remote Notifications
Each time your app launches, it must register with APNs. The methods to use differ according to the platform, but in all cases it works as follows:
1. Your app asks to be registered with APNs.
2. On successful registration, APNs sends an app-specific device token to the device.
3. The system delivers the device to your app by calling a method in your app delegate.
4. Your app sends the device token to the app’s associated provider.

**Sample (Objective-C):**

```
- (void)applicationDidFinishLaunching:(UIApplication *)app {
    // Configure the user interactions first.
    [self configureUserInteractions];

   // Register for remote notifications.
    [[UIApplication sharedApplication] registerForRemoteNotifications];
}

// Handle remote notification registration.
- (void)application:(UIApplication *)app
        didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)devToken {
    // Forward the token to your provider, using a custom method.
    [self enableRemoteNotificationFeatures];
    [self forwardTokenToServer:devTokenBytes];
}

- (void)application:(UIApplication *)app
        didFailToRegisterForRemoteNotificationsWithError:(NSError *)err {
    // The token is not currently available.
    NSLog(@"Remote notification support is unavailable due to error: %@", err);
    [self disableRemoteNotificationFeatures];
}
```
**Sample (Swift):**
```
func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    // Configure the user interactions first.
    self.configureUserInteractions()

    // Register with APNs
    UIApplication.shared.registerForRemoteNotifications()
}

// Handle remote notification registration.
func application(_ application: UIApplication,
                 didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data){
    // Forward the token to your provider, using a custom method.
    self.enableRemoteNotificationFeatures()
    self.forwardTokenToServer(token: deviceToken)
}

func application(_ application: UIApplication,
                 didFailToRegisterForRemoteNotificationsWithError error: Error) {
    // The token is not currently available.
    print("Remote notification support is unavailable due to error: \(error.localizedDescription)")
    self.disableRemoteNotificationFeatures()
}
```

5. **On successful** issuance of an app-specific device token, the system calls the `application:didRegisterForRemoteNotificationsWithDeviceToken:` method. Implement this method to receive the token and forward it to your provider.

6. **On error**, the system calls the `application:didFailToRegisterForRemoteNotificationsWithError:` method. Implement this method to respond to APNs registration errors.

## Developer's Responsibility
An app-specific device token is globally unique and identifies one app-device combination.

1. Open a network connection to your provider.
2. Forward the device token along with any other relevant data you want to send to the provider.
3. When the provider later sends remote notification requests to APNs, it must include the device token, along with the notification payload.
4. Never cache device tokens in your app; instead, get them from the system when you need them.

## Note
1. APNs issues a new device token to your app when certain events happen. The device token is guaranteed to be different.
2. When a device token has changed, the user must launch your app once before APNs can once again deliver remote notifications to the device.
3. Apps running on watchOS do not register for remote notifications explicitly. Instead, they rely on their paired iPhone to forward remote notifications for display on the watch.
4. The forwarding of remote notifications happens when the iPhone is locked (or the screen is asleep) and the Apple Watch is on the user’s wrist and unlocked.
5. APNs device tokens are of variable length. Do not hard-code their size.
6. After successful APNs registration, the app object contacts APNs only when the device token has changed; otherwise, calling the `registerForRemoteNotifications` method results in a call to the `application:didRegisterForRemoteNotificationsWithDeviceToken:` method which returns the existing token quickly.
7. If the device token changes while your app is running, the app object calls the `application:didRegisterForRemoteNotificationsWithDeviceToken:` delegate method again to notify you of the change.

## Handling Remote Notifications
Your app receives the payload of a remote notification through its app delegate. When a remote notification arrives, the system handles user interactions normally when the app is in the background.

Some examples of tasks
* If your app is in the foreground, you can receive the notification directly and silence it.
* If your app is in the background or not running:
    * You can respond when the user selects a custom action associated with a notification.
    * You can respond when the user dismisses the notification or launches your app.

In iOS and tvOS, the system delivers the notification payload to the `application:didReceiveRemoteNotification:fetchCompletionHandler:` method of the app delegate. In macOS, the system delivers the payload to the `application:didReceiveRemoteNotification:` method of the app delegate.
