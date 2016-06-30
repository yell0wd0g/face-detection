
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

*

Platform specific implementation notes
======================================

## Mac OS X / iOS

## Android

Rough sketch of a proposal
==========================


```
partial interface navigator {
  Promise <Whatever> detectFaceOrNotOrMaybe();
};
```
Usage
=====
```
 navigator.detectFaceOrNotOrMaybe().then(function(result) {
  // Bounding box
 }).catch(function() {
   alert("It just didn't work");
 });
```

Notes
=====


Open questions
==============

