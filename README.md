# ng2-metadata [![npm version](https://badge.fury.io/js/ng2-metadata.svg)](http://badge.fury.io/js/ng2-metadata) [![npm downloads](https://img.shields.io/npm/dm/ng2-metadata.svg)](https://npmjs.org/ng2-metadata)

[![Linux build](https://travis-ci.org/fulls1z3/ng2-metadata.svg?branch=master)](https://travis-ci.org/fulls1z3/ng2-metadata) [![Windows build](https://ci.appveyor.com/api/projects/status/github/fulls1z3/ng2-metadata?branch=master&svg=true)](https://ci.appveyor.com/project/fulls1z3/ng2-metadata) [![coverage](https://codecov.io/github/fulls1z3/ng2-metadata/coverage.svg?branch=master)](https://codecov.io/gh/fulls1z3/ng2-metadata) [![peerDependency Status](https://david-dm.org/fulls1z3/ng2-metadata/peer-status.svg)](https://david-dm.org/fulls1z3/ng2-metadata#info=peerDependencies) [![devDependency Status](https://david-dm.org/fulls1z3/ng2-metadata/dev-status.svg)](https://david-dm.org/fulls1z3/ng2-metadata#info=devDependencies)

This repository holds the TypeScript source code and distributable bundle of **`ng2-metadata`**, the dynamic page title &amp; meta tags generator for Angular2.

## Prerequisites
Verify that you are running at least `@angular v2.1.0` and `@angular/router v3.1.0`. Older versions are not tested, might produce errors.

## Getting started
### Installation
You can install **`ng2-metadata`** using `npm`
```
npm install ng2-metadata --save
```

### Adding ng2-metadata to your project (SystemJS)
Add `map` for **`ng2-metadata`** in your `systemjs.config`
```javascript
'ng2-metadata': 'node_modules/ng2-metadata/bundles/ng2-metadata.umd.min.js'
```

### Route configuration
Add metadata inside the `data` property of routes.

**Note:** meta properties such as `title`, `description`, `author` and `publisher` are duplicated as `og:title`, `og:description`, `og:author` and `og:publisher`, so there's no need to declare them again in this context.

```TypeScript
export const routes = [
  {
    path: 'home',
    component: HomeComponent,
    data: {
      metadata: {
        title: 'Sweet home',
        description: 'Home, home sweet home... and what?'
      }
    }
  },
  {
    path: 'duck',
    component: DuckComponent,
    data: {
      metadata: {
        title: 'Rubber duckie',
        description: 'Have you seen my rubber duckie?'
      }
    }
  },
  {
    path: 'toothpaste',
    component: ToothpasteComponent,
    data: {
      metadata: {
        title: 'Toothpaste',
        override: true, // prevents appending/prepending the application name to the title attribute
        description: 'Eating toothpaste is considered to be too healthy!'
      }
    }
  }
  ...
];
```

### app.module configuration
Import **MetadataModule** using the mapping `'ng2-metadata'` and append `MetadataModule.forRoot()` within the imports property of app.module (*considering the app.module is the core module in the angular application*).

```TypeScript
...
import { MetadataModule } from 'ng2-metadata';
...

@NgModule({
  declarations: [
    AppComponent,
    ...
  ],
  imports: [
    ...
    RouterModule.forRoot(routes),
    MetadataModule.forRoot()
  ],
  bootstrap: [AppComponent]
})
```

### app.component configuration
Import **MetadataService** using the mapping `'ng2-metadata'` and inject it in the constructor of app.component (*considering the app.component is the bootstrap component in the angular application*).

```TypeScript
...
import { MetadataService } from 'ng2-metadata';
...

@Component({
  ...
})
export class AppComponent {
  ...
  constructor(private metadataService: MetadataService) { }
  ...
}
```

Holy cow! **`ng2-metadata`** will update the page title & meta tags every time the route changes.

## Settings
You can import the **MetadataModule** using the `MetadataStaticLoader`. By default, it is configured to **`prepend page titles`** after the `application name` (if any set). These **default metadata settings** are used when a route doesn't contain any metadata in its `data` property.

You can customize this behavior (and ofc other settings) by configuring a custom `MetadataLoader`, and supplying respective values. The following example shows the use of an exported function (instead of an inline function) for [AoT compilation].

```TypeScript
...
import { MetadataModule, MetadataLoader, MetadataStaticLoader } from 'ng2-metadata';
...

export function createMetadataLoader() {
  return new MetadataStaticLoader({
    pageTitlePositioning: PageTitlePositioning.PrependPageTitle,
    pageTitleSeparator: ' - ',
    applicationName: 'Tour of (lazy/busy) heroes',
    defaults: {
      title: 'Mighty mighty mouse',
      description: 'Mighty Mouse is an animated superhero mouse character',
      'og:image': 'https://upload.wikimedia.org/wikipedia/commons/f/f8/superraton.jpg',
      'og:type': 'website',
      'og:locale': 'en_US',
      'og:locale:alternate': 'nl_NL,tr_TR'
    }
  });
}

...

@NgModule({
  declarations: [
    AppComponent,
    ...
  ],
  imports: [
    ...
    RouterModule.forRoot(routes),
    MetadataModule.forRoot({
      provide: MetadataLoader,
      useFactory: (createMetadataLoader)
    })
  ],
  bootstrap: [AppComponent]
})
```

### Set metadata programmatically
```TypeScript
...
import { Component, OnInit } from '@angular/core';
import { MetadataService } from 'ng2-metadata';
...

@Component({
  ...
})
export class ItemComponent implements OnInit {
  ...
  constructor(private metadataService: MetadataService) { }
  ...
  ngOnInit() {
    this.item = //HTTP GET for "item" in the repository
    this.metadataService.setTitle(`Page for ${this.item.name}`);
    this.metadataService.setTag('og:image', this.item.imageUrl);
  }
}

```

## Licence
The MIT License (MIT)

Copyright (c) 2016 [Burak Tasci](http://www.buraktasci.com)

[AoT compilation]: https://angular.io/docs/ts/latest/cookbook/aot-compiler.html