# Authentication Service - Internal Documentation

[Linked Table of Contents](#table-of-contents)

## Table of Contents <a name="table-of-contents"></a>

* [1. Overview](#overview)
* [2. Class: `AuthenticationService`](#class-authenticationservice)
    * [2.1 Constructor](#constructor)
    * [2.2 `currentUser` and `currentUserSubject`](#currentuser-and-currentusersubject)
    * [2.3 `currentUserValue` Getter](#currentuservalue-getter)
    * [2.4 `login(accessCode: string, server: string)` Method](#loginaccesscode-string-server-string-method)
    * [2.5 `logout()` Method](#logout-method)
* [3. Class: `User`](#class-user)


## 1. Overview

This document details the implementation of the `AuthenticationService` and `User` classes, crucial components for user authentication within the application.  The service leverages RxJS Observables and Angular's `HttpClient` to manage user login and logout functionalities, persisting user data in the browser's local storage.


## 2. Class: `AuthenticationService`

This service manages user authentication, utilizing a BehaviorSubject to track the currently logged-in user.

### 2.1 Constructor

```typescript
constructor(private http: HttpClient) {
    this.currentUserSubject = new BehaviorSubject<User>(JSON.parse(localStorage.getItem('currentUser')));
    this.currentUser = this.currentUserSubject.asObservable();
}
```

The constructor initializes the `currentUserSubject` with the user data retrieved from local storage. If no user data is found in local storage, `JSON.parse` will return `null`, which will be the initial value of the BehaviorSubject.  `currentUser` is then set as an observable stream of the `currentUserSubject`. This allows components to subscribe to changes in the current user.


### 2.2 `currentUser` and `currentUserSubject`

```typescript
public currentUser: Observable<User>;
private currentUserSubject: BehaviorSubject<User>;
```

`currentUserSubject` is a BehaviorSubject that holds the currently logged-in `User` object.  It's a private member, ensuring controlled access. `currentUser` is a public Observable derived from `currentUserSubject`, allowing components to subscribe and react to changes in the logged-in user without directly accessing the BehaviorSubject.

### 2.3 `currentUserValue` Getter

```typescript
public get currentUserValue(): User {
    return this.currentUserSubject.value;
}
```

This getter provides a direct access to the current value of the `currentUserSubject`.  This is useful when a snapshot of the current user is needed, rather than subscribing to the observable stream.


### 2.4 `login(accessCode: string, server: string)` Method

```typescript
login(accessCode: string, server: string) {
    return this.http.post<any>(server + '/' + 'api/sega/aime/getByAccessCode', {accessCode})
      .pipe(
        map(
          resp => {
            if (resp && resp.extId) {
              const user = new User(resp.extId, server);
              localStorage.setItem('currentUser', JSON.stringify(user));
              this.currentUserSubject.next(user);
              return user;
            }
          }
        )
      );
}
```

This method handles user login.  It performs the following steps:

1. **HTTP POST Request:** Sends a POST request to the specified server endpoint (`/api/sega/aime/getByAccessCode`) with the provided `accessCode`.
2. **Response Mapping:** Uses the `map` operator to process the HTTP response.  If the response (`resp`) is valid and contains an `extId` property, a new `User` object is created.
3. **Local Storage Update:** The newly created `User` object is stored in local storage under the key 'currentUser'.
4. **BehaviorSubject Update:** The `currentUserSubject` is updated with the new `User` object, notifying subscribers of the login event.
5. **Return Value:** The function returns an Observable that emits the newly created `User` object upon successful login or null otherwise.


### 2.5 `logout()` Method

```typescript
logout() {
    localStorage.removeItem('currentUser');
    this.currentUserSubject.next(null);
}
```

This method logs the user out by removing the user data from local storage and setting the `currentUserSubject` to `null`, effectively signaling to any subscribers that the user is no longer logged in.


## 3. Class: `User`

```typescript
export class User {
  extId: number;
  apiServer: string;

  constructor(extId: number, apiServer: string) {
    this.extId = extId;
    this.apiServer = apiServer;
  }
}
```

A simple class representing a logged-in user.  It stores the user's external ID (`extId`) obtained from the server and the API server address (`apiServer`).  These attributes are used to identify and interact with the user within the application.

