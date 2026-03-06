# WizXR-SDK
Wi@rdXR SDK
## Version 0.1.0-Alpha

WizXR is an experimental predictive spatial computing SDK designed to estimate how a user will move through space over time. It provides a lightweight runtime that can estimate walking distance, predict future position, and map those predictions into spatial coordinates suitable for augmented reality or visualization.

The goal of WizXR is to enable applications that combine motion prediction, time-based forecasting, and spatial visualization.

A flagship concept for WizXR is predicting where a user will be when a song finishes playing, allowing an application to visualize that destination in space.

This repository contains the WizXR SDK prototype, including the runtime engine, prediction modules, spatial math utilities, and a C bridge API that allows the SDK to be used from multiple platforms including Android.

# Status

Current version: 0.1.0-Alpha

This is an early prototype release.

The SDK is functional but still evolving. APIs may change in future releases.

# Current capabilities include:

session-based runtime API

walking distance estimation

short-term distance prediction

spatial endpoint computation

cross-platform C bridge API

Windows CLI demonstration

Android native prototype integration

Future versions will expand the SDK with improved sensor integration and spatial visualization capabilities.

# Core Idea

WizXR focuses on predictive movement.

Instead of only reacting to where a user is now, the SDK predicts where the user will be in the future.

# Example use case:

A user starts playing a song.

The SDK estimates their walking cadence.

The SDK predicts how far the user will walk by the time the song ends.

The application visualizes the predicted destination.

Applications can use this prediction to create spatial experiences such as:

augmented reality navigation cues

fitness tracking with future projections

interactive music experiences

motion-based gameplay

predictive spatial user interfaces

# Architecture

The SDK is organized into several modules.

wizcore
    Core SDK utilities and base structures

wizsensor
    Sensor abstractions and motion estimation

wizpredict
    Distance prediction models

wizruntime
    Session runtime and prediction pipeline

wizar
    Spatial mathematics and coordinate mapping

wizxr_bridge
    C API bridge for platform integrations

Applications are expected to interact with the SDK primarily through the bridge API.

# Public API Overview

The SDK exposes a session-handle based C interface for maximum compatibility across platforms.

Main workflow:

create session
     ↓
update session (time ticks)
     ↓
query prediction results
     ↓
destroy session
Create session
WizxrSession* wizxr_session_create(WizxrError* err);

Creates a runtime session.

Update session
int wizxr_session_update(WizxrSession* session,
                         double deltaSeconds,
                         WizxrError* err);

Advances the runtime by a time step.

Applications typically call this periodically.

Query prediction results
int wizxr_session_get_distances(
    WizxrSession* session,
    double predictAheadSeconds,
    WizxrDistances* outDistances,
    WizxrError* err);

Returns:

current estimated distance

predicted future distance

spatial endpoints

Destroy session
void wizxr_session_destroy(WizxrSession* session);

Releases runtime resources.

Coordinate System

WizXR uses a simple right-handed coordinate system.

x = right
y = up
z = forward

Endpoints are computed as:

origin + forward * predictedDistance

Distances are measured in meters.

Time values are measured in seconds.

# Example Usage

Below is a minimal example using the bridge API.

#include <wizxr/bridge/wizxr_bridge.h>
#include <iostream>

int main()
{
    WizxrError err{};
    WizxrSession* session = wizxr_session_create(&err);

    if (!session)
    {
        std::cout << "Failed: " << err.message << std::endl;
        return 1;
    }

    const double delta = 0.1;

    for (int i = 0; i < 100; ++i)
    {
        wizxr_session_update(session, delta, &err);
    }

    WizxrDistances distances{};

    wizxr_session_get_distances(
        session,
        10.0,
        &distances,
        &err);

    std::cout << "Current distance: "
              << distances.currentDistanceMeters << " m\n";

    std::cout << "Predicted distance: "
              << distances.predictedDistanceMeters << " m\n";

    wizxr_session_destroy(session);

    return 0;
}

# SDK Directory Layout
WizXR-0.1.0-Alpha
│
├── include
│   └── wizxr
│       ├── bridge
│       ├── runtime
│       ├── predict
│       ├── sensor
│       ├── ar
│       └── core
│
├── lib
│   ├── windows
│   └── android
│
├── samples
│   ├── demo_cli
│   └── android_native_test
│
├── README.md
├── CHANGELOG.md
└── VERSION.txt

# Building the SDK

The SDK uses CMake.

Requirements

CMake 3.21+

C++17 compatible compiler

Ninja (recommended)

Windows build
mkdir build
cd build
cmake .. -G Ninja
ninja

The CLI sample will be built as:

apps/demo_cli/wizxr_demo_cli.exe
Android build

Use the Android NDK toolchain:

cmake -G Ninja ^
  -DCMAKE_TOOLCHAIN_FILE=%ANDROID_NDK%/build/cmake/android.toolchain.cmake ^
  -DANDROID_ABI=armeabi-v7a ^
  -DANDROID_PLATFORM=android-24 ^
  ..

Then build:

ninja

This produces static libraries for Android.

Android Prototype

The SDK has been successfully integrated into an Android test application via JNI.

The prototype demonstrates:

native runtime execution

prediction results returned to Kotlin

real device testing

Future versions will include:

music integration

sensor streaming

spatial visualization

Future Development

Planned areas of development include:

improved step estimation

real sensor input integration

heading estimation

spatial visualization

fallback AR tracking for unsupported devices

enhanced prediction models

cross-platform SDK packaging

# Roadmap
v0.1 Alpha

Initial prototype.

runtime session API

prediction model

spatial endpoint math

Android native integration

v0.2

Sensor-driven runtime.

real sensor inputs

cadence estimation improvements

improved prediction stability

v0.3

Visualization layer.

spatial path visualization

camera integration

AR-like projection without device restrictions

# License
MiT license

This SDK is currently provided for experimental and research purposes.

License terms will be defined in future releases.

# Author

WizXR SDK
Developed as part of the Wiz@rdXR initiative.
