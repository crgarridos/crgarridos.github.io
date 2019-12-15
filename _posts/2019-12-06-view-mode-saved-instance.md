---
layout: post
title: Wrapping up Android Saved Instance State
description: Sometimes the syntactic sugar of kotlin can tempt us to write code less readable in some ways
tags: 
    - Android lifecycle
    - Process lifecycle
    - ViewModel
    - Save instance state
---

Level: Basic/Intermediary

In this article we will dive into how we could preserve the state of our views. Avoiding the user to lost the work/flow he has already done in our app.
@leboncoin we mostly use ViewModel, but this apply to any of thz popular presentation patterns out there. 

We wil visit the following content:

- 
- blalabala blalabala blalabala
- blalabala blalabala 

Nothing left to say, let's jump into it.

<!-- more -->

# Lifecycle basics concepts

As Android developers we are forced know the activity/fragments' (henceforth the View) lifecycles. It's not necessary to have the whole lifecycle in memory but at least recognize the points of interactions between our UI and the users. 
A pretty complete lifecycle diagram can be consulted [here](https://github.com/xxv/android-lifecycle) and to have a better understanding of the window management interactions [here](https://github.com/JoseAlcerreca/android-lifecycles) is also an very usefull resource.

To recap the resources above and for the porpouse of this article, we can just focus on:

- When OnCreate a new view is created.
We have the oportunity to restore a previous state
- When OnPause the view is not longer active
We have to stop drawing on the view as the view could not be bound anymore
-When OnStop the window is non visible by the user.
- onSaveIntanceState to save the pièces of data that are key to restore the screen that the user left.

We are particularlly interested on when our view is getting recreated from a previous state. There are two mainly categories after this can happen :

- Configuration changes
- Process lifecycle

Let's dive into them

## Configuration changes

These are changes that affects our view and the system will drop the current View's hierachy and mount it up again according to the new configuration. 
These are system UI related events such as:

- Screen rotation
- SoftInput changes
- Language changes
- Theme Changes
- Day/night mode

These changes are requested by the system and your the view hierarchy will be, by default, recreated and will provide us a way to save the state for later passing it through again when our View get recreated. Here we have the oportunity to save a current search or some displayed data. Some of the data the user put into our view gets automatically saved as this is managed by system Views, but others require to be explicitly managed.  

onCreate => onDestroy

Anyway as the instance in memory of our View still the same, a common practice is just using a property member in the view class. As this works very well upon the user has view in front of him/her, for the most simplest usages this is sometimes enough. 
But we have to have in mind that the system can require the resources our app is holding whenever it wants.
We are going to talk about how it is done in the later sections.

### ViewModel + LiveData

To avoid managing this, jetpack offers us ViewModel + LiveData(TODO ref here). The first one will persist to configuration changes of our View, as it remains in memory, the state of it isn't affected by the destruction of the View. LiveData by its side offers us a way to observe the changes of a data model, if a configuration change occurs the last value offered is avalaible to the View. also as the observation is tied to the View's lifecycle we can be confident in posting new values even if the View is not ready to consume it.

## Process lifecycle

> See the [documentation](https://developer.android.com/guide/components/activities/process-lifecycle) reference to more details
https://developer.android.com/reference/android/app/Activity.html#ProcessLifecycle

Android OS treats each application as an independent linux process. It stablishes an order of priority through several states. With this it can determinate if the resources of the device could them be freed until sufficient resources are available for other functions/processes to work.

The hierarchy importance can be defined as four states:

- **Foreground state**  
  A user action is interacting directly with the app. The process is considered as this if the following conditions are meet: An `Activity` is in its resumed state, a `BroadcastReceiver` is executing its onReceived method, a `Service` running onStart, onCreate or onDestroy.  
  This is the higher priority, only a few processes are in this state and the system will kill it as last resort. 
- **Visible state**  
  An action is being executed and the user is aware of. For example, an `Activity` is partially visible and onPause was called, a `Service` was started using startForeground, or the system is using a feature that the user is aware (live wallpaper, input method service, etc.)
  This priority remains very high as the user could experience a negative feeling if notice that the process is killed.
- **Background state**  
  An action is being executed but the user isn't aware of. As a network call running, or a calculation being made. For example an `Activity` that isn't longer visible to the user.
  Android will kill these processes as they are meanless to the user.
- **Empty state**  
  To fall in this state, It means the current process have any component tied to its execution. Otherway said, Alls `Activities`, `Services`, `BroadcastReceiver` are destroyed.
  The system will kill these processes as quick as it needs some resources.

If the system decide that need some resources that our app is using. our app will flaweless be killed and the resource will be freed. But the system will allow us to save the state of our UI into a Bundle using `onSaveInstanceState`

## What's onSaveInstanceState actually does?

```kotlin
override fun onSaveInstanceState(outState: Bundle)
```
We can deduce from it signature that it is called when the instance of the state is getting saved. We are genius! :clap: :awesome:
But deeply seeing under the cover, we can see there is a lot of stuff done here.
This will persist the state of our instance and include all the relative information to reinstantiate it if need it. 
This will dispatch a save of the full tree of view hierachy.
Each `android.widget(?).View` will call its own `onSaveInstanceState` at its time. Some of them support this by default as we can constate on `EditText` when a configuration changes is applied, the current set text is preserve, or `CheckBox` that recover its checked state when restored. 

Save the view hierarchy recursively
- fragment arguments
- activity intent
State is unique for each UI components. This means that the bundle is unique and unnaccesible from out of its owner.
Stable behaviour since API 28 (Pie)

Since Pie: After onStop()  
Previous Pie: Before onStop() no guarantees about it will occur before or after onPause().

# Basic implementation



## Fragment 
## Activity
### Dagger
#### Koin ?

# Size limitations 
## Anti patterns
# TODO Restoration order

activity stack
fragment stack

# ViewModel Saved instance state library
## Dagger
## AsistedInject