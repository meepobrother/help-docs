```ts

import {Component, ContentChild, Directive, Input} from '@angular/core';

@Directive({selector: 'pane'})
export class Pane {
  @Input() id: string;
}

@Component({
  selector: 'tab',
  template: `
    <div>pane: {{pane?.id}}</div> 
  `
})
export class Tab {
  @ContentChild(Pane) pane: Pane;
}

@Component({
  selector: 'example-app',
  template: `
    <tab>
      <pane id="1" *ngIf="shouldShow"></pane>
      <pane id="2" *ngIf="!shouldShow"></pane>
    </tab>
    
    <button (click)="toggle()">Toggle</button>
  `,
})
export class ContentChildComp {
  shouldShow = true;

  toggle() { this.shouldShow = !this.shouldShow; }
}

```


```ts
import {AfterContentInit, ContentChild, Directive} from '@angular/core';

@Directive({selector: 'child-directive'})
class ChildDirective {
}

@Directive({selector: 'someDir'})
class SomeDir implements AfterContentInit {
  @ContentChild(ChildDirective) contentChild: ChildDirective;

  ngAfterContentInit() {
    // contentChild is set
  }
}
```