## hash

```ts
import {HashLocationStrategy, Location, LocationStrategy} from '@angular/common';
import {Component} from '@angular/core';

@Component({
  selector: 'hash-location',
  providers: [Location, {provide: LocationStrategy, useClass: HashLocationStrategy}],
  template: `
    <h1>HashLocationStrategy</h1>
    Current URL is: <code>{{location.path()}}</code><br>
    Normalize: <code>/foo/bar/</code> is: <code>{{location.normalize('foo/bar')}}</code><br>
  `
})
export class HashLocationComponent {
  location: Location;
  constructor(location: Location) { this.location = location; }
}
```

### path

```ts
import {Location, LocationStrategy, PathLocationStrategy} from '@angular/common';
import {Component} from '@angular/core';

@Component({
  selector: 'path-location',
  providers: [Location, {provide: LocationStrategy, useClass: PathLocationStrategy}],
  template: `
    <h1>PathLocationStrategy</h1>
    Current URL is: <code>{{location.path()}}</code><br>
    Normalize: <code>/foo/bar/</code> is: <code>{{location.normalize('foo/bar')}}</code><br>
  `
})
export class PathLocationComponent {
  location: Location;
  constructor(location: Location) { this.location = location; }
}
```