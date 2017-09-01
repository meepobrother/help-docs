

### import 

```ts
import {Component, NgModule, OnInit, TemplateRef, ViewChild} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
import {Subject} from 'rxjs/Subject';
```

### NgTemplateOutlet

```ts
@Component({
  selector: 'ng-template-outlet-example',
  template: `
    <ng-container *ngTemplateOutlet="greet"></ng-container>
    <hr>
    <ng-container *ngTemplateOutlet="eng; context: myContext"></ng-container>
    <hr>
    <ng-container *ngTemplateOutlet="svk; context: myContext"></ng-container>
    <hr>
    
    <ng-template #greet><span>Hello</span></ng-template>
    <ng-template #eng let-name><span>Hello {{name}}!</span></ng-template>
    <ng-template #svk let-person="localSk"><span>Ahoj {{person}}!</span></ng-template>
`
})
class NgTemplateOutletExample {
  myContext = {$implicit: 'World', localSk: 'Svet'};
}
```