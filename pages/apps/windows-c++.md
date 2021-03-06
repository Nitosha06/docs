## Overview

### SDK Details

!!! info "Current SDK Version 1.1.0"

### Branch-integrated Demo Apps

There are several demo apps embedded in this repository, which you can find in the ***BranchSDK-Samples*** folder.
Please use these as a reference.

Should you run into an issue with the SDK, please check that it is present in the demo app before submitting any issue
as a bug.

- Console HelloBranch -- The most basic application to demonstrate Branch Initialization and basic attribution.
- Console Example -- A menu driven application with more advanced functionality.
- Windows Color Picker -- Windows app to demonstrate Sharing and Deep Linking.

## Branch links and Branch keys

### Format of Branch links

Branch links consist of an app-specific base domain (*testbed-windows.app.link*, for example) followed by an alphanumeric
suffix. This suffix, referred to as the *alias*, can be either randomly generated (*FCIWxFeBGx*, for example) or
manually-defined (*testlink*, for example). The app-specific base domain used to create the links is termed the
*Branch link domain*.

A Branch-integrated app called ColorPicker and assigned the default Branch link domain *uxzce.app.link* has
the following Branch link examples:

- https://uxzce.app.link/o599TjuIK1 (Resolves to a Teal color)
- https://uxzce.app.link/39nO6CAIK1 (Resolves to a Purple color)

Note that when using the 'app.link' default link domain there is a second, alternate, form of the link domain that comes
into play when displaying Branch Deepviews - the *alternate link domain*. This *alternate link domain* sees the domain's
'.app.link' string replaced by '-alternate.app.link'. If you are using a default app.link link domain, both the regular
and alternate domains will be needed when configuring a project to support deep linking.

### Branch keys

Branch keys consist of a prefix (**key_live_** or **key_test_**) followed by a string of 32 aphanumeric characters
(*key_live_kdzhBMBYt5Pi4g4DVRfQbdkbqDlm5rIv*, for example). Branch keys are used to associate apps with Branch links.
An app's Branch key is required to initialize the Branch SDK. Data associated with Branch links can only be read by apps
that  use the same Branch key.

Note that the **Live** and **Test** apps on the Branch dashboard are completely separate; each has its own Branch key.
Even when **Live** and **Test** sections of the dashboard have been configured identically and links from both are able
to open up a particular app, the app will only receive link data for which it has the matching Branch key.

### Creating a Branch link domain and Branch key for an app

