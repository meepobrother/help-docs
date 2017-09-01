```ts
import {Component, Directive, Input, ViewChild} from '@angular/core';

@Directive({selector: 'pane'})
export class Pane {
  @Input() id: string;
}

@Component({
  selector: 'example-app',
  template: `
    <pane id="1" *ngIf="shouldShow"></pane>
    <pane id="2" *ngIf="!shouldShow"></pane>
    
    <button (click)="toggle()">Toggle</button>
       
    <div>Selected: {{selectedPane}}</div> 
  `,
})
export class ViewChildComp {
  @ViewChild(Pane)
  set pane(v: Pane) {
    setTimeout(() => { this.selectedPane = v.id; }, 0);
  }
  selectedPane: string = '';
  shouldShow = true;
  toggle() { this.shouldShow = !this.shouldShow; }
}

```


```

ngOnInit

ngDoCheck

ngDoCheck

ngAfterContentChecked

ngAfterContentInit

ngAfterViewChecked

ngAfterViewInit

ngOnDestroy

ngOnChanges


```