# Set up Project Griffon

## **Configure the Project Griffon extension in** Experience Platform **Launch**

{% hint style="warning" %}
This extension is in beta. Use of this beta product requires acceptance of terms outlined on [https://griffon.adobe.com](https://griffon.adobe.com).
{% endhint %}

1. In Experience Platform Launch, click the **Extensions** tab.
2. On the **Catalog** tab, locate the **Project Griffon** extension, and click **Install**.
3. Follow the publishing process to update SDK configuration.

### **Install the Project Griffon extension**

![](../../.gitbook/assets/pg-launch.png)

Click **Install** on the extension card. No extension settings are required.

## Add Project Griffon to your app

{% tabs %}
{% tab title="Android" %}
1. Add the following libraries in your project's `build.gradle` file:
`implementation 'com.adobe.marketing.mobile:core:1.4.0'`
`implementation 'com.adobe.marketing.mobile:griffonbridge:1.0.1'`. 
 Also be sure to use the latest available versions.
2. Import the Project Griffon libaries along with other SDK libaries:
#### Java

```
import com.adobe.marketing.mobile.AndroidGriffonBridge;
import com.adobe.marketing.mobile.MobileCore;
```
3. Configure deep link
```xml
<activity
    android:name=".MainActivity"
    android:label="@string/app_name"
    android:theme="@style/AppTheme.NoActionBar" >
    <intent-filter>
      <action android:name="android.intent.action.MAIN" />
      <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Example URI "griffonbridge://host?adb_validation_sessionid=12fbc3d9-f405-6a7e-8cce-f91123f299df” -->
        <data android:scheme="griffonbridge" android:host="host" />
    </intent-filter>
</activity>
<activity android:name="com.adobe.griffon.FullScreenTakeoverActivity" />
```

{% endtab %}

{% tab title="iOS" %}
1. Add the library to your project via your Cocoapods `Podfile` by adding `pod 'ACPGriffonBeta'` ​
2. Import the Project Griffon libraries along with other SDK libraries:

#### Objective-C

```objectivec
#import "ACPCore.h"
#import "ACPAnalytics.h"
#import "ACPGriffonBridge.h" // <-- import the Project Griffon library
```

#### Swift

```swift
import ACPCore
import ACPAnalytics
import ACPGriffonBeta // <-- import the Project Griffon library
```
{% endtab %}
{% endtabs %}

### Register Project Griffon with Mobile Core

{% tabs %}
{% tab title="Android" %}
Registering the extension with Core sends Experience Platform SDK events to an active Project Griffon session. To start using the extension library, you must register the extension with the [Mobile Core](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/mobile-core) extension.

#### Java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    AndroidGriffonBridge.setup(getApplication());

    final Intent intent = getIntent();
    final Uri data = intent.getData();

    if (data != null) {
        MobileCore.setApplication(getApplication());
        AndroidGriffonBridge.registerExtension();
        Map<String, Object> config = new HashMap<>();
        config.put("lifecycle.timeout", 5);
        MobileCore.updateConfiguration(config);
        MobileCore.start(null);
    }
}
```

{% endtab %}

{% tab title="iOS" %}
Registering the extension with Core sends Experience Platform SDK events to an active Project Griffon session. To start using the extension library, you must register the extension with the [Mobile Core](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/mobile-core) extension.

#### Objective-C

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [ACPCore configureWithAppId:@"yourAppId"];
    [ACPAnalytics registerExtension];
    [ACPIdentity registerExtension];
    [ACPGriffonBridge registerExtension]; // <-- register Project Griffon with Core
    [ACPCore start:nil];
    // Override point for customization after application launch.
    return YES;
 }
```

#### Swift

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
     ACPCore.configure(withAppId: "yourAppId")   
     ACPAnalytics.registerExtension()
     ACPIdentity.registerExtension()
     ACPGriffonBridge.registerExtension() // <-- register Project Griffon with Core
     ACPCore.start(nil)
     // Override point for customization after application launch. 
     return true;
}
```
{% endtab %}
{% endtabs %}

### Start a Project Griffon session

Once the extension has been registered, you may begin a Project Griffon session using the following API.

{% tabs %}
{% tab title="Android" %}
### startSession

This API accepts a deep link in order to begin a session. When this API is called, the SDK will display a pin authentication overlay on your app to begin a session.

#### Java

#### Syntax
````text
public static void startSession(final String url)
````

#### Example
```java
 AndroidGriffonBridge.startSession(url);
