# AuthGuardService Documentation

[Linked Table of Contents](#table-of-contents)

## Table of Contents

* [1. Overview](#1-overview)
* [2. Class `AuthGuardService`](#2-class-authguardservice)
    * [2.1 Constructor](#21-constructor)
    * [2.2 `canActivate` Method](#22-canactivate-method)
    * [2.3 `canLoad` Method](#23-canload-method)


<a name="1-overview"></a>
## 1. Overview

This document details the `AuthGuardService` in Angular, responsible for restricting access to routes based on user authentication status.  The service utilizes the `AuthenticationService` to check for the current user and redirects unauthenticated users to the login page.


<a name="2-class-authguardservice"></a>
## 2. Class `AuthGuardService`

This class implements route guarding functionality in an Angular application. It leverages Angular's router capabilities to control access to application routes.

<a name="21-constructor"></a>
### 2.1 Constructor

The constructor initializes the `AuthGuardService` by injecting the necessary dependencies:

| Parameter           | Type                       | Description                                                                     |
|--------------------|----------------------------|---------------------------------------------------------------------------------|
| `router`            | `Router`                    | Angular's routing service, used for navigation.                               |
| `authenticationService` | `AuthenticationService` | Service providing access to user authentication status (currentUserValue). |


<a name="22-canactivate-method"></a>
### 2.2 `canActivate` Method

The `canActivate` method is called by Angular's router to determine if a route can be activated.  Its logic is as follows:

1. **Retrieve Current User:** It fetches the currently logged-in user from `this.authenticationService.currentUserValue`.

2. **Check Authentication Status:** It checks if `currentUser` exists.  If it does, the user is authenticated, and the method returns `true`, allowing route activation.

3. **Redirect to Login:** If `currentUser` is null (user is not authenticated), the method redirects the user to the '/login' route using `this.router.navigate(['/login'])` and returns `false`, preventing route activation.

**Algorithm:**

The algorithm is a simple conditional check:

```
IF currentUser EXISTS THEN
  RETURN true
ELSE
  Redirect to '/login'
  RETURN false
ENDIF
```


<a name="23-canload-method"></a>
### 2.3 `canLoad` Method

Similar to `canActivate`, the `canLoad` method determines if a route can be loaded.  It performs the same authentication check:

1. **Retrieve Current User:** It retrieves the currently logged-in user from `this.authenticationService.currentUserValue`.

2. **Check Authentication Status:** It checks if `currentUser` exists. If it does, the user is authenticated, and the method returns `true`, allowing route loading.

3. **Redirect to Login:** If `currentUser` is null (user is not authenticated), the method redirects the user to the '/login' route using `this.router.navigate(['/login'])` and returns `false`, preventing route loading.

**Algorithm:**

The algorithm is identical to that of `canActivate`:

```
IF currentUser EXISTS THEN
  RETURN true
ELSE
  Redirect to '/login'
  RETURN false
ENDIF
```

The key difference between `canActivate` and `canLoad` lies in *when* they are executed.  `canActivate` guards against activating a route *after* it's loaded, whereas `canLoad` prevents the route from loading at all.  This is particularly relevant for lazy-loaded modules.
