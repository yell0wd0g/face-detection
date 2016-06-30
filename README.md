Users interacting with WebCams or other Video Capture Devices have become accustomed to a series of features, including seeing the human faces in the scene being highlighted (usually by surrounding them with a bounding box). This is particularly true in the case of mobile devices.

Use cases
=========

* Live video feeds would like to identify faces in the scene to mark those areas as highly salient and e.g. give hints to image or video encoders.
* Social networks would like to quickly identify the humans in a picture and offer the user e.g. the possibility of "tagging" which name corresponds to what face.
*

Possible future use cases
=========================

* Blabla

Relevant sources
=========

Current Workarounds
=========

*

Potential for misuse
=========

Platform specific implementation notes
======================================

## Mac OS X / iOS

CoreImage library includes a CIDetector class that provides not only Face Detection, but also QR, Text and Rectangles.


(CIDetector class, Mac OS X)[https://developer.apple.com/library/mac/documentation/CoreImage/Reference/CIDetector_Ref/]
(CIDetector class, iOS)[https://developer.apple.com/library/ios/documentation/CoreImage/Reference/CIDetector_Ref/]

## Android

Android Camera2 API provides functionality to

https://developer.android.com/reference/android/hardware/camera2/CaptureRequest.html#STATISTICS_FACE_DETECT_MODE

Rough sketch of a proposal
==========================


```
typedef (HTMLImageElement or
         HTMLVideoElement or
         HTMLCanvasElement or
         Blob or
         ImageData or
         ImageBitmap) ImageBitmapSource;

partial interface navigator {
  Promise <sequence<DOMRect>> detectFaceOrNotOrMaybe(ImageBitmapSource);
};
```
Usage
=====
```
 navigator.detectFaceOrNotOrMaybe().then(function(bounding_boxes) {
  for (DOMRect face :  bounding_boxes) {
    console.log("Face detected at (" + face.x + "x" + face.y + ") with size (" +
        face.width + "x" + face.height + ")";
  }
 }).catch(function() {
   alert("It just didn't work");
 });
```

Notes
=====


Open questions
==============

