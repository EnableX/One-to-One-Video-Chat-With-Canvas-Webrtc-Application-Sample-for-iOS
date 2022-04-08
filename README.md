# 1-to-1 RTC: A Sample iOS App with EnableX iOS Toolkit

The sample iOS App demonstrates the use of [EnableX platform Server APIs](https://www.enablex.io/cpaas/video-api) and iOS Toolkit to build 1-to-1 RTC (Real Time Communication) Application.  It allows developers to ramp up on app development by hosting on their own devices. 

This App creates a virtual Room on the fly  hosted on the Enablex platform using REST calls and uses the Room credentials (i.e. Room Id) to connect to the virtual Room as a Moderator or Participant using a mobile client.  The same Room credentials can be shared with others to join the same virtual Room to carry out a RTC (Real Time Communication) session. 

> EnableX Developer Center: https://developer.enablex.io/


## 1. How to get started

### 1.1 Pre-Requisites

#### 1.1.1 App Id and App Key 

* Register with EnableX [https://portal.enablex.io/cpaas/trial-sign-up/] 
* Login to the EnableX Portal
* Create your Application Key
* Get your App ID and App Key delivered to your Email


#### 1.1.2 Sample iOS Client 

* Clone or download this Repository [https://github.com/EnableX/One-to-One-Video-Chat-With-Canvas-Webrtc-Application-Sample-for-iOS.git] 


#### 1.1.3 Test Application Server

You need to setup an Application Server to provision Web Service API for your iOS Application to communicate enabling Video Session. 

To help you to try our iOS Application quickly, without having to setup Applciation Server, this Application is shipped pre-configured to work in a "try" mode with EnableX hosted Application Server i.e. https://demo.enablex.io. 

Our Application Server restricts a single Session Duation to 10 minutes, and allows 1 moderator and note more than 1 Participant in a Session.

Once you tried EnableX iOS Sample Application, you may need to setup your own  Application Server and verify your Application to work with your Application Server.  More on this, read Point 2 later in the Document.

#### 1.1.4 Configure iOS Client 

* Open the App
* Go to VCXConstant.swift and change the following:
``` 
 /* To try the App with Enablex Hosted Service you need to set the kTry = true
 When you setup your own Application Service, set kTry = false */
 let kTry = true

 /* Your Web Service Host URL. Keet the defined host when kTry = true */
 let kBasedURL = "https://demo.enablex.io/"
     
 /* Your Application Credential required to try with EnableX Hosted Service
 When you setup your own Application Service, remove these */
 let kAppId    = ""
 let kAppkey   = ""
 ```
 
 Note: The distributable comes with demo username and password for the Service. 

### 1.2 Test

#### 1.2.1 Open the App

* Open the App in your Device. You get a form to enter Credentials i.e. Name & Room Id.
* You need to create a Room by clicking the "Create Room" button.
* Once the Room Id is created, you can use it and share with others to connect to the Virtual Room to carry out a RTC Session either as a Moderator or a Participant (Choose applicable Role in the Form).

Note: Only one user with Moderator Role allowed to connect to a Virtual Room while trying with EnableX Hosted Service. Your Own Application Server may allow upto 5 Moderators.
 
Note:- If you used any emulator/simulator your local stream will not create. It will create only on real device.

## 2 Setup Your Own Application Server

You may need to setup your own Application Server after you tried the Sample Application with EnableX hosted Server. We have differnt variant of Appliciation Server Sample Code, pick one in your preferred language and follow instructions given in respective README.md file.

*NodeJS: [https://github.com/EnableX/Video-Conferencing-Open-Source-Web-Application-Sample.git]
*PHP: [https://github.com/EnableX/Group-Video-Call-Conferencing-Sample-Application-in-PHP]

Note the following:

* You need to use App ID and App Key to run this Service.
* Your iOS Client End Point needs to connect to this Service to create Virtual Room and Create Token to join session.
* Application Server is created using EnableX Server API, a Rest API Service helps in provisioning, session access and pos-session reporting.  

To know more about Server API, go to:
https://developer.enablex.io/latest/server-api/



## 3 iOS Toolkit

This Sample Applcation uses EnableX iOS Toolkit to communicate with EnableX Servers to initiate and manage Real Time Communications. You might need to update your Application with latest version of EnableX iOS Toolkit time as and when a new release is avaialble.   

* Documentation: https://developer.enablex.io/latest/client-api/ios-toolkit/
* Download Toolkit: https://developer.enablex.io/resources/downloads/#ios-toolkit


## 4 Application Walk-through

### 4.1 Create Token

We create a Token for a Room Id to get connected to EnableX Platform to connect to the Virtual Room to carry out a RTC Session.

To create Token, we make use of Server API. Refer following documentation:
https://developer.enablex.io/latest/server-api/rooms-route/#create-token


### 4.2 Connect to a Room, Initiate & Publish Stream

We use the Token to get connected to the Virtual Room. Once connected, we intiate local stream and publish into the room. Refer following documentation for this process:
https://developer.enablex.io/latest/client-api/ios-toolkit/enxrtc/


### 4.3 Play Stream

We play the Stream into EnxPlayerView Object.
``` 
let streamView = EnxPlayerView(frame: CGRect) 
self.view.addSubview(streamView) 
localStream.attachRenderer(streamView) 
  ```
More on Player: https://developer.enablex.io/latest/client-api/ios-toolkit/basic-functions/#play-stream

### 4.4 Handle Server Events

EnableX Platform will emit back many events related to the ongoing RTC Session as and when they occur implicitly or explicitly as a result of user interaction. We use delegates of handle all such events.

``` 
/* Example of Delegates */

/* Delegate: didConnect 
Handles successful connection to the Virtual Room */ 

func room(_ room: EnxRoom?, didConnect roomMetadata: [AnyHashable : Any]?) { 
    /* You may initiate and publish your stream here */
} 


/* Delegate: didError
 Error handler when room connection fails */
 
func room(_ room: EnxRoom?, didError reason: String?) { 

} 

 
/* Delegate: didAddedStream
 To handle any new stream added to the Virtual Room */
 
func room(_ room: EnxRoom?, didAddedStream stream: EnxStream?) { 
    /* Subscribe Remote Stream */
} 

/* Delegate: activeTalkerList
 To handle any time Active Talker list is updated */
  
func room(_ room: EnxRoom?, activeTalkerList Data: [Any]?) { 
    /* Handle Stream Players */
}
```
### 4.5 Start Canvas
    Enablex Platform provide api to start/stop canvas. 
    To initiate canvas customer must have canvas subscription enable in their room.
    
    Note:- Right now only one user can share his canvas at any given time in the room. The canvas stream can be edited in real time
    only by the person who started the canvas. Exm:- If in room their are 2 participants 'A' and 'B', If participant 'A' start the canvas then participant 'B' can only view and vice varsa.
    Method:
    ``` 
        Start Canvas
            -(void)startCanvas:(UIView*_Nonnull)view;
            /*
            Here user needs to pass the view, which he/she wants to publish as another stream into the room over the canvas channel. 
            */
        Stop Canvas
            -(void)stopCanvas;
    ``` 
    Delegates:
    ``` 
    /*
        Owner of start/Stop canvas (The person startting/Stoping canvas) will receive acknowledgement delegates as below */
        //Start Canvas
        -(void)room:(EnxRoom *_Nullable)room didStartCanvasACK:(NSArray *_Nullable)Data;
        //Stop Canvas
        -(void)room:(EnxRoom *_Nullable)room didStoppedCanvasACK:(NSArray *_Nullable)Data;
    /*
        Other participants in same room will receive delegates as Below */
        //Once Canvas start
        -(void)room:(EnxRoom*)room canvasStarted:(NSArray * _Nullable)Data;
        //Once Canvas Stopen
        -(void)room:(EnxRoom*)room canvasStopped:(NSArray * _Nullable)Data;
    ``` 

### Exploring the sample app
    Join Screen :- On this screen user creates the roomID, and share this roomID to other end user who has to join this room.
![home](./home.PNG)  

   ``` 
   Here in this screen 2nd participant joins into the same room. On Top of the screen, option button is available to start the  canvas.
   ```
![conf1](./conf1.PNG) 
   ```
   Once user clicks start canvas, a white page will open, where user can draw, add interactive buttons or play a video etc. In this example we will change the canvas  color and its name on "Change color button tap"
```
![conf2](./conf2.PNG)   ![rec1](./rec1.PNG) 
```
    Changed color from white to Red.
```
![conf3](./conf3.PNG)   ![rec2](./rec2.PNG) 
```
    Changed color from Red to Blue.
```
![conf4](./conf4.PNG)   ![rec3](./rec3.PNG)
![conf5](./conf5.PNG)   ![rec3](./rec4.PNG)
```
    As you can see once any user starts the canvas, users in same room views the activity on the canvas, based on the interactive actions initiated from canvas owner.
```
    
## 5 Trial

EnableX provides hosted Vemo Application of different use-case for you to try out.

1. Try a quick Video Call: https://try.enablex.io
2. Sign up for a free trial https://portal.enablex.io/cpaas/trial-sign-up/
