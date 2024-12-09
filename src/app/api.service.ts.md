# ApiService Documentation

[Linked Table of Contents](#linked-table-of-contents)

## Linked Table of Contents

* [1. Introduction](#1-introduction)
* [2. Class: `ApiService`](#2-class-apiservice)
    * [2.1 Constructor](#21-constructor)
    * [2.2 Methods](#22-methods)
        * [2.2.1 `get(path: string, params?: HttpParams)`](#221-getpath-string-params-httpparams)
        * [2.2.2 `post(path: string, data?: object, params?: HttpParams)`](#222-postpath-string-data-object-params-httpparams)
        * [2.2.3 `put(path: string, data?: object, params?: HttpParams)`](#223-putpath-string-data-object-params-httpparams)
        * [2.2.4 `delete(path: string, params?: HttpParams)`](#224-deletepath-string-params-httpparams)
        * [2.2.5 `getHost(): string`](#225-gethost-string)
        * [2.2.6 `show()`](#226-show)
        * [2.2.7 `hide()`](#227-hide)
* [3. Class: `Resp`](#3-class-resp)
* [4. Interface: `LoadingState`](#4-interface-loadingstate)


## 1. Introduction

This document provides internal code documentation for the `ApiService` class,  `Resp` class, and `LoadingState` interface within an Angular application.  `ApiService` handles HTTP requests,  `Resp` defines a standard response structure, and `LoadingState` manages loading indicator state.


## 2. Class: `ApiService`

This service handles all HTTP requests to the backend API. It uses Angular's `HttpClient` and manages loading indicators.

### 2.1 Constructor

The constructor injects the `HttpClient` and `AuthenticationService` dependencies.

```typescript
constructor(private http: HttpClient,
            private authenticationService: AuthenticationService) {
}
```

*   `http`: An instance of Angular's `HttpClient` used to make HTTP requests.
*   `authenticationService`: An instance of `AuthenticationService` used to determine the API server base URL based on authentication status.


### 2.2 Methods

#### 2.2.1 `get(path: string, params?: HttpParams)`

Performs a GET request to the specified API path.

```typescript
get(path: string, params?: HttpParams) {
  return this.http.get<any>(this.getHost() + path, {params});
}
```

*   `path`: The API endpoint path (e.g., '/users').
*   `params`: Optional `HttpParams` object for adding query parameters to the request.
*   Returns an observable of type `any`. The response type isn't explicitly defined here and depends on the API endpoint.


#### 2.2.2 `post(path: string, data?: object, params?: HttpParams)`

Performs a POST request to the specified API path.

```typescript
post(path: string, data?: object, params?: HttpParams) {
  return this.http.post<any>(this.getHost() + path, data, {params});
}
```

*   `path`: The API endpoint path.
*   `data`: The request body data (optional).
*   `params`: Optional `HttpParams` object for query parameters.
*   Returns an observable of type `any`.  The response type isn't explicitly defined and depends on the API endpoint.


#### 2.2.3 `put(path: string, data?: object, params?: HttpParams)`

Performs a PUT request to the specified API path.

```typescript
put(path: string, data?: object, params?: HttpParams) {
  return this.http.put<any>(this.getHost() + path, data, {params});
}
```

*   `path`: The API endpoint path.
*   `data`: The request body data (optional).
*   `params`: Optional `HttpParams` object for query parameters.
*   Returns an observable of type `any`. The response type isn't explicitly defined and depends on the API endpoint.


#### 2.2.4 `delete(path: string, params?: HttpParams)`

Performs a DELETE request to the specified API path.

```typescript
delete(path: string, params?: HttpParams) {
  return this.http.delete<any>(this.getHost() + path, {params});
}
```

*   `path`: The API endpoint path.
*   `params`: Optional `HttpParams` object for query parameters.
*   Returns an observable of type `any`. The response type isn't explicitly defined and depends on the API endpoint.


#### 2.2.5 `getHost(): string`

Determines the base URL for API requests.  It prioritizes the API server URL from the authenticated user's information; otherwise, it defaults to `http://localhost:80`.

```typescript
getHost(): string {
  if (this.authenticationService.currentUserValue) {
    return this.authenticationService.currentUserValue.apiServer + '/';
  }
  return 'http://localhost:80' + '/';
}
```

This function uses a simple conditional to determine the appropriate base URL based on the authentication status. If a user is logged in (`this.authenticationService.currentUserValue` is truthy), it retrieves the `apiServer` property from the `currentUserValue` object, which is assumed to contain the user's API server address.  Otherwise, it defaults to `http://localhost:80`.


#### 2.2.6 `show()`

Displays the loading indicator.

```typescript
show() {
  this.loadingSubject.next({show: true});
}
```

This method updates the loading state subject, signaling that the loading indicator should be shown.  It does this by emitting a value to the `loadingSubject`.


#### 2.2.7 `hide()`

Hides the loading indicator.

```typescript
hide() {
  this.loadingSubject.next({show: false});
}
```

This method updates the loading state subject to hide the loading indicator by emitting a value to the `loadingSubject`.


## 3. Class: `Resp`

This class defines a standard structure for API responses.

```typescript
export class Resp {
  status: string;
  data: object;
}
```

*   `status`: A string representing the status of the API response (e.g., "success", "error").
*   `data`: An object containing the response data.


## 4. Interface: `LoadingState`

This interface defines the structure for the loading state object.

```typescript
export interface LoadingState {
  show: boolean;
}
```

*   `show`: A boolean indicating whether the loading indicator should be displayed.
