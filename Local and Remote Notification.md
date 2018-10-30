# Local and Remote Notification

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
