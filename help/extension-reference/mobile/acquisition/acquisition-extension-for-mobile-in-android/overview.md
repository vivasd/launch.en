---
description: ''
---

# Acquisition Extension for Mobile in Android

Marketers can create tracking links to promote and drive traffic to their apps. These tracking links can then drive users to app stores, app deep links, and interstitials, which can be correlated to in-app behavior. A marketer can create separate links that route users to different platforms, such as iOS, Android, and so on.

You can create, edit, manage, and view reports on trackable mobile app marketing links.

Acquisition links with unique tracking codes can be generated in Adobe Mobile services. When a user downloads and runs an app from the app store after clicking on the generated link, the SDK automatically collects and sends the acquisition data to Adobe Mobile services.

If you cannot use the acquisition links that are created in Adobe Mobile Services, the acquisition data can still be collected and sent by the SDK by using Google Play Acquisition.

To collect acquisition data from a standard Google Play Acquisition campaign:

* Use the standard Google Play Store acquisition method. Custom acquisition data can be used with the standard Google Play Acquisition key value pairs.
* When the user downloads and runs an app as the result of a Google Play store acquisition, the data from the referrer will be collected and sent to Adobe Mobile Services.

If you are using the Acquisition links that were created on Adobe Mobile Services, add custom data to the acquisition link by completing the following tasks:

* Prefix an acquisition variable with `adb`.
* The custom data keys will be prefixed with `a.acquisition.custom`.

**Tip**: If you are sending data to multiple report suites, use the acquisition data from the app that is associated with the first report suite in your list of report suite IDs.

The updates in this section enable the Adobe Cloud Platform SDKs to send acquisition data from an acquisition link.

## Tracking Mobile Acquisition in Android

Here is information about how to configure and use Acquisition with the Android library.

1. Add the library to your project.
1. Import the library:

   `import com.adobe.marketing.mobile.*;`

1. Update AndroidManifest.xml to enable the `BroadcastReceiver` that was created in the previous step:

   ```text
   <receiver
      android:name="com.adobe.marketing.mobile.AdobeMobileSdkReceiver"
      android:exported="true">
      <intent-filter>
          <action android:name="com.android.vending.INSTALL_REFERRER" />
      </intent-filter>
   </receiver>
   ```

1. Verify that ADBMobileConfig.json contains the required acquisition settings:

   ```text
   {
      "acquisition.server": "c00.adobe.com",
      "acquisition.timeout": 5,
      "acquisition.appid": "714c3a0216700cc3656d6ea6ea69b96d9cfb52c3a5b5fdab42de42d74867588e",
    ...
   }
   ```

>[!IMPORTANT]  If you are sending data to multiple report suites, use the acquisition settings \(acquisition server and appid\) from the app that is associated with the first report suite in your list of report suite IDs.

The acquisition settings are generated by Adobe Mobile services and should not be changed.

>[!IMPORTANT]  `acquisition.timeout` must be set to a value higher than 0 to enable app acquisition.

## Tracking Deep Links in Android

You can use this information to track deep and deferred deep links in your mobile apps by using the Adobe Cloud Platform SDKs.

1. Add the Adobe Cloud Platform SDKs to your project.
1. Pass Activity with deep link intent to Adobe SDK by using `collectLaunchInfo`.

   Here is a sample for track deep link:

   ```java
   public class ParseDeepLinkActivity extends Activity {
        @Override
        protected void onCreate\(Bundle savedInstanceState\) {
             super.onCreate(savedInstanceState);

    Acquisition.collectLaunchInfo(this);

     }

   }
   ```

The Adobe Cloud Platform SDKs can parse key and value pairs of data that is appended to any deep or universal link as long as the link contains a key with the`a.deeplink.id` label and a corresponding non-null and user-generated value. All key and value pairs of data that are appended to the link will be parsed, attached to a lifecycle hit, and sent to Adobe Analytics as long as the link contains the `a.deeplink.id` key and value.

Additionally, you might append one or more of the following reserved keys \(with user-generated values\) to the deep or universal link:

* a.launch.campaign.trackingcode
* a.launch.campaign.source
* a.launch.campaign.medium
* a.launch.campaign.term
* a.launch.campaign.content

These keys are pre-mapped variables for reporting in Adobe Analytics.

### Tracking Deferred Deep Links \(for use with Marketing Links\)

With a deferred deep link, the Adobe SDK will open a new Intent with the deep link as the intent data. This process is handled as an external deep link using the code above.

### Tracking Third-Party Deferred Deep Links in Android

Use the Adobe Cloud Platform SDKs to implement the tracking third-party deferred deep links.

#### Classic Adobe Mobile SDK Deep Linking

The Adobe Cloud Platform SDKs currently supports deep linking where the app developer is expected to use the `collectLaunchInfo(Activity)` API from the deep linked activity. The SDK appends the deep link data from the deep link URL parameters.

#### Facebook Deep Linking

An ad creator can create an ad on Facebook as a deep link. When users click the ad, it goes directly to the information in which they are interested in the app. The deep link, is not a fingerprinter URL. However, during ad configuration, there is an option to provide a third-party deep link URL. An app developer who is using the Adobe Mobile SDKs and services is expected to enter the Adobe Mobile Service-configured fingerprinter URL in this field. If everything is set up correctly, the Facebook SDK passes this URL to the application when the app is installed or launched.

**Setting up the SDKs in Android**

You can set up the Adobe Cloud Platform SDKs to add Facebook deep linking support .

If the application is set up correctly, `trackAdobeDeepLink()` enables collecting the deep link information from the Facebook acquisition campaign and sends the information to Adobe Analytics. If the install hit has not been sent to Adobe Analytics at the first launch, this information will be added to the Lifecycle hit. Otherwise, it will be sent as an deep link Adobe Analytics hit.

**Tip**: Ensure that the deep link URL has a key called `a.deeplink.id`. If the URL is missing the deep link ID parameter, the URL parameters will not be appended to the context data.

If the link can be attributed to an acquisition, the Adobe Cloud Platform SDKs will store the acquisition data from the Facebook deep link that was used to call `trackAdobeDeepLink()`. This data will be available to the Adobe Cloud Platform SDKs in future launches.

**Enable Deep Linking in your Android Application**

1. Register the application to handle deep links. For more information, see _Allowing Other Apps to Start your Activity_.
1. Link the Facebook SDKs. To add the Facebook gradle dependency in the app, complete the steps in _Getting Started Android SDK_.
1. To initialize the Facebook SDK, complete the instructions in _Android Studio Setup_.
1. Call `trackAdobeDeepLink()` from the main activity.

   ```java
   @Override
   protected void onResume() {
      super.onResume(); AppEventsLogger.activateApp(this);
      /*
      *Adobe Tracking - Config
      *call collectLaunchInfo(Activity) () to begin collecting lifecycle data
      *must be in the onResume() of every activity in your app
      */
      Acquisition.collectLaunchInfo(Activity) (this);

   AppLinkData.fetchDeferredAppLinkData(this,
         new AppLinkData.CompletionHandler() {
            @Override
            public void onDeferredAppLinkDataFetched(AppLinkData appLinkData) {
               // Process app link data
               if (appLinkData != null {
                     Acquisition.trackAdobeDeepLink(appLinkData.getTargetUri());
               }
            }
         }
     };
   }
   ```