Register the app on the Branch dashboard, here: [https://dashboard.branch.io](https://dashboard.branch.io)

The Branch key can be found on the [General Settings](https://dashboard.branch.io/settings) page.
The app's Branch link domain  will be listed at the very bottom of the [Link Settings](https://dashboard.branch.io/link)
page.

## Configuring a Windows app for deep linking

### Adding the Branch SDK to a Windows Solution

#### Conan Package Manager

The Branch C++ SDK has dependencies on [Poco](https://pocoproject.org/) for JSON and Network  libraries. Poco itself has
dependencies on OpenSSL and other packages.  Solving dependency issues is a thorny problem and to be able to produce an
SDK that works on any platform (including Windows, Mac, Linux) a package manager is recommended.

The C++ Branch SDK is fully compatible with [Conan](https://conan.io).  Conan is a C/C++ Package  manager for developers
and has integrations with Visual Studio to make it even easier to integrate quickly.

 1. Install Conan, if you haven't already.
 2. Install the [Conan Extension for Visual Studio](https://marketplace.visualstudio.com/items?itemName=conan-io.conan-vs-extension)

#### Adding the Branch SDK

* Once Conan is configured, it is just a matter of adding a `conanfile.txt` to your project.
An example would look like this:
```
[requires]
BranchIO/1.0.0@branch/stable
```

That's it!  The Conan dependency manager will solve all of the problems with getting Branch and the Branch dependency
chain.   Should Branch need to provide patches, it is just a simple step to update to (say) version 1.0.1.
___

### Integrating the Branch SDK

**I. Create project in visual studio**  

* Open Visual Studio and create a new Blank App (Windows Desktop Application) project

**II. Add the Branch SDK**

* Add a `conanfile.txt` to your project.  Use the example above to get started.

The Conan Extension will automatically pick up the dependency on Branch, and pull in the necessary
files needed.

**III. Create a Branch Instance**

Creating a Branch instance should be done as early as possible in the Application lifecycle.
For example, in `WinMain()`.

```C++
    #define BRANCH_KEY "key_live_xxx"

    BranchIO::Branch* _branchInstance;
    BranchIO::AppInfo _appInfo;

    void initializeBranch() {
        _appInfo
            .setAppVersion("1.0")
            .setCountryCode("US")
            .setDeveloperIdentity("My Company")
            .setEnvironment("FULL_APP")
            .setLanguage("en");

        _branchInstance = BranchIO::Branch::create(BRANCH_KEY, &_appInfo);
    }
```

**IV. Initialize a Branch Session**

A Branch Session is typically defined as starting when the user interface first appears, and  finishing when the last
user interface goes away.  Multiple views may be shown during a single session.   A session must be created
before doing any analytic events.

On Windows, this typically means creating a Branch Session in `WM_CREATE` and closing the session in `WM_DESTROY` in the
application `WndProc`.

```C++
LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    switch (message)
    {
    case WM_CREATE:
        // Start the Branch Session
        _branchInstance->openSession("", _myOpenCallback);
        break;

    case WM_DESTROY:
        // End the Branch Session
        _branchInstance->closeSession(_myCloseCallback);

        PostQuitMessage(0);
        break;

    default:
        return DefWindowProc(hWnd, message, wParam, lParam);
    }
    return 0;
}
```

Note that opening a session with Branch will notify of any pending deep links in the callback.

**V. Register app to handle deep links**  

To enable your Windows application for deep linking, please follow [Microsoft's documentation on enabling apps for websites using app URI handlers](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/web-to-app-linking).

**VI. Configuring**

* You may configure your app with these classes:
	* `BranchIO::AppInfo` - Application Information, such as version and language.
	* `BranchIO::AdvertiserInfo` - Advertiser Information, including the ability to disable tracking.

Example to disable ad tracking:

```C++
    _branchInstance->getAdvertiserInfo().disableTracking();
```

### Example code

To help you get started, there are a number of examples for learning how to effectively use the Branch C++ SDK

#### [Hello Branch](https://github.com/BranchMetrics/cpp-branch-deep-linking-attribution/blob/BranchSDK-Samples/console/hello/helloworld.cpp)
The most basic console application.  This sample creates a couple of attribution events and waits until they have all completed.

#### [Console Example](https://github.com/BranchMetrics/cpp-branch-deep-linking-attribution/blob/BranchSDK-Samples/console/example/example.cpp)
A more detailed console application.  This sample puts up a simple menu and allows the user to initialize sessions,
enable and disable tracking, and more.   This is most useful for examining how Branch works in an isolated environment
and provides an easy framework to extend to try out other requests that are pertinent to the problems that you are trying to solve.
Adding new menu items is trivial and is immensely useful for a debugging tool.

This example also has the ability to print out details of the environment that it is running on, such as the OS,
OS Version, SDK version, etc.

#### [Windows Color Picker](https://github.com/BranchMetrics/cpp-branch-deep-linking-attribution/blob/BranchSDK-Samples/Windows/ColorPicker/README.md)
Basic Windows Win32 application to demonstrate the Branch SDK in a Windows environment.  This sample will also
demonstrate basic deep linking functionality and creating links to share.

Creating Branch Links and sharing these will demonstrate how a Branch-enabled application can open a link and display
the correct values.


## Branch SDK Method Reference

### API Documentation
Branch auto-generates API documentation for every release. Please download the [Github Docs Zip file](https://github.com/BranchMetrics/cpp-branch-deep-linking-attribution/releases/tag/1.0.0) and run it locally.

### Create a Branch Instance

Creating a Branch Instance takes a Branch Key, and Application Information.
Initialize the AppInfo with settings specific to your app.

```C++
// Set AppInfo properties as necessary
BranchIO::AppInfo _appInfo;

_branchInstance = BranchIO::create("key_live_xxx", _appInfo);
```

### Create a Branch Session

Creating a Branch Session takes an optional link parameter and a callback.

```C++
BranchIO.openSession("", myCallback);
```

#### Persistent identities

Often, you might have the own user IDs, or want referral and event data to persist across  platforms or
uninstall/reinstall. It's helpful if you know the users access the service  from different devices. This where we
introduce the concept of an 'identity'.

To identify a user, just call:

```C++
BranchIO.setIdentity("user id", myCallback);
```

#### Logout

If you provide a logout function in the app, be sure to clear the user when the logout completes. This will ensure that
all the stored parameters get cleared and all events are properly attributed to the right identity.

```C++
BranchIO.logout(myCallback):
```

### Tracking User Actions and Events

Use BranchIO::BranchStandardEvent class to track special user actions or application specific events beyond app installs,
opens, and sharing. You can track events such as when a user adds an item to an on-line shopping cart, or searches for a
keyword etc. BranchEvent provides an interface to add content(s) in order to associate content(s) with events. You can
view analytics for the BranchEvents you fire on the Branch dashboard.  

*BranchIO::BranchStandardEvent* enumerate the most commonly tracked events and event parameters that can be used with
BranchEvent for the best results. You can always use custom event names and event parameters.

```C++
BranchStandardEvent ev = new BranchStandardEvent(BranchIO::StandardEvent::Type::ADD_TO_CART)
    .SetDescription("Test description")
    .SetTransactionID("123")
    .AddCustomDataProperty("TestProperty", "TestValue");
_branchInstance->sendEvent(ev, _myCallback);
```

### Generating Branch links

Branch links can be created in-app (as well as in many other ways).
When they are, and setIdentity has been called to associate a User ID with the current user session, Branch links will be associated with that User ID.

```C++
// you can access this data from any instance that installs or opens the app from this link

BranchIO::LinkInfo linkInfo;
linkInfo
    .addTag("Tag1")
    .setChannel("Sharing_Channel_name")
    .setFeature("my_feature_name")
    .addControlParameter("$android_deeplink_path", "custom/path/*")
    .addControlParameter("$ios_url", "http://example.com/ios")
    .setDuration(100);

// Add some additional control parameters
linkInfo
    .addControlParameter("$canonical_identifier", "item/12345")
    .addControlParameter("$canonical_url", https://branch.io/deepviews")
    .addControlParameter("$og_title", "My Content Title")
    .addControlParameter("$og_description", "my_product_description1")
    .addControlParameter("$og_image_url", "https://example.com/mycontent-12345.png");

// Generate a "long" branch link, synchronously.
string url = linkInfo.createLongUrl(_branchInstance);

// Create a "short" branch link, asynchronously (requires a server round trip).
// If successful, the callback will contain the long URL
linkInfo.createUrl(_branchInstance, _myCallback);
```

**Note**
You can customize the Facebook OG tags of each URL if you want to dynamically share content by using the following
_optional keys in the data dictionary_. Please use this
[Facebook tool](https://developers.facebook.com/tools/debug/og/object) to debug the OG tags!

| Key | Value
| --- | ---
| "$og_title" | The title you'd like to appear for the link in social media.
| "$og_description" | The description you'd like to appear for the link in social media.
| "$og_image_url" | The URL for the image you'd like to appear for the link in social media.
| "$og_video" | The URL for the video.
| "$og_url" | The URL you'd like to appear.
| "$og_app_id" | the OG app ID. Optional and rarely used.

Also, you do custom redirection by inserting the following _optional keys in the dictionary_:

| Key | Value
| --- | ---
| "$desktop_url" | Where to send the user on a desktop or laptop. By default it is the Branch-hosted text-me service.
| "$android_url" | The replacement URL for the Play Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_
| "$ios_url" | The replacement URL for the App Store to send the user if they don't have the app. _Only necessary if you want a mobile web splash_
| "$ipad_url" | Same as above but for iPad Store.
| "$fire_url" | Same as above but for Amazon Fire Store.
| "$blackberry_url" | Same as above but for Blackberry Store.
| "$windows_phone_url" | Same as above but for Windows Store.

You have the ability to control the direct deep linking of each link by inserting the following _optional keys in the dictionary_:

| Key | Value
| --- | ---
| "$deeplink_path" | The value of the deep link path that you'd like us to append to the URI. For example, you could specify "$deeplink_path": "radio/station/456" and we'll open the app with the URI "theapp://radio/station/456?link_click_id=branch-identifier". This is primarily for supporting legacy deep linking infrastructure.
| "$always_deeplink" | true or false. (default is not to deep link first) This key can be specified to have our linking service force try to open the app, even if we're not sure the user has the app installed. If the app is not installed, we fall back to the respective app store or $platform_url key. By default, we only open the app if we've seen a user initiate a session in the app from a Branch link (has been cookied and deep linked by Branch).
