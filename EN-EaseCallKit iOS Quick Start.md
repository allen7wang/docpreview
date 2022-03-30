# Get Started with iOS EaseCallKit

Built upon Agora RTC, `EaseCallKit` is an open-source audio/video UI library that uses Easemob IM as the signaling channel. It enables one-to-one voice calls, one-to-one video calls, and multi-party voice and video calls, allowing developers to easily implement general-purpose voice and video functions. Through signaling interaction confirmation among multiple devices, `EaseCallKit` ensures that: 
`1. When a call invitation is received, multiple devices ring at the same time during a multi-device login scenario.`
`2. When a call invitation is handled at one device, the handling result is synchronized to other devices immediately.`

`EaseCallKit` is saved on Github: https://github.com/easemob/easecallkitui-ios.

**When making a call using `EaseCallKit`, you need to join a channel with your Easemob IM username which will be shown on the voice and video view. If you directly invoke Agora APIs instead of using `EaseCallKit`, you can join the channel using a digital UID. **

**`Note: This UI library can be used only with the iOS/Android demo 3.8.0 or later. For `EaseCallKit` V3.8.1, you can join a channel using a digital UID, but use a string if `EaseCallKit` V3.8.0 is used. Users using the two versions of `EaseCallKit` cannot call each other. When using the demo, you need to apply for the token and UID used by `EaseCallKit` from Agora. Also, if you need to use Agora's voice and video services, apply for the token from Agora.`**


## Run the demo

`EaseCallKit` is integrated in Easemob's open-source IM demo. You can select iOS and download the Easemob IM SDK on the [Easemob IM demo and source code](https://www.easemob.com/download/im) page. Alternatively, you can download [Android IM source code](https://github.com/easemob/chat-android).

- Install the SDK and EaseCallKit

As the demo source code does not involve the SDK and `EaseCallKit`, you need to import them manually or using CocoaPods。

