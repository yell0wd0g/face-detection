(Originally proposed on Mozilla's Dev Platform list by Andrea Marchesini and Ehsan Akhgari - moving here to begin formal standardization discussions.)

Use cases
=========
 
* I have a media player app, and I want to use the media keys on my keyboard on desktop to control it.
* I have a media player app and I want to use the headset controls on my headphones to control it.
* I have a media player app and I want to use the mobile soft keys (e.g. as done on iOS and Android lock screens) to control it.
 
Possible future use cases
=========================
* I have registered a media player app and I want it to start up when I press the play key. (We could possibly dispatch an event to the SW, etc., out of scope for now)
 
Relevant sources
=========
 
* http://smus.com/remote-controls-web-media/
* http://paulrouget.com/e/mediaevents/
* http://beardedspice.com/
* https://groups.google.com/forum/#!msg/mozilla.dev.webapi/TSDZCeWYiDU/kEXkuQG83ngJ
(Originally proposed on Mozilla's Dev Platform list by Andrea Marchesini and Ehsan Akhgari - moving here to begin formal standardization discussions.)

Use cases
=========
 
* I have a media player app, and I want to use the media keys on my keyboard on desktop to control it.
* I have a media player app and I want to use the headset controls on my headphones to control it.
* I have a media player app and I want to use the mobile soft keys (e.g. as done on iOS and Android lock screens) to control it.
 
Possible future use cases
=========================
* I have registered a media player app and I want it to start up when I press the play key. (We could possibly dispatch an event to the SW, etc., out of scope for now)
 
Relevant sources
=========
 
* http://smus.com/remote-controls-web-media/
* http://paulrouget.com/e/mediaevents/
* http://beardedspice.com/
* https://groups.google.com/forum/#!msg/mozilla.dev.webapi/TSDZCeWYiDU/kEXkuQG83ngJ
* http://developer.ubuntu.com/api/devel/ubuntu-13.10/javascript/web-docs/
 
Platform specific implementation notes
======================================
 
## Windows
 
It seems like Windows will dispatch a WM_APPCOMMAND message when a media key is pressed. The application has an opportunity to handle the message and return TRUE, or pass it along to DefWindowProc to allow the message to be delivered to another application.
 
See http://msdn.microsoft.com/en-us/library/windows/desktop/ms646275%28v=vs.85%29.aspx
 
## Mac OS X
 
You need to write specific code to relinquish the event interceptor when your application goes to the background. http://overooped.com/post/2593597587/mediakeys explains the reason. This is how to do it: https://github.com/nevyn/SPMediaKeyTap. This library is used by apps such as VLC.
 
## iOS
 
There is a way to capture the ipod music controls on the lockscreen: http://stackoverflow.com/questions/3196330/how-to-enable-ipod-controls-in-the-background-to-control-non-ipod-music-in-ios-4 But it seems like we need to know in advance whether we need to capture these keys, which maps well with what we are thinking of (navigator.requestMediaKeys).
 
The same API seems to give you access to the media keys on the headphones too. This is documented here: https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Remote-ControlEvents/Remote-ControlEvents.html#//apple_ref/doc/uid/TP40009541-CH7-SW3 

It seems like your app can claim to be the first responder to the event, and if the event is not handled by your app, iOS will send it to the next one, and so on.
 
## Android
 
https://developer.android.com/training/managing-audio/volume-playback.html
 
The app has to register a BroadcastReceiver in its manifest that listens for the ACTION_MEDIA_BUTTON action broadcast. When the action is received it contains EXTRA_KEY_EVENT informations such as KeyEvent.KEYCODE_MEDIA_PLAY, KeyEvent.KEYCODE_MEDIA_PAUSE, and others.
 
The receiver has to be registered and unregistered.
 
## Linux
 
Xorg has custom X codes for media buttons such as XF86AudioMute, XF86AudioNext, XF86AudioPause, XF86AudioPlay etc. They are emitted as normal events.
 
Rough sketch of a proposal
==========================
**NOTE**: We are aware that `MediaController` clashes with an existing HTML interface - we plan to rename it something else! 

```
partial interface navigator {
  Promise <MediaController> requestMediaController();
};
enum MediaKeyEventType {
  "play", "pause", "playpause", "next", "previous",
  // possibly other codes
};
dictionary MediaKeyEventInit: EventInit {
  MediaKeyEventType detail = "play";
};
[Constructor(DOMString type, optional MediaKeyEventInit init)]
interface MediaKeyEvent: Event {
  readonly attribute MediaKeyEventType detail;
};
interface MediaController: EventTarget {
  attribute EventHandler onmediakey;
  // Do we need a revoke method?
  // void revoke();
  // This attribute is used to see if the play/pause event has been received correct.
  attribute boolean mediaActive;
  // An image to show when the device is locked.
  attribute(DOMString or URL or Blob or HTMLImageElement or HTMLCanvasElement or HTMLVideoElement) mediaImage;
  // Extra info to show.
  attribute DOMString mediaTitle;
  attribute DOMString mediaDuration;
  // other attributes.
};
```
Usage
=====
```
 navigator.requestMediaController().then(function(controller) {
   controller.audioActive = true;
   controller.mediaTitle =
     "Maurice Ravel - Piano Concerto for the left hand"
   controller.onmediakey = function(e) {
     switch (e.detail) {
       case "play":
         // ...
         break;
       case "pause":
         // ...
         break;
     }
   };
 }).catch(function() {
   alert("Access to media keys not granted");
 });
```
 
Notes
=====
 
We intentionally do not use the DOM3 media KeyboardEvent keys, since on some OSes such as Android and iOS it would not be possible to map these events to a keydown/keyup event in a sensible way.
 
Technically we could get rid of MediaController, and dispatch the event on Window/Navigator/etc. ehsan has no strong preferences, and baku prefers to keep things this way. ehsan likes the fact that this model plays nice with the promise returned from requestMediaKeys.
 
The asynchronous requestMediaKeys() function provides a chance for the UA to show a UI asking the user to confirm if they choose to do so, etc.
 
In the future we may be able to extend MediaController to allow the application to provide some information about the currently playing track, such as the title, the picture, etc. That will enable us to build soft media controls on the lockscreen for example similar to the way that iOS and Android do.
 
We should also add the ability for the webapp to use the MediaController to send information to the platform about song-name/album-name/artist-name/album-art/timeposition.
 
We also need the webapp send information about if it's currently playing or not, so that the platform knows if it should display a "play" or a "pause" button on a software keyboard or a widget. This can also be useful to enable the platform to display playing-status next to song name etc. This might also enable us to dispatch the "play" or "pause" event rather than the "playpause" event.
 
Open questions
==============
 
The MediaKey* terminology is closed to what is used in the EME spec. ehsan thinks this is fine because it is not likely for most apps to want to use EME, so there are no big chances for confusion.
 
Is it OK to diverge from the DOM KeyboardEvent codes for this?
 
The policy choice as to what to do when two web apps request access to this API is left to the UA, and the UA has the freedom to adopt their own policies. Is that OK? (ehsan thinks yes.)
 
As far as the ergonomics of using the API is concerned, should we return the same MediaController object from requestMediaKeys() no matter how many times the author calls it?
 
Given the possibility of future extensions to add more things to MediaController as discussed above, should we pick a better name for it?
