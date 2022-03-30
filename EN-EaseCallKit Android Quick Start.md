# Get Started with Android EaseCallKit

`EaseCallKit` is an open-source audio/video UI library built upon Easemob IM and Agora RTC. It enables one-to-one voice calls, one-to-one video calls, and multi-party voice and video calls, allowing developers to easily implement general-purpose voice and video functions.

**When making a call using `EaseCallKit`, you need to join a channel with your Easemob IM username which will be shown on the voice and video view. If you directly invoke Agora APIs instead of using `EaseCallKit`, you can join the channel using a digital UID.**

**`Note: This UI library can be used only with the iOS/Android demo 3.8.0 or later. For `EaseCallKit` V3.8.1, you can join a channel using a digital UID, but use a string if `EaseCallKit` V3.8.0 is used. Users using the two versions of `EaseCallKit` cannot call each other. When using the demo, you need to apply for the token and UID used by `EaseCallKit` from Agora. Also, if you need to use Agora's voice and video services, apply for the token from Agora.`**

## Run the demo

EaseCallKit is integrated in Easemob's open-source IM demo. You can select Android and download the Easemob IM SDK on the [Easemob demo and source code](https://www.easemob.com/download/im) page. Alternatively, you can download [Android IM source code](https://github.com/easemob/chat-android).

Before running the demo, make sure that you have the following:

- Android Studio 3.5 and later
- Gradle 4.6 and later
- targetSdkVersion 29
- minSdkVersion 19
- Java JDK 1.8 and later

After the source code is downloaded, you can open the project in Android Studio, connect to a mobile phone, and run the demo. 

## Prerequisites 

