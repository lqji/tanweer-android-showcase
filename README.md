# Tanweer (تنوير) for Android

This is the Android version of Tanweer, built with Kotlin and Jetpack Compose. The goal was pixel parity with the iOS app, same Mushaf typesetting, same prayer time and Qiblah logic, same widgets, rebuilt screen by screen against the original.

This repo is a portfolio case study. The app itself is closed source, so what you will find here is screenshots, a plain explanation of how it is built, and a few of the harder bugs I ran into along the way.

Not on the Play Store yet, it is in its final release and QA pass.

## Screenshots

<p align="center">
  <img src="screenshots/01-home.png" width="180" />
  <img src="screenshots/02-mushaf-reader.png" width="180" />
  <img src="screenshots/03-qiblah.png" width="180" />
  <img src="screenshots/04-prayer-times.png" width="180" />
</p>

## Stack

Kotlin and Jetpack Compose for the UI, with Hilt handling dependency injection across the app and its home screen widgets. Prayer times and Qiblah direction use the device's location. Audio playback uses Media3. Release builds are signed through a keystore setup that also works from environment variables, so a real signed build can be produced without secrets sitting in the repo.

## How it is put together

Compose screens stay light and just render state. A small set of repositories and services own the actual logic, prayer time calculation, location, the audio player, local storage, and Hilt wires those same instances into both the app and the widgets, so everything reads from one place instead of duplicating state.

## Some of the harder problems

Porting for pixel parity instead of just feature parity meant treating the iOS app as the spec and comparing behavior frame by frame rather than rebuilding from memory. Most of the bugs below only surfaced because of that comparison.

One of the widgets crashed the moment they rendered, even though everything compiled fine. All five home screen widgets were passing plain numbers into padding and sizing functions, and on this framework a plain number gets treated as a resource id instead of a size. The compiler had no way to catch it. Only a lint check caught it, and it turned out to affect every widget at once.

There was also a verse numbering bug almost identical to one on the iOS side, same font, same limitation where its automatic digit styling only had rules for one or two digit numbers, so anything past ninety nine came out garbled. Diagnosed by pulling screenshots straight off a device and looking at the pixels rather than guessing from the font itself, and fixed the same way, drawing the circle and the digits separately for longer numbers.

A smaller one, the playback speed label was being formatted without a locale, so on an Arabic or Urdu device it would quietly switch to different digits and a different decimal separator. Pinning that one label to a fixed locale fixed it without affecting anything else in the app.

And a scrubber bug where dragging the audio progress bar could freeze mid drag if the layout shifted at just the wrong moment, because the gesture handling was tied to the scrubber's measured size instead of being independent of it.

## Also by me

Tanweer for iOS: https://github.com/lqji/tanweer-ios-showcase
Type Faster: https://github.com/lqji/type-faster-showcase
Full portfolio: https://github.com/lqji/portfolio

Ahmed Abdullah
