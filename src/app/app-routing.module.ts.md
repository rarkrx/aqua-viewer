# AppRoutingModule Documentation

[Linked Table of Contents](#linked-table-of-contents)

## Linked Table of Contents

* [1. Overview](#1-overview)
* [2. Route Configuration (`routes` Array)](#2-route-configuration-routes-array)
    * [2.1. Root Route](#21-root-route)
    * [2.2. Login Route](#22-login-route)
    * [2.3. Dashboard Route](#23-dashboard-route)
    * [2.4. Changelog Route](#24-changelog-route)
    * [2.5. Importer Route](#25-importer-route)
    * [2.6. Lazy-Loaded Routes (`diva`, `ongeki`, `amazon`)](#26-lazy-loaded-routes-diva-ongeki-amazon)
* [3. NgModule Configuration](#3-ngmodule-configuration)


## 1. Overview

This document details the `AppRoutingModule` in the application.  This module configures the routing for the Angular application, defining the paths and components associated with each route.  It leverages Angular's `RouterModule` to manage navigation.  The module also utilizes lazy loading for certain modules to improve initial load times.


## 2. Route Configuration (`routes` Array)

The `routes` array defines the application's routing rules. Each object in the array represents a route, specifying a path and the component or module to be loaded when that path is navigated to.

| Path          | Component/Module             | `canActivate`     | `canLoad`        | Description                                                                 |
|---------------|------------------------------|--------------------|--------------------|-----------------------------------------------------------------------------|
| `''`           | `/login`                     |                    |                    | Redirects the root path (`/`) to the login page.                           |
| `/login`       | `LoginComponent`             |                    |                    | Displays the login component.                                                |
| `/dashboard`   | `DashboardComponent`         | `AuthGuardService` |                    | Displays the dashboard; access is protected by `AuthGuardService`.          |
| `/changelog`   | `ChangelogComponent`         |                    |                    | Displays the changelog component.                                             |
| `/import`      | `ImporterComponent`          |                    |                    | Displays the importer component.                                             |
| `/diva`        | `DivaModule`                 |                    | `AuthGuardService` | Lazy-loads the `DivaModule`; access is protected by `AuthGuardService`.       |
| `/ongeki`      | `OngekiModule`               |                    | `AuthGuardService` | Lazy-loads the `OngekiModule`; access is protected by `AuthGuardService`.     |
| `/amazon`      | `AmazonModule`               |                    | `AuthGuardService` | Lazy-loads the `AmazonModule`; access is protected by `AuthGuardService`.     |


### 2.1. Root Route

The route with an empty path (`''`) redirects to the `/login` route. This ensures that users are always directed to the login page upon initial access to the application.  `pathMatch: 'full'` ensures that only exact matches to the empty path trigger this redirection.


### 2.2. Login Route

The `/login` route displays the `LoginComponent`, responsible for user authentication.


### 2.3. Dashboard Route

The `/dashboard` route displays the `DashboardComponent`, providing users with an overview of application data.  The `canActivate` guard, `AuthGuardService`, ensures that only authenticated users can access this route.


### 2.4. Changelog Route

The `/changelog` route displays the `ChangelogComponent`, showing application updates and changes.


### 2.5. Importer Route

The `/import` route displays the `ImporterComponent`, used for importing data into the application.


### 2.6. Lazy-Loaded Routes (`diva`, `ongeki`, `amazon`)

The routes for `/diva`, `/ongeki`, and `/amazon` utilize lazy loading.  This means that these modules are only loaded when the user navigates to the corresponding route. This improves application load time by deferring the loading of these potentially large modules until they are needed.  The `canLoad` guard, `AuthGuardService`, protects these routes, requiring authentication before loading.  The `loadChildren` property uses a function that dynamically imports the module using ES6 module syntax.  This function returns a Promise that resolves to the module.



## 3. NgModule Configuration

The `AppRoutingModule` is decorated with `@NgModule`, specifying the `imports` and `exports` of the module.

* **`imports: [RouterModule.forRoot(routes, {preloadingStrategy: PreloadAllModules})]`**: This imports the `RouterModule` and configures it using the `forRoot` method.  The `routes` array defines the application's routing configuration.  `PreloadAllModules` is used as the preloading strategy, meaning all lazy-loaded modules are preloaded in the background to improve performance.

* **`exports: [RouterModule]`**:  This exports the `RouterModule` to make it available to other modules within the application.  This allows other modules to utilize the routing functionality.