```
{% endtab %}

{% tab title="iOS" %}
### startSession

This API accepts a deep link in order to begin a session. When this API is called, the SDK will display a pin authentication overlay on your app to begin a session.

#### Objective-C

#### Syntax

```text
+ (void) startSession: (NSURL* _Nonnull) url;
```

#### Example

```objectivec
- (BOOL)application:(UIApplication *)app openURL:(nonnull NSURL *)url options:(nonnull NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {
    [ACPGriffonBridge startSession:url];
    return false;
}
```

#### Swift

#### Syntax

```text
class func startSession(_ url: URL) {
}
```

#### Example

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    do {
        ACPGriffonBridge.startSession(url)
        return false
    }
}
```
{% endtab %}
{% endtabs %}

### End a Project Griffon session

You can end a session in the app interface by pressing the floating indicator and selecting **Disconnect**. You can also programmatically close an active session by using the following API.

{% tabs %}
{% tab title="Android" %}
### endSession

This API ends the active session and will ensure that no data is sent to a Project Griffon session.

#### Java
````java
AndroidGriffonBridge.endSession()
````

{% endtab %}

{% tab title="iOS" %}
### endSession

This API ends the active session and will ensure that no data is sent to a Project Griffon session.

#### Objective-C

```objectivec
[ACPGriffon endSession];
```

#### Swift

```swift
ACPGriffon.endSession()
```
{% endtab %}
{% endtabs %}

### Send custom events

You can send custom events from the app to Project Griffon using the following API. Sending custom events can help inspect information from the app such as API and network responses, foreground and background activity, asset and media downloads, performance metrics, timed processes, app startup times, or screen load times.

{% tabs %}
{% tab title="Android" %}
### sendEvent

This API is for sending custom events.

#### Java

#### Syntax
````java
AndroidGriffonBridge.sendEvent(Event event);
````

#### Example
````java
final Map<String, Object> eventPayload = new HashMap<>();
eventPayload.put("Test Event Key", "Test Event Value");
final Event newEvent = new Event("GRIFFON", "SINGLE_EVENT_TEST", eventPayload);
AndroidGriffonBridge.sendEvent(newEvent);
````
{% endtab %}

{% tab title="iOS" %}
### sendEvent

This API is for sending custom events.

#### Objective-C

#### Syntax

```objectivec
[ACPGriffon sendEvent: NSDictionary];
```

#### Example

The following example shows you how to send a custom event that measures the download time of an asset download activity in the app.

```objectivec
CFAbsoluteTime downloadStartTime = CFAbsoluteTimeGetCurrent();

CFAbsoluteTime totalDownloadTime = CFAbsoluteTimeGetCurrent() - downloadStartTime;
        ACPGriffonEvent* griffonDownloadEvent = [[ACPGriffonEvent alloc] initWithVendor:@"com.adobe.myapp"
                                                                                   type:@"download info"
                                                                                payload:@{
                                                                                    @"time" : @(totalDownloadTime),
                                                                                    @"size" : @(data.length)
                                                                                }];
        [ACPGriffon sendEvent: griffonDownloadEvent];
```

#### Swift

#### Syntax

```swift
ACPGriffon.sendEvent(NSDictionary)
```

#### Example

```swift
var downloadStartTime: CFAbsoluteTime = CFAbsoluteTimeGetCurrent()

var totalDownloadTime: CFAbsoluteTime = CFAbsoluteTimeGetCurrent() - downloadStartTime
var griffonDownloadEvent = ACPGriffonEvent(vendor: "com.adobe.myapp", type: "download info", payload: [
    "time": NSNumber(value: totalDownloadTime),
    "size": NSNumber(value: data.length)
])
ACPGriffon.sendEvent(griffonDownloadEvent)
```
{% endtab %}
{% endtabs %}