In order to follow the procedure in this page, you must have:
- [Easemob app](https://docs-im.easemob.com/im/ios/sdk/prepare#%E6%B3%A8%E5%86%8C%E5%B9%B6%E5%88%9B%E5%BB%BA%E5%BA%94%E7%94%A8) and [Agora app](https://docs.agora.io/en/Video/run_demo_video_call_ios?platform=iOS#1-%E5%88%9B%E5%BB%BA-agora-%E9%A1%B9%E7%9B%AE);
- Easemob IM's basic functions, like login, contact, group, and conversation;
- [App server](https://github.com/easemob/easemob-im-app-server/tree/master/agora-app-server) deployed to generate a token before the token authentication by Agora. For details on how to use the app server to generate a token, see https://docs.agora.io/cn/live-streaming/token_server.

## Integration Procedure

The procedure of using `EaseCallKit` to make a voice or video call is as follows:

1. The user calls the API to initialize `EaseCallKit`.
2. The caller invokes the API to initiate a call invitation. The call page appears. 
3. Upon reception of a call invitation, the callee chooses to answer or reject the call on the call page.
4. The caller or callee hangs up on the call page.

### Import the EaseCallKit

`EaseCallKit` mainly depends on `com.hyphenate:hyphenate-chat:xxx` and `io.agora.rtc:full-sdk:xxx`, where `xxx` indicates a version. 

`EaseCallKit` can be integrated by using Gradle or source code.

 
#### Integrate EaseCallKit using Gradle

- Add the following code to `build.gradle` and rebuild your project.

```
implementation 'io.hyphenate:ease-call-kit:3.8.9'
```

**`EaseCallKit` must depend on Easemob IM SDK (hyphenate-chat). Therefore, you must add Easemob IM SDK when using `EaseCallKit`.**

####  Integrate EaseCallKit using source code

- Download [EaseCallKit source code](https://github.com/easemob/easecallkitui-android).
- Add the following code to `build.gradle` and rebuild your project.

```
implementation project(':ease-call-kit')
```

To change the version number in `hyphenate-chat` and `agora.rtc` in `EaseCallKit`, you need to modify the following dependency:

```
//Easemob SDK
implementation 'io.hyphenate:hyphenate-chat:3.8.0' (`hyphenate-chat` only supports Easemob SDK 3.8.0 or later)
//Agora SDK
implementation 'io.agora.rtc:full-sdk:3.8.0'
```

### Add permissions

Add the microphone permission, camera permission, and floating window permission for the `EaseCallKit` as required. 

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.hyphenate.easeim">
 
    <!-- Adds the floating window permission.-->
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    <!-- Adds the network access permission.-->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- Adds the microphone permission.-->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <!-- Adds the camera permission.-->
    <uses-permission android:name="android.permission.CAMERA" />
    ...
</manifest>
```

###  Add EaseCallKit activities

Add `EaseVideoCallActivity` and `EaseMultipleVideoActivity` in the `EaseCallKit` to the activity list:

```
//Adds activities.
<activity
    android:name="com.hyphenate.easecallkit.ui.EaseVideoCallActivity"
    android:configChanges="orientation|keyboardHidden|screenSize"
    android:excludeFromRecents="true"
    android:launchMode="singleInstance"
    android:screenOrientation="portrait"/>
<activity
    android:name="com.hyphenate.easecallkit.ui.EaseMultipleVideoActivity"
    android:configChanges="orientation|keyboardHidden|screenSize"
    android:excludeFromRecents="true"
    android:launchMode="singleInstance"
    android:screenOrientation="portrait"/>
```

### Initialize the EaseCallKit

Upon the initialization of Easemob IM SDK, you can initialize the `EaseCallKit`, add callback methods within `EaseCallKitListener`, and set common configuration options. Following is sample code:

```
//Initializes the EaseCallUIKit.
EaseCallKitConfig callKitConfig = new EaseCallKitConfig();
//Sets the default avatar. 
String headImage = EaseFileUtils.getModelFilePath(context,"watermark.png");
callKitConfig.setDefaultHeadImage(headImage);
//Sets the ringtone file.
String ringFile = EaseFileUtils.getModelFilePath(context,"huahai.mp3");
callKitConfig.setRingFile(ringFile);
//Sets the call timeout in seconds.
callKitConfig.setCallTimeOut(30 * 1000);
//Sets Agora appId.
callKitConfig.setAgoraAppId("*****");
Map<String, EaseCallUserInfo> userInfoMap = new HashMap<>();
userInfoMap.put("***",new EaseCallUserInfo("****",null));
userInfoMap.put("***",new EaseCallUserInfo("****",null));
callKitConfig.setUserInfoMap(userInfoMap);
EaseCallKit.getInstance().init(context,callKitConfig);
addCallkitListener();
```

Configuration options are as follows:

```
/**
 * User configuration options of `EaseCallKit`.
 * defaultHeadImage   The user's default avatar. The value is a local absolute file path or a URL.
 * userInfoMap      The user information. The user information is represented as a key-value pair, where the key is the user's Easemob IM username and the value is `EaseCallUserInfo`.
 * callTimeOut      The call timeout in milliseconds. The default value is 30 seconds.
 * audioFile       The ringtone file. The value is a local absolute file path.
* enableRTCToken   Whether to enable RTC authentication. This function is controlled on the Agora console. By default, RTC authentication is disabled.
 */
public class EaseCallKitConfig {
    private String defaultHeadImage;
    private Map<String,EaseCallUserInfo> userInfoMap = new HashMap<>();
    private String RingFile;
    private String agoraAppId = "****";
    private long callTimeOut = 30 * 1000;
    public EaseCallKitConfig(){
  ...
}
```

### Initiate a call invitation

After the `EaseCallKit` initiation is completed, you can make a voice or video call.

#### One-to-one voice or video call

The one-to-one call can be a voice call or video call. 
You can make a one-to-one call by using the `startSingleCall` API:
```
/**
* Starts a one-to-one call.
* @param type The call type, which can only be `SIGNAL_VOICE_CALL` or `SIGNAL_VIDEO_CALL`.
* @param user The callee ID, which is the Easemob IM username.
* @param ext  The custom extension field that describes call extension information.
*/
public void startSingleCall(final EaseCallType type, final String user,final  String ext){}
```

#### Multi-party voice or video call

You can initiate a voice or video call invitation to multiple users on the group member list or contact list. For details, see `ConferenceInviteActivity` in the demo. 

```
/**
 * Invites users to join a multi-party call.
 * @param users The list of callee IDs which are usernames of the Easemob IM.
 * @param ext   The custom extension field that describes call extension information.
 */
public void startInviteMultipleCall(final String[] users,final String ext){}
```

The call page appears after a call is started:

[![img](https://docs-im.easemob.com/_media/im/ios/other/sendcall.png?w=200&tok=965af3)](https://docs-im.easemob.com/_detail/im/ios/other/sendcall.png?id=im%3Aandroid%3Aother%3Aeasecallkit)

### Receive a call invitation

After the caller initiates a call invitation, if the callee is online but not in the call, a call invitation page appears for the callee to choose to accept or reject the call. The call page is as follows.

When the callee receives a call invitation, the `onRevivedCall` method in `EaseCallKitListener` will be triggered.

```
/**
 * Occurs when a call invitation is received.
 * @param callType  The call type.
 * @param userId    The ID of the user that sends the call invitation.
 * @param ext    The custom extension field that describes call extension information.
 */
void onRevivedCall(EaseCallType callType, String userId,String ext){}
```

The call invitation page is as follows:

[![img](https://docs-im.easemob.com/_media/im/android/other/called.jpg?w=200&tok=c5f26f)](https://docs-im.easemob.com/_detail/im/android/other/called.jpg?id=im%3Aandroid%3Aother%3Aeasecallkit)

### Send a call invitation to users during a muti-party call

During an ongoing multi-party call, a participate can still add other users to the call by clicking the **Invite** button in the upper-right corner of the multi-party call page. In this case, the `onInviteUsers` callback in `EaseCallKitListener` will be triggered.

```
/**
 * Invites contacts to join an ongoing multi-party call.
 * @param context  The context.
 * @param users   The existing participants in the call.
 * @param ext     The custom extension field that describes call extension information.
 */
public void onInviteUsers(Context context,String userId[],String ext) {
}
```

### Callback for successfully joining a channel

If a user joins a voice or video call, all participants in the call, including the new user, will receive the `onRemoteUserJoinChannel` callback of `EaseCallKitListener`. This callback API has been added since SDK 3.8.1.

```
@Override
public void onRemoteUserJoinChannel(String channelName, String userName, int uid, EaseGetUserAccountCallback callback){
    //At this time, you can obtain the mappings of Agora usernames and Easemob IM usernames of the existing users on the channel, add the mappings to `EaseCallKit`, as well as update their avatars and nicknames. 
    // callback.onUserAccount(accounts);
}
```

### End a call

For a one-to-one voice or video call, if one party hangs up, the call will be ended for both parties; for a multi-party call, if a participant hangs up, the call is ended only for this participant, but still ongoing for other participants.

When a call ends, the `onEndCallWithReason` method in `EaseCallKitListener` will be triggered. 

```
//**
 * Occurs when a call is ended.
 * @param callType   The call type.
 * @param reason    The reason why the call is ended.
 * @param callTime  The call duration.
*/
void onEndCallWithReason(EaseCallType callType, String channelName, EaseCallEndReason reason, long callTime){}
 
The reason why a call is ended can be as follows:  
public enum EaseCallEndReason {
    EaseCallEndReasonHangup(0), //The call is ended normally.
    EaseCallEndReasonCancel(1), //You have cancelled the call.
    EaseCallEndReasonRemoteCancel(2), //The other party cancelled the call. 
    EaseCallEndReasonRefuse(3),//The callee refuses to answer the call.
    EaseCallEndReasonBusy(4), //The line is busy.
    EaseCallEndReasonNoResponse(5), //The call times out.
    EaseCallEndReasonRemoteNoResponse(6), //There is no answer from the other party.
    EaseCallEndReasonHandleOnOtherDevice(7); //The call is handled on another device.
   ....
}
```

## Advanced Features

### Call exception callback

If an exception or error occurs during a call, the `onCallError` callback in `EaseCallKitListener` will be triggered.

```
/**
  * Occurs when an exception or error occurs during a call. 
  * @param type           The error type. 
  * @param errorCode      The error code.
  * @param description   The error description
*/
void onCallError(EaseCallKit.EaseCallError type, int errorCode, String description){}
```

EaseCallError involves business logic errors, voice or video errors, and Easemob IM errors.

```
/**
 * The call error type.
 *
*/
public enum EaseCallError{
   PROCESS_ERROR, //The business logic error
   RTC_ERROR, //The voice or video error.
   IM_ERROR  //The Easemob IM error.
}
```

### Modify configurations

After the `EaseCallKit` initialization is complete, you can modify related configurations:

```
/**
* Gets configurations of `EaseCallKit`.
*
*/
public EaseCallKitConfig getCallKitConfig(){}
 
//Changes the default avatar.
EaseCallKitConfig config = EaseCallKit.getInstance().getCallKitConfig();
if(config != null){
     String Image = EaseFileUtils.getModelFilePath(context,"bryant.png");
     callKitConfig.setDefaultHeadImage(Image);
}
```

### Change the avatar and nickname

Since `EaseCallKit` 3.8.1, an API has been added to allow you to change your avatar and nickname. Therefore, after joining a channel, you can change your own avatar and nickname and those of others in the call.

```
@Override
public void onRemoteUserJoinChannel(String channelName, String userName, int uid, EaseGetUserAccountCallback callback){
    if(userName == null || userName == ""){
        //Gets the user's Easemob IM username according to the Agora UID. `url` indicates the URL to request user information. For details, see the [Easemob IM demo](https://www.easemob.com/download/demo).  
        getUserIdAgoraUid(uid, url, callback);
        //Passes the obtained user information to the `onUserAccount` method. For details, see [Easemob IM demo](https://www.easemob.com/download/demo).
        //callback.onUserAccount(userAccounts);
    }else{
        //Sets the user's nickname and avatar. 
        setEaseCallKitUserInfo(userName);
        EaseUserAccount account = new EaseUserAccount(uid,userName);
        List<EaseUserAccount> accounts = new ArrayList<>();
        accounts.add(account);
        callback.onUserAccount(accounts);
    }
}
```

## Reference

### Obtain an Agora token

Before joining a voice or video call, you need to obtain an Agora token for authentication and invoke the `onSetToken` method to pass the token to `EaseCallKit`.

If authentication is not required, you will invoke the `onSetToken` method to pass **null** as the token to `EaseCallKit`. Alternatively, it is unnecessary to invoke this method in this case.

```
/**
 * Occurs when a token is generated for a user. 
 * @param userId       The user's Easemob IM username .
 * @param channelName  The channel name.
 * @param agoraAppId   Agora's app ID.
 * @param callback     Callback for token generation. If a token is generated, the token is returned; if a token fails to be generated, the related error code and error message will be returned.
 */
 default void onGenerateToken(String userId,String channelName,String agoraAppId,EaseCallKitTokenCallback callback){};
 
 @Override
 public void onGenerateToken(String userId, String channelName, String agoraAppId, EaseCallKitTokenCallback )callback{
         if(callback != null){
               // If token authentication is not required, pass `null` to the `token` parameter and `0` to `uId`.            
               //callback.onSetToken(null, 0); 
               
               // If token authentication is required, use your app server to generate a token and invoke the `onSetToken` method to pass the token to `EaseCallKit`.
               // url indicates the URL into which parameters are spliced to request a token. For details, see the [Easemob IM demo on Easemob's official website](https://www.easemob.com/download/demo)
               getRtcToken(url, callback);
               //Invoke the `onSetToken` method to return the obtained token and uid to `EaseCallKit`.
               //callback.onSetToken(token, uid);
         }
}

Since EaseCallKit 3.8.1, the `uId` parameter has been added to the `onSetToken` method in `EaseCallKitTokenCallback` and you can use a digital UID to join Agora's channel. 

void onSetToken(String token, int uId);
```

### Offline Push

You can enable offline push to ensure that call requests can be received when your app runs in the background or gets offline. For details on offline push, see https://docs-im.easemob.com/im/other/integrationcases/appimnotifi.

For how to enable offline push, see [Enable Android Push](https://docs-im.easemob.com/ccim/android/push). 

After offline push is enabled, when a call request is received, a notification message will appear on the notification page of your mobile phone. Clicking this message, you can wake up the app and open the ringtone page.


## API List

`EaseCallKit` provides the following methods:

| Method                 | Description                                                      |
| :---------------------- | :----------------------------------------------------------- |
| init                    | Initializes `EaseCallKit`.   |
| setCallKitListener      | Sets `EaseCallKitListener`.     |
| startSingleCall         | Starts a one-to-one call. |
| startInviteMultipleCall | Invites users to join a multi-party call. |
| getCallKitConfig        | Gets configurations of `EaseCallKit`.  |

`EaseCallKitListener` provides the following methods:

|  Event                 | Description                                                      |
| :---------------------- | :----------------------------------------------------------- |
| onEndCallWithReason     | Occurs when the call is ended.                                         |
| onInviteUsers           | Occurs when a participant in a multi-party call invites others to join the call by clicking the `Invite` button on the call page.                             |
| onReceivedCall          | Occurs when the phone rings.                                             |
| onGenerateToken         | Occurs when an Agora token is obtained.  You can invoke this method to pass the token to `EaseCallKit`.|
| onCallError             | Occurs when a call error occurs.                                         |
| onInViteCallMessageSent | Occurs when a call invitation message is sent.                    |
| onRemoteUserJoinChannel | Occurs when a user joins a channel. This method has been added since EaseCallKit 3.8.1.          |

`EaseGetUserAccountCallback` provides the following methods:

| Event               | Description                               |
| :-------------------- | :------------------------------------- |
| onUserAccount         | Occurs when the mappings between Easemob usernames and Agora UIDs are passed. This API has been added since `EaseCallKit` 3.8.1.  |
| onSetUserAccountError | Occurs when user information fails to be obtained. This method has been added since `EaseCallKit` 3.8.1.           |