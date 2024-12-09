# Internal Code Documentation: `dashboard.component.ts`

[Linked Table of Contents](#linked-table-of-contents)

## Linked Table of Contents

* [1. Overview](#1-overview)
* [2. Component Properties](#2-component-properties)
* [3. Constructor](#3-constructor)
* [4. `ngOnInit()` Method](#4-ngoninit-method)
* [5. `reload()` Method](#5-reload-method)


## 1. Overview

This document provides internal code documentation for `dashboard.component.ts`. This Angular component displays the loading status of various game data.  It utilizes the `PreloadService` to subscribe to the loading status of different data sets and the `NgxIndexedDBService` to manage the application's IndexedDB database.


## 2. Component Properties

The component maintains several properties to track the loading status of different data sets.  These properties are initialized to 'Initialize' and updated via subscriptions to observables provided by the `PreloadService`.

| Property Name        | Data Type | Description                                         |
|-----------------------|------------|-----------------------------------------------------|
| `divaPv`             | string     | Loading status of DIVA PV data.                   |
| `divaModule`         | string     | Loading status of DIVA Module data.                |
| `divaCustomize`      | string     | Loading status of DIVA Customize data.             |
| `chuniMusic`         | string     | Loading status of Chunithm Music data.             |
| `ongekiCard`         | string     | Loading status of Ongeki Card data.                |
| `ongekiCharacter`    | string     | Loading status of Ongeki Character data.           |
| `ongekiMusic`        | string     | Loading status of Ongeki Music data.               |
| `ongekiSkill`        | string     | Loading status of Ongeki Skill data.               |
| `chuniCharacter`     | string     | Loading status of Chunithm Character data.         |
| `chuniSkill`         | string     | Loading status of Chunithm Skill data.             |


## 3. Constructor

The constructor injects the necessary services:

* `dbService`: An instance of `NgxIndexedDBService` for database manipulation.
* `preload`: An instance of `PreloadService` for accessing data loading status.

```typescript
constructor(
  private dbService: NgxIndexedDBService,
  private preload: PreloadService
) {
}
```

## 4. `ngOnInit()` Method

The `ngOnInit()` lifecycle hook subscribes to observables from the `PreloadService` to update the component's status properties.  Each subscription updates a corresponding property whenever the observable emits a new value.  This ensures the dashboard reflects the current loading state of each data set.

```typescript
ngOnInit() {
  this.preload.divaPvState.subscribe(data => this.divaPv = data);
  this.preload.divaModuleState.subscribe(data => this.divaModule = data);
  this.preload.divaCustomizeState.subscribe(data => this.divaCustomize = data);
  this.preload.chuniMusicState.subscribe(data => this.chuniMusic = data);
  this.preload.ongekiCardState.subscribe(data => this.ongekiCard = data);
  this.preload.ongekiCharacterState.subscribe(data => this.ongekiCharacter = data);
  this.preload.ongekiMusicState.subscribe(data => this.ongekiMusic = data);
  this.preload.ongekiSkillState.subscribe(data => this.ongekiSkill = data);
  this.preload.chuniCharacterState.subscribe(data => this.chuniCharacter = data);
  this.preload.chuniSkillState.subscribe(data => this.chuniSkill = data);
}
```

## 5. `reload()` Method

The `reload()` method triggers a data reload. It first calls the `reload()` method on the `PreloadService` to initiate data fetching. Then it deletes the IndexedDB database using `dbService.deleteDatabase()` and finally reloads the current page using `window.location.reload()`. This ensures that any cached data is cleared and the application starts with a fresh database.

```typescript
reload() {
  this.preload.reload();
  this.dbService.deleteDatabase().then(
    () => window.location.reload()
  );
}
```
