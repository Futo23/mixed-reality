---
title: Tracking loss in Unity
description: Learn about tracking loss—the inability of the device to locate itself in the world—and the default and manual techniques for handling it.
author: thetuvix
ms.author: alexturn
ms.date: 03/21/2018
ms.topic: article
keywords: Unity, tracking loss, tracking loss image
---



# Tracking loss in Unity

When the device cannot locate itself in the world, the app experiences "tracking loss". By default, Unity will pause the update loop and display a splash image to the user. When tracking is regained, the splash image goes away and the update loop continues.

As an alternative, the user can manually handle this transition by opting out of the setting. All content will seem to become body locked during tracking loss if nothing is done to handle it.

## Default Handling

By default, the update loop of the app as well as all messages and events will stop for the duration of tracking loss. At that same time, an image will be displayed to the user. You can customize this image by going to Edit->Settings->Player, clicking Splash Image, and setting the Holographic Tracking Loss image.

## Manual Handling

To manually handle tracking loss, you need to go to **Edit** > **Project Settings** > **Player** > **Universal Windows Platform settings tab** > **Splash Image** > **Windows Holographic** and uncheck "On Tracking Loss Pause and Show Image". After which, you need to handle tracking changes with the APIs specified below.

**Namespace:** *UnityEngine.XR.WSA*<br>
**Type:** *WorldManager*

* World Manager exposes an event to detect tracking lost/gained (*WorldManager.OnPositionalLocatorStateChanged*) and a property to query the current state (*WorldManager.state*)
* When the tracking state is not active, the camera will not appear to translate in the virtual world even as the user translates. This means objects will no longer correspond to any physical location and all will appear body locked.

When handling tracking changes on your own you either need to poll for the state property each frame or handle the *OnPositionalLocatorStateChanged* event.

### Polling

The most important state is *PositionalLocatorState.Active* which means tracking is fully functional. Any other state will result in only rotational deltas to the main camera. For example:

```cs
void Update()
{
    switch (UnityEngine.XR.WSA.WorldManager.state)
    {
        case PositionalLocatorState.Active:
            // handle active
            break;
        case PositionalLocatorState.Activating:
        case PositionalLocatorState.Inhibited:
        case PositionalLocatorState.OrientationOnly:
        case PositionalLocatorState.Unavailable:
        default:
            // only rotational information is available
            break;
    }
}
```

### Handling the OnPositionalLocatorStateChanged event

Alternatively and more conveniently, you can also subscribe to *OnPositionalLocatorStateChanged* to handle the transitions:

```cs
void Start()
{
    UnityEngine.XR.WSA.WorldManager.OnPositionalLocatorStateChanged += WorldManager_OnPositionalLocatorStateChanged;
}

private void WorldManager_OnPositionalLocatorStateChanged(PositionalLocatorState oldState, PositionalLocatorState newState)
{
    if (newState == PositionalLocatorState.Active)
    {
        // Handle becoming active
    }
    else
    {
        // Handle becoming rotational only
    }
}
```

## Next Development Checkpoint

If you're following the Unity development checkpoint journey we've laid out, you're in the midst of exploring the Mixed Reality platform capabilities and APIs. From here, you can proceed to the next topic: 

> [!div class="nextstepaction"]
> [Keyboard input](keyboard-input-in-unity.md)

Or jump directly to deploying your app on a device or emulator:

> [!div class="nextstepaction"]
> [Deploy to HoloLens or Windows Mixed Reality immersive headsets](using-visual-studio.md)

You can always go back to the [Unity development checkpoints](unity-development-overview.md#3-platform-capabilities-and-apis) at any time.

## See also
* [Handling tracking loss in DirectX](coordinate-systems-in-directx.md#handling-tracking-loss)