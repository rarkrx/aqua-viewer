# Error Interceptor Service Documentation

[Linked Table of Contents](#linked-table-of-contents)


## Linked Table of Contents

* [1. Overview](#1-overview)
* [2. Class Structure: `ErrorInterceptorService`](#2-class-structure-errorinterceptorservice)
    * [2.1 Constructor](#21-constructor)
    * [2.2 `intercept` Method](#22-intercept-method)
* [3. Algorithm Details: `intercept` Method](#3-algorithm-details-intercept-method)


## 1. Overview

The `ErrorInterceptorService` is an Angular HTTP interceptor responsible for handling and transforming HTTP errors. It intercepts outgoing HTTP requests, catches any errors that occur during the request, and transforms the error into a more user-friendly format before re-throwing it.  This allows for centralized error handling within the application.


## 2. Class Structure: `ErrorInterceptorService`

The service is defined using Angular's `@Injectable` decorator, making it injectable into other components.  It implements the `HttpInterceptor` interface, requiring the implementation of the `intercept` method.

### 2.1 Constructor

The constructor is empty, as no dependencies are required for this interceptor.

```typescript
constructor() {
}
```

### 2.2 `intercept` Method

This method intercepts HTTP requests and handles potential errors.

```typescript
intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(request).pipe(catchError(err => {
      console.log(err);
      const error = err.error ? err.error.message : err.status + err.statusText;
      return throwError(error);
    }));
  }
```

The `intercept` method takes two parameters:

| Parameter | Type                     | Description                                                                 |
| --------- | ------------------------ | --------------------------------------------------------------------------- |
| `request` | `HttpRequest<any>`       | The outgoing HTTP request.                                                  |
| `next`    | `HttpHandler`            | A handler to delegate the request to the next interceptor or backend. |


It uses RxJS's `catchError` operator to handle any errors that occur during the request.


## 3. Algorithm Details: `intercept` Method

The `intercept` method's core logic functions as follows:

1. **Request Delegation:** The interceptor first uses `next.handle(request)` to pass the request to the next interceptor in the chain or ultimately to the backend. This allows other interceptors to perform their operations before error handling.

2. **Error Catching:** The `catchError` operator intercepts any errors emitted by the `next.handle(request)` observable.

3. **Error Transformation:** The error object (`err`) is then examined.  The code prioritizes extracting a user-friendly error message:
    * **Priority 1:  Structured Error:** If the error object has an `err.error` property, which contains a `message` property (common in many REST APIs), that message is used as the error.  This assumes that the API returns structured JSON error responses.
    * **Priority 2: HTTP Status:** Otherwise, the HTTP status code (`err.status`) and status text (`err.statusText`) are concatenated to create a more informative error message.

4. **Error Re-throwing:** Finally,  `throwError(error)` re-throws the processed error, allowing other error handling mechanisms in the application (e.g., error handling in components) to catch and display it to the user.  The error is now a simpler string rather than a complex HTTP error object.

The logging of the raw error (`console.log(err)`) is included for debugging purposes.  This provides developers with a complete error object for troubleshooting.
