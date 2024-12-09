# Aqua Viewer Application: AppComponent Documentation

[Linked Table of Contents](#linked-table-of-contents)

## Linked Table of Contents

* [1. Overview](#1-overview)
* [2. Component Structure](#2-component-structure)
  * [2.1. Properties](#21-properties)
  * [2.2. Methods](#22-methods)
* [3. Data Structures](#3-data-structures)
* [4. Algorithm Details](#4-algorithm-details)


## 1. Overview

This document details the `AppComponent` of the Aqua Viewer application.  `AppComponent` serves as the main application component, handling user authentication, data loading, and routing based on the user's logged-in status and platform.  It manages the display of menus specific to different game platforms (OnGeKi, Amazon, Diva).

## 2. Component Structure

### 2.1. Properties

| Property Name          | Type                  | Description                                                                        |
|-----------------------|-----------------------|------------------------------------------------------------------------------------|
| `title`                | `string`              | Application title.                                                                  |
| `user`                 | `User`                 | Current logged-in user object (from `AuthenticationService`).                       |
| `loading`              | `boolean`             | Flag indicating loading status; controlled by `ApiService`.                       |
| `ongekiMenu`           | `Menu[]`               | Array of menu items for OnGeKi platform.                                           |
| `amazonMenus`          | `Menu[]`               | Array of menu items for Amazon platform.                                          |
| `divaMenus`            | `Menu[]`               | Array of menu items for Diva platform.                                            |
| `mobileQuery`          | `MediaQueryList`       | MediaQueryList object to detect mobile screen size.                               |
| `dark`                 | `string`              | String representing the dark theme. (Currently unused, may be placeholder). |
| `subscription`         | `Subscription`         | RxJS Subscription for managing the loading state from `ApiService`.               |
| `_mobileQueryListener` | `() => void`          | Callback function for media query change detection.                               |


### 2.2. Methods

| Method Name       | Description                                                                                                 | Algorithm Details                                                                                                                                                  |
|--------------------|-------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ngOnInit()`      | Initializes the component. Loads preload data if a user is logged in. Subscribes to the `ApiService` loading state. | 1. Checks if a user is logged in (`this.user !== null`). 2. If logged in, calls `preLoad.load()` to initiate data preloading. 3. Subscribes to `api.loadingState` using RxJS to update the `loading` property. |
| `ngOnChanges()`   | Updates the `user` property whenever changes are detected.                                                  | Directly updates `user` with the current user value from `authenticationService.currentUserValue`.                                                                              |
| `ngOnDestroy()`   | Removes the media query listener to prevent memory leaks.                                                     | Removes the listener added in the constructor using `this.mobileQuery.removeListener(this._mobileQueryListener);`.                                                      |
| `logout()`        | Logs out the current user and reloads the application.                                                      | 1. Calls `this.authenticationService.logout()` to perform the logout action. 2. Calls `location.reload(true)` to force a full page reload, ensuring the logout is effective across the application. |


## 3. Data Structures

The `Menu` class is a simple data structure used to represent menu items:

| Property Name | Type    | Description             |
|---------------|---------|--------------------------|
| `id`          | `number` | Unique identifier        |
| `name`        | `string` | Display name of the menu |
| `url`         | `string` | URL associated with menu |

## 4. Algorithm Details

The `ngOnInit()` method utilizes RxJS subscriptions for efficient handling of asynchronous loading state updates from the `ApiService`.  The `logout()` method employs a full page reload to guarantee complete session termination and UI refresh.  The use of `MediaQueryList` ensures responsive design adaptation based on screen size.  No complex algorithms are implemented within this component; its primary role is orchestration and data binding.