If CocoaPods is not available on your system, install it as indicated in [Getting Started with CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

To install the SDK and `EaseCallKit`, you need to access the directory where `podfile` is located and run the following command:

```
pod install
```

- Run the demo

After the SDK and `EaseCallKit` are installed, open the workspace `EaseIM.xcworkspace` in Xcode, connect to a mobile phone, and run the demo.

## Prerequisites

**In order to follow the procedure in this page, you must have:**

**[Easemob app](https://docs-im.easemob.com/im/ios/sdk/prepare#%E6%B3%A8%E5%86%8C%E5%B9%B6%E5%88%9B%E5%BB%BA%E5%BA%94%E7%94%A8) and [Agora app](https://docs.agora.io/en/Video/run_demo_video_call_ios?platform=iOS#1-%E5%88%9B%E5%BB%BA-agora-%E9%A1%B9%E7%9B%AE);**
**Easemob IM's basic functions, like login, contact, group, and conversation;**
**[App server](https://github.com/easemob/easemob-im-app-server/tree/master/agora-app-server) which is available before token authentication by Agora. For details on how to create the token service and use the app server to generate a token based on your Easemob IM username, see https://docs.agora.io/en/live-streaming/token_server.** 

## Integration Procedure

The procedure of using `EaseCallKit` to make a voice or video call is as follows:

1. The user calls the API to initialize `EaseCallKit`.
2. The caller invokes the API to initiate a call invitation. The call page appears. 
3. Upon reception of a call invitation, the callee chooses to answer or reject the call on the call page.
4. The caller or callee ends the call by clicking the **Hang Up** button on the call page.


### Import the EaseCallKit

`EaseCallKit` depends on `HyphenateChat`, `AgoraRtcEngine_iOS`, `Masonry`, and `SDWebImage`. Therefore, you need to import the dependency libraries using CocoaPods into your project during the import of `EaseCallKit`. 

**As `EaseCallKit` is a dynamic library, remember to add `use_frameworks!` to `podfile`.**

`EaseCallKit` can be imported manually or using CocoaPods. 

#### Import EaseCallKit using CocoaPods

- In Terminal, navigate to the root directory of the project and run the `pod init` command. Then the text file `Podfile` is generated in the project folder. 
- Open the `Podfile` file and modify it as follows. Remember to replace AppName with your app name.

```
use_frameworks!
target 'AppName' do
    pod 'HyphenateChat'
    pod 'Masonry'
    pod 'AgoraRtcEngine_iOS'
    pod 'SDWebImage'
    pod 'EaseCallKit', '~> version'
end
```

- In Terminal, run the command to update the version of your local library.
- Run the `pod install` command to install `EaseCallKit`. If this UI library is installed successfully, the message **Pod installation complete!** will be shown in Terminal. Also, the `xcworkspace` file is generated under the project folder.
- Open the `xcworkspace` file, connect to a mobile phone, and run the demo.
 
#### Manually import EaseCallKit

- Copy `EaseCallKit.framework` downloaded when you run through the demo to the root directory of your project.
- Choose **Project Settings > General** in Xcode, drag `EaseCallKit.framework` to the project and set `EaseCallKit.framework` to `Embed & Sign` in `Frameworks`, `libraries`, and `Embedded Content`.

### Add permissions

You app requires the permission to access voice and video devices and cameras. To add the permission, click the icon `+` to add the following information to the `info.plist` file.

| Key                                    | Type   | Value                                |
| :------------------------------------- | :----- | :----------------------------------- |
| Privacy - Microphone Usage Description | String | Specific microphone use description, for example, "Easemob IM needs to use your microphone."  |
| Privacy - Camera Usage Description     | String | Specific camera use description, for example, "Easemob IM needs to use your camera." |

If you want to run `EaseCallKit` in the background, you need to add the permission to play voices and videos in the background. In the `info.plist` file, click the icon `+` and select `Required background modes`, with `Type` set as `Array`, and add the `App plays audio or streams audio/video using AirPlay` element.

###  Initialize EaseCallKit

Upon the initialization of Easemob IM SDK, you can initialize the `EaseCallKit`, add callback methods within `EaseCallDelegate`, and set common configuration options. Following is sample code:

```
EaseCallConfig* config = [[EaseCallConfig alloc] init];
EaseCallUser* usr = [[EaseCallUser alloc] init];
usr.nickName = @"custom nickname";
usr.headImage = [NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"headImage" ofType:@"png"]];
config.users = @{@"Easemob IM ID":usr};
config.agoraAppId=@"Agora appID";
[[EaseCallManager sharedManager] initWithConfig:config delegate:self];
```

Configuration options are as follows:

```
@interface EaseCallConfig : NSObject
// Sets the default avatar.
@property (nonatomic)  NSURL*  defaultHeadImage;
// Sets the call timeout in seconds.
@property (nonatomic) UInt32 callTimeOut;
// Sets the user information dictionary. Within the dictionary, the data is expressed as a key-value pair, where the key is the user's Easemob IM username and the value is `EaseCallUser`.
@property (nonatomic) NSMutableDictionary* users;
// Sets the ringtone file.
@property (nonatomic) NSURL* ringFileUrl;
// Sets Agora appId.
@property (nonatomic) NSString* agoraAppId;
// Whether to enable authentication based on an Agora token. By default, this function is disabled.
@property (nonatomic) BOOL enableRTCTokenValidate
@end
```

### Initiate a call invitation
 
After the `EaseCallKit` initiation is completed, you can make a voice or video call.

#### One-to-one voice or video call

The one-to-one call can be a voice call or video call. You can make a one-to-one call using the `startSingleCallWithUId` API:

```
//Start a one-to-one call.
// remoteUser  The Easemob IM username of the user that is invited to join the call.
// type  The call type. The value `EaseCallType1v1Audio` indicates a voice call, while `EaseCallType1v1Video` indicates a video call.
// ext The call extension information. Actually, it is the user information dictionary.
[[EaseCallManager sharedManager] startSingleCallWithUId:remoteUser type:aType ext:nil completion:^(NSString * callId, EaseCallError * aError) {
    
}];
```

#### Multi-party voice or video call

You can initiate a voice or video call invitation to multiple users on the group member list or contact list by invoking the `startInviteUsers` method. For details, see `ConferenceInviteActivity` in the demo. 

```
// Invites users to join a multi-party call.
// aInviteUsers  The array of Easemob IM usernames of users that are invited to join the multi-party call.   
// ext  The call extension information. If a call is started within a group, you need to set the group ID 
[[EaseCallManager sharedManager] startInviteUsers:aInviteUsers ext:@{@"groupId":aConversationId} completion:^(NSString * callId, EaseCallError * aError) {
    
}];
```

The call page appears after a call is started:

[![img](https://docs-im.easemob.com/_media/im/ios/other/sendcall.png?w=200&tok=965af3)](https://docs-im.easemob.com/_detail/im/ios/other/sendcall.png?id=im%3Aios%3Aother%3Aeasecallkit)

### Receive a call invitation

After the caller initiates a call invitation, if the callee is online but not in the call, a call page appears as follows for the callee to choose to accept or reject the call.

[![img](https://docs-im.easemob.com/_media/im/ios/other/recvcall.png?w=200&tok=135b0b)](https://docs-im.easemob.com/_detail/im/ios/other/recvcall.png?id=im%3Aios%3Aother%3Aeasecallkit)

When the callee's mobile phone rings, the `callDidReceive` method in `EaseCallKitListener` will be triggered.

```
- (void)callDidReceive:(EaseCallType)aType inviter:(NSString*_Nonnull)user ext:(NSDictionary*)aExt
{
    
}
```

### Send a call invitation to users during a multi-party call

During an ongoing multi-party call, a participate can still invite other users to join the call by clicking the **Invite** button in the upper-right corner of the multi-party call page. In this case, the `multiCallDidInvitingWithCurVC` method in `EaseCallKitListener` will be triggered.

```
//Occurs when a user is invited to join a multi-party call. vc is the current view controller; users indicate exiting users in the call; aExt indicates call extension information.
- (void)multiCallDidInvitingWithCurVC:(UIViewController*_Nonnull)vc excludeUsers:(NSArray<NSString*> *_Nullable)users ext:(NSDictionary *)aExt
{
    //If you want to invite users only from a certain group to join a call, add `groupId` as the extension field. 
    NSString* groupId = nil;
    if(aExt) {
        groupId = [aExt objectForKey:@"groupId"];
    }
    
    ConfInviteUsersViewController * confVC = nil;
    if([groupId length] == 0) {
        confVC = [[ConfInviteUsersViewController alloc] initWithType:ConfInviteTypeUser isCreate:NO excludeUsers:users groupOrChatroomId:nil];
    }else{
        confVC = [[ConfInviteUsersViewController alloc] initWithType:ConfInviteTypeGroup isCreate:NO excludeUsers:users groupOrChatroomId:groupId];
    }
    [confVC setDoneCompletion:^(NSArray *aInviteUsers) {
        [[EaseCallManager sharedManager] startInviteUsers:aInviteUsers ext:aExt completion:nil];
    }];
    confVC.modalPresentationStyle = UIModalPresentationPopover;
    [vc presentViewController:confVC animated:NO completion:nil];
}
```

For details on how to implement the call invitation page, see the `ConfInviteUsersViewController` method in the demo.

### Callback triggered when the current user joins a channel

Since `EaseCallKit` 3.8.1, the `callDidJoinChannel` method has been added to allow a user to receive a callback after joining a call.

```
- (void)callDidJoinChannel:(NSString*_Nonnull)aChannelName uid:(NSUInteger)aUid
{
    //At this time, you can obtain the mappings of Agora usernames and Easemob IM usernames of the existing users on the channel, add the mappings to `EaseCallKit`, as well as update their avatar and nickname. 
    //[self _fetchUserMapsFromServer:aChannelName];
    [[EaseCallManager sharedManager] setUsers:users channelName:channelName];
}
```

### Callback triggered when the other party joins a channel

Since `EaseCallKit` 3.8.1, the `remoteUserDidJoinChannel` method has been added to allow the current user to receive a callback when the other party joins the call.

```
-(void)remoteUserDidJoinChannel:( NSString*_Nonnull)aChannelName uid:(NSInteger)aUid username:(NSString*_Nullable)aUserName
{
    //At this time, the other party of the call can obtain the mappings of Agora RTC UIDs and Easemob IM usernames of the existing users on the channel, add the mappings to `EaseCallKit`, as well as update their avatars and nicknames. 
    //[self _fetchUserMapsFromServer:aChannelName];
    [[EaseCallManager sharedManager] setUsers:users channelName:channelName];
}
```

### End a call

For a one-to-one voice or video call, if one party hangs up, the call will be ended for both parties; for a multi-party call, if a participant hangs up, the call is ended only for this participant, but still ongoing for other participants.

When a call ends, the `callDidEnd` method in `EaseCallKitListener` will be triggered. 

```
// Occurs when a call is ended.
// aChannelName   The name of Agora's channel used during the call. According to the channel name, you can query the call quality on Agora Analytics on Agora Console.
// aReason   The reason why the call is ended.
// aTm   The call duration in seconds.
// aCallType   The call type.
- (void)callDidEnd:(NSString*)aChannelName reason:(EaseCallEndReason)aReason time:(int)aTm type:(EaseCallType)aCallType
{
    NSString* msg = @"";
    switch (aReason) {
        case EaseCallEndReasonHandleOnOtherDevice:
            msg = @"The call is handled on another device.";
            break;
        case EaseCallEndReasonBusy:
            msg = @"The other party is busy.";
            break;
        case EaseCallEndReasonRefuse:
            msg = @"The other party refuses to answer the call.";
            break;
        case EaseCallEndReasonCancel:
            msg = @"You have cancelled the call. ";
            break;
        case EaseCallEndReasonRemoteCancel:
            msg = @"The other party cancelled the call. ";
            break;
        case EaseCallEndReasonRemoteNoResponse:
            msg = @"There is no answer from the other party.";
            break;
        case EaseCallEndReasonNoResponse:
            msg = @"The call times out.";
            break;
        case EaseCallEndReasonHangup:
            msg = [NSString stringWithFormat:@"The call is ended, with a duration of %d seconds, aTm];
            break;
        default:
            break;
    }
    if([msg length] > 0)
       [self showHint:msg];
}
```

## Advanced Features

### Call exception callback

If an exception or error occurs during a call, the `callDidOccurError` method will be triggered.

Call errors are divided into business errors, voice or video errors, and Easemob IM errors.

```
// Occurs when an exception or error occurs during a call. 
// aError   An error that can be an Easemob IM error, RTC error, or business error.
- (void)callDidOccurError:(EaseCallError *)aError
{
    
}
```

`EaseCallError` involves business errors, voice or video errors, and Easemob IM errors.

```
@interface EaseCallError : NSObject
//The error type, which can be an Easemob IM error, RTC error, and business logic error.
@property (nonatomic) EaseCallErrorType aErrorType;
//Error code.
@property (nonatomic) NSInteger errCode;
//The error description.
@property (nonatomic) NSString* errDescription;
```

### Modify EaseCallKit configurations

After `EaseCallKit` is initialized, you can modify its configurations by invoking the following method: 

```
//Change the ringtone.
EaseCallConfig* config = [[EaseCallManager sharedManager] getEaseCallConfig];
NSString* path = [[NSBundle mainBundle] pathForResource:@"huahai128" ofType:@"mp3"];
config.ringFileUrl = [NSURL fileURLWithPath:path];
```

### Change the avatar and nickname 

Since `EaseCallKit` 3.8.1, an API has been added to allow you to change your avatar and nickname. Therefore, after joining a channel, you can change your own avatar and nickname and those of others in the call.

```
EaseCallUser* user = [EaseCallUser userWithNickName:info.nickName image:[NSURL URLWithString:info.avatarUrl]];
[[[EaseCallManager sharedManager] getEaseCallConfig] setUser:username info:user];
```
## Reference

### Obtain an Agora token

Before joining a voice or video call, you need to obtain an Agora token for authentication. First, enable token authentication as follows:

```
EaseCallUser* callUser = [[EaseCallUser alloc] init];
config.enableRTCTokenValidate = YES;//Whether to enable token-based RTC authentication. By default, this function is disabled.
[[EaseCallManager sharedManager] initWithConfig:config delegate:self];
```

After token-based RTC authentication is enabled, the `callDidRequestRTCTokenForAppId` method within `EaseCallDelegate` is triggered and you invoke the `setRTCToken:channelName:` method to pass the token to `EaseCallKit` during a call. Then a token is obtained from your app server.
For how to implement the app server, see [Authenticate Your Users with Tokens](https://docs.agora.io/en/live-streaming/token_server)

```
- (void)callDidRequestRTCTokenForAppId:(NSString * _Nonnull)aAppId
                           channelName:(NSString * _Nonnull)aChannelName
                               account:(NSString * _Nonnull)aUserAccount
{
  [[EaseCallManager sharedManager] setRTCToken:@"Your RTC Token" channelName:aChannelName];
}

Since EaseCallKit 3.8.1, the `uid` parameter has been added to the `callDidRequestRTCTokenForAppId` method and you can use a digital UID to join Agora's channel. 
- (void)callDidRequestRTCTokenForAppId:(NSString *)aAppId channelName:(NSString *)aChannelName account:(NSString *)aUserAccount uid:(NSInteger)aAgoraUid
{
    [[EaseCallManager sharedManager] setRTCToken:@"Your RTC Token" channelName:aChannelName uid:You Agora uid];
}
```

### Offline Push

You can enable offline push to ensure that call requests can be received when your app runs in the background or gets offline. For details on offline push, see https://docs-im.easemob.com/im/other/integrationcases/iosimnotifi.

For how to enable offline push, see [Enable iOS Push](https://docs-im.easemob.com/ccim/ios/push). 

After offline push is enabled, when a call request is received, a notification message will appear on the notification page of your mobile phone. Clicking this message, you can wake up the app and open the ringtone page.

## API List

`EaseCallKitUI` consists of the management module `EaseCallManager` and callback module `EaseCallDelegate`.

`EaseCallManager` provides the following methods:

|  Method                                                  | Description                     |
| :------------------------------------------------------ | :------------------------------- |
| initWithConfig:delegate                    | Initializes `EaseCallKit`.              |
| startSingleCallWithUId:type:ext:completion | Starts a one-to-one call.       |
| startInviteUsers:type:ext:completion:      | Invites users to join a multi-party call.      |
| getEaseCallConfig                          | Gets configurations of `EaseCallKit`.         |
| setRTCToken:channelName:                   | Sets an Agora token. This method has been added since `EaseCallKit` 3.8.1.                         |
| setRTCToken:channelName:uid:               | Sets an Agora token. This method has been added since `EaseCallKit` 3.8.1.                  |
| setUsers:channelName:                      | Set mappings between Easemob IM usernames and Agora UIDs. This method has been added since `EaseCallKit` 3.8.1. |

`EaseCallDelegate` provides the following methods:

| Method                                                  |  Description                      |
| :------------------------------------------------------ | :------------------------------- |
| callDidEnd:reason:time:type:                            | Occurs when the call is ended.      |
| multiCallDidInvitingWithCurVC:excludeUsers:ext:         | Occurs when a participant in a multi-party call invites others to join the call by clicking the `Invite` button on the call page.    |
| callDidReceive:inviter:ext:                             | Occurs when the phone rings.               |
| callDidRequestRTCTokenForAppId:channelName:account:     | Occurs when an Agora token is obtained. This method has been added since `EaseCallKit` 3.8.1.         |
| callDidRequestRTCTokenForAppId:channelName:account:uid: | Occurs when an Agora token is obtained. This method has been added since `EaseCallKit` 3.8.1.    |
| callDidOccurError:                                      | Occurs when a call error occurs.                 |
| remoteUserDidJoinChannel:uid:                           | Occurs when the other party joins a channel. This method has been added since `EaseCallKit` 3.8.1.   |
| callDidJoinChannel:uid:                                 | Occurs when the current user joins a channel. This method has been added since `EaseCallKit` 3.8.1.  |