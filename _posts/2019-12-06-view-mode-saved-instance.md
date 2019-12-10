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

We wil visit the following content :
- blalabala
- blalabala blalabala blalabala
- blalabala blalabala 

Nothing left to say, let's jump into it.

<!-- more -->

# Lifecycle basics concepts

Every Android developer know (or should know) the activity/fragments' (henceforth the View) lifecycles. It's not necessary to have the whole lifecycle in memory but at least recognize the point of interactions between our UI and the users. 
The full lifecycle diagram can be consulted [here](https://github.com/xxv/android-lifecycle) and to have a better understanding of the window management interactions [here](https://github.com/JoseAlcerreca/android-lifecycles) is also an very usefull resource.

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

These changes are requested by the system and your the view hierarchy will be, by default, recreated and will provide us a way to save the state and later passing it through again when our View get recreated. Here we have the oportunity to save a current search or some displayed data. Some of the data the user put into our view gets automatically saved as this is managed by system Views, but others require to be explicitly managed.  

onCreate => onDestroy

Anyway as the instance in memory of our view still the same, a common practice is just using a property member of the view class. As this works very well upon the user has view in front of him/her, for the most simplest usages this is sometimes enough. 
But we have to have in mind that 
We are going to talk about how in the later sections.
We have to 

### ViewModel + LiveData

To avoid managing this, jetpack offers us ViewModel + LiveData(TODO ref here). The first one will persist to configuration changes of our View as remain in memory and the state of it isn't affected by the destruction of the View. LiveData by its side offers us a way to observe the changes of a data model, if a configuration change occurs the last value offered is avalaible to the View. also as the observation is tied to the View's lifecycle we can be confident in posting new values even if the View is not ready to consume it.

## Process lifecycle

- Foreground activity.
- Visible activity 
- Background activity 
- Empty Process

State tied to user interaction 
The system will kill less important processes (the last ones)
Use service to prioritise (more than non visible apps)
Empty process : no component tied to the execution.


If the system decide that need some resources that our app is using. our apps will flaweless be killed and the resource will be freed.
//TODO Doc reference here

## What's onSaveInstanceState actually does?

```kotlin
override fun onSaveInstanceState(outState: Bundle)
```
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