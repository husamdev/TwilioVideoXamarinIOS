# Twilio Video Xamarin IOS

Twilio Video iOS SDK binding for Xamarin

[![NuGet][nuget-img]][nuget-link]

[nuget-img]: https://img.shields.io/badge/nuget-3.2.5-blue.svg
[nuget-link]: https://www.nuget.org/packages/Twilio.Video.XamarinBinding

## How to Build

### Twilio.Video iOS 3.2.5 (April 23, 2020)
```
sh bootstrapper.sh
```

Add --registrar:static as additional mtouch arguments on iOS Build dialog for your iOS application.
Sometimes adding -cxx is also required.

## Documentation

### Info.plist
Enable Background Modes and select Audio, AirPlay, and Picture in Picture
You should also add permissions to access microphone and camera
```
<key>NSMicrophoneUsageDescription</key>
<string>MyApp wants to use your microphone.</string>
<key>NSCameraUsageDescription</key>
<string>MyApp wants to use your camera.</string
```
### Using TVICameraSource API
First we create ```CameraSource``` and use that source to create ```TVILocalVideoTrack```
```
var cameraSource = new TVICameraSource(new TVICameraSourceDelegate());
var localVideoTrack = TVILocalVideoTrack.TrackWithSource(cameraSource, true, "Camera");
```
Now it is time to start producing frames from one of the built in camera
```
var frontCamera = TVICameraSource.CaptureDeviceForPosition(AVCaptureDevicePosition.Front);
// Start capturing with the device that we discovered.
cameraSource.StartCaptureWithDevice(frontCamera, GetVideoFormat(frontCamera), null);
```
But you cannot see anything right now. We have to add a renderer to the ```localVideoTrack``` created previously.
First create an instance of TVIVideoView, TVIVideoView draws video frames from a TVIVideoTrack. 
TVIVideoView inherits from UIView.

```
var videoView = new TVIVideoView(CGRect.Empty, new VideoViewDelegate(this))
{
    TranslatesAutoresizingMaskIntoConstraints = false, // then constraint view
    ContentMode = UIViewContentMode.ScaleAspectFill, // supports ScaleToFill, ScaleAspectFill and ScaleAspectFit
    BackgroundColor = UIColor.Black
};

this.View.AddSubview(videoView);
```
Now add renderer to the ```localVideoTrack```
```
localVideoTrack.AddRenderer(view);
```
### Connect to a Room with local video track
Next, we want to connect to a Room with the TVILocalVideoTrack we created earlier.
```
var builderBlock = new TVIConnectOptionsBuilderBlock(builder =>
{
    builder.RoomName = "room-name";
    builder.VideoTracks = new TVILocalVideoTrack[] { localVideoTrack };
});

var connectOptions = TVIConnectOptions.OptionsWithToken("access-token", builderBlock);
var roomDelegate = new RoomDelegate(viewController);
var room = TwilioVideoSDK.ConnectWithOptions(connectOptions, roomDelegate);
```
to be continued...

## Sample

####  I don't have C# version of twilio quickstart application, so I highly recommend you to read about using native library bindings for xamarin and check official Twilio quickstart guides.

[delegate sample](sample)

[voice-quickstart-swift](https://github.com/twilio/video-quickstart-ios)

## Contributions

Members of the community have contributed to improving and update bindings:

- [MKGNZ](https://github.com/MKGNZ)

If you have a bugfix or an update you'd like to add, please open an issue. 
All pull requests should be opened against the `master` branch.
