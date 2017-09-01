```ts
import {Component, createPlatformFactory} from '@angular/core';
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';

// #docregion longform
@Component({selector: 'my-app', template: 'Hello World'})
class MyApp {
}

const myPlatformFactory = createPlatformFactory(platformBrowserDynamic, 'myPlatform');
myPlatformFactory().bootstrapModule(MyApp);
```