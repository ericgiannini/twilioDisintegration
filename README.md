

# Schematic: Agora’s iOS SDK Mapping Guide for Twilio Video functionalities



## Practical Coding Implementations of Mapped Functions 
### Importing SDK

#### Twilio
```Swift
import TwilioVideo
```
#### Agora
```Swift
import AgoraRtcEngineKit
```
### Accessing SDK / Initialization
#### Twilio: instances

```Swift
  // Video SDK components
    var room: TVIRoom?
    var camera: TVICameraSource?
    var localVideoTrack: TVILocalVideoTrack?
    var localAudioTrack: TVILocalAudioTrack?
    var remoteParticipant: TVIRemoteParticipant?
    var remoteView: TVIVideoView?
```

#### Agora: a single instance 
```Swift
let agoraKit: AgoraRtcEngine 
``` 

### Toggle Microphone
#### Twilio: self.localAudioTrack?.isEnabled

```Swift
  @IBAction func toggleMic(sender: AnyObject) {
        if (self.localAudioTrack != nil) {
            self.localAudioTrack?.isEnabled = !(self.localAudioTrack?.isEnabled)!
            
            // Update the button title
            if (self.localAudioTrack?.isEnabled == true) {
                self.micButton.setTitle("Mute", for: .normal)
            } else {
                self.micButton.setTitle("Unmute", for: .normal)
```           
        
    

#### Agora: agoraKit.muteLocalAudioStream(sender.isSelected)

```Swift
@IBAction func didClickMuteButton(_ sender: UIButton) {
    sender.isSelected = !sender.isSelected
    agoraKit.muteLocalAudioStream(sender.isSelected)
    resetHideButtonsTimer()
}
```


### Toggle Camera
#### Twilio: TVICameraSource.captureDevice(for: .back)


```Swift
@objc func flipCamera() {
        var newDevice: AVCaptureDevice?

        if let camera = self.camera, let captureDevice = camera.device {
            if captureDevice.position == .front {
                newDevice = TVICameraSource.captureDevice(for: .back)
            } else {
                newDevice = TVICameraSource.captureDevice(for: .front)
            

            if let newDevice = newDevice {
                camera.select(newDevice) { (captureDevice, videoFormat, error) in
                    if let error = error {
                        self.logMessage(messageText: "Error selecting capture device.\ncode = \((error as NSError).code) error = \(error.localizedDescription)")
                    } else {
                        self.previewView.shouldMirror = (captureDevice.position == .front)
```                    
                
            
        
    

#### Agora: agoraKit.switchCamera()
@IBAction func didClickSwitchCameraButton(_ sender: UIButton) {
    sender.isSelected = !sender.isSelected
    agoraKit.switchCamera()
    resetHideButtonsTimer()
}

### Leave Room / Channel 
#### Twilio: self.room!.disconnect()
```Swift
    @IBAction func disconnect(sender: AnyObject) {
        self.room!.disconnect()
        logMessage(messageText: "Attempting to disconnect from room \(room!.name)")
```

#### Agora: agoraKit.leaveChannel(nil)

```Swift
func leaveChannel() {
    agoraKit.leaveChannel(nil)
    logMessage(messageText: "Attempting to leave channel")
    agoraKit = nil
}
```

### Join Room / Channel 


#### Twilio: TwilioVideo.connect(with: connectOptions, delegate: self)

```Swift 
room = TwilioVideo.connect(with: connectOptions, delegate: self)
```

#### Agora: agoraKit.joinChannel()
```Swift
func joinChannel() {
    agoraKit.joinChannel(byKey: nil, channelName: "demoChannel1", info:nil, uid:0) {[weak self] (sid, uid, elapsed) -> Void in
        if let weakSelf = self {
            weakSelf.agoraKit.setEnableSpeakerphone(true)
            UIApplication.shared.isIdleTimerDisabled = true
}
```

### Configure Video 
#### Twilio: TVIConnectOptions.init(token: accessToken) {(builder) in}

```Swift
/ Preparing the connect options with the access token that we fetched (or hardcoded).
        let connectOptions = TVIConnectOptions.init(token: accessToken) { (builder) in
            
            // Use the local media that we prepared earlier.
            builder.audioTracks = self.localAudioTrack != nil ? [self.localAudioTrack!] : [TVILocalAudioTrack]()
            builder.videoTracks = self.localVideoTrack != nil ? [self.localVideoTrack!] : [TVILocalVideoTrack]()
            
            // Use the preferred audio codec
            if let preferredAudioCodec = Settings.shared.audioCodec {
                builder.preferredAudioCodecs = [preferredAudioCodec]
            
            
            // Use the preferred video codec
            if let preferredVideoCodec = Settings.shared.videoCodec {
                builder.preferredVideoCodecs = [preferredVideoCodec]
            
            
            // Use the preferred encoding parameters
            if let encodingParameters = Settings.shared.getEncodingParameters() {
                builder.encodingParameters = encodingParameters
 ```           
            


#### Agora: agoraKit.setVideoEncoderConfiguration()

```Swift
func setupVideo() {
    agoraKit.enableVideo()  // Enables video mode.
    agoraKit.setVideoEncoderConfiguration(
        AgoraVideoEncoderConfiguration(size: AgoraVideoDimension640x360,
                                  frameRate: .fps15,
                                    bitrate: AgoraVideoBitrateStandard,
                            orientationMode: .adaptative)
    ) // Default video profile is 360P
}
```

### Configure remote tokenization
#### Twilio: accessToken = try TokenUtils.fetchToken(url: tokenUrl)

```Swift
// MARK: IBActions
    @IBAction func connect(sender: AnyObject) {
        // Configure access token either from server or manually.
        // If the default wasn't changed, try fetching from server.
        if (accessToken == "TWILIO_ACCESS_TOKEN") {
            do {
                accessToken = try TokenUtils.fetchToken(url: tokenUrl)
            } catch {
                let message = "Failed to fetch access token"
                logMessage(messageText: message)
                return
}
```            
        
#### Agora:  agoraKit.joinChannel

```Swift
func joinChannel() {
  agoraKit.joinChannel(by Token: nil, channelId: "demoChannel1", info:nil, uid:0){[weak self] (sid, uid, elapsed) -> Void in
      // Join channel "demoChannel1" 
}
```


