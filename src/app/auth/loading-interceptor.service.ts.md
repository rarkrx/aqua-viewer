# LoadingInterceptorService Documentation

[Linked Table of Contents](#linked-table-of-contents)

## Linked Table of Contents

* [1. Overview](#1-overview)
* [2. Class Structure: `LoadingInterceptorService`](#2-class-structure-loadinginterceptorservice)
    * [2.1 Constructor](#21-constructor)
    * [2.2 `intercept` Method](#22-intercept-method)
    * [2.3 `onEnd` Method](#23-onend-method)
    * [2.4 `showLoader` Method](#24-showloader-method)
    * [2.5 `hideLoader` Method](#25-hideloader-method)
* [3. Algorithm Explanation](#3-algorithm-explanation)


## 1. Overview

This document details the `LoadingInterceptorService` class, an Angular HTTP interceptor responsible for managing a loading indicator during API calls.  The service uses RxJS operators to handle both successful and failed HTTP requests.  It interacts with an `ApiService` (assumed to provide loading indicator functionality).

## 2. Class Structure: `LoadingInterceptorService`

The `LoadingInterceptorService` class is an Angular service (`@Injectable({ providedIn: 'root' })`) implementing the `HttpInterceptor` interface.  This allows it to intercept all HTTP requests and responses.

### 2.1 Constructor

```typescript
constructor(private api: ApiService) { }
```

The constructor injects an instance of `ApiService` which is used to display and hide the application's loading indicator.

### 2.2 `intercept` Method

```typescript
intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
  this.showLoader();
  return next.handle(req).pipe(tap((event: HttpEvent<any>) => {
      if (event instanceof HttpResponse) {
        this.onEnd();
      }
    },
    (err: any) => {
      this.onEnd();
    }));
}
```

The `intercept` method is the core of the interceptor. It performs the following actions:

1. **Displays the loader:** Calls `this.showLoader()` to display a loading indicator before the HTTP request is made.
2. **Handles the request:** Uses `next.handle(req)` to pass the request to the next interceptor in the chain (or the backend if this is the last interceptor). The result is an `Observable<HttpEvent<any>>`.
3. **Observes the response:** Uses the RxJS `tap` operator to observe both successful responses (`HttpResponse`) and errors.  `tap` allows performing side effects without altering the original observable stream.
4. **Hides the loader:**  Calls `this.onEnd()` when either a successful response or an error occurs. This ensures the loader is hidden regardless of the request outcome.


### 2.3 `onEnd` Method

```typescript
private onEnd(): void {
  this.hideLoader();
}
```

A helper method to hide the loading indicator using the injected `ApiService`.  This simplifies the `intercept` method's logic.

### 2.4 `showLoader` Method

```typescript
private showLoader(): void {
  this.api.show();
}
```

This method simply calls the `show()` method on the injected `ApiService` to display the loading indicator.  The implementation details of `ApiService.show()` are outside the scope of this documentation.

### 2.5 `hideLoader` Method

```typescript
private hideLoader(): void {
  this.api.hide();
}
```

This method calls the `hide()` method on the injected `ApiService` to hide the loading indicator.  The implementation details of `ApiService.hide()` are outside the scope of this documentation.


## 3. Algorithm Explanation

The core algorithm employed by `LoadingInterceptorService` is straightforward:

1. **Initialization:** Upon receiving an HTTP request, the service displays a loading indicator.
2. **Request Handling:** The request is passed down the interceptor chain for processing.
3. **Response Observation:** The service uses RxJS's `tap` operator to observe both successful responses and errors from the observable returned by `next.handle(req)`.
4. **Completion:** Regardless of success or failure, the service hides the loading indicator once the HTTP request completes.  This ensures the indicator is always removed, providing consistent user feedback.

The use of `tap` is crucial because it allows for side effects (showing and hiding the loader) without modifying the observable's emitted values.  This keeps the observable stream clean and avoids unexpected behavior.  The error handling is also integrated seamlessly within the `tap` operator, improving code readability and maintainability.
