### import 

```ts
import {CommonModule} from '@angular/common';
import {
  Compiler, 
  Component, 
  Injectable, 
  Injector, 
  NgModule, 
  NgModuleFactory, 
  ReflectiveInjector
} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
```


## SimpleExample
```ts
@Component({selector: 'hello-world', template: 'Hello World!'})
class HelloWorld {
}

@Component({
  selector: 'ng-component-outlet-simple-example',
  template: `<ng-container *ngComponentOutlet="HelloWorld"></ng-container>`
})
class NgTemplateOutletSimpleExample {
  // This field is necessary to expose HelloWorld to the template.
  HelloWorld = HelloWorld;
}
```

### CompleteExample

```ts
@Injectable()
class Greeter {
  suffix = '!';
}

@Component({
  selector: 'complete-component',
  template: `Complete: <ng-content></ng-content> <ng-content></ng-content>{{ greeter.suffix }}`
})
class CompleteComponent {
  constructor(public greeter: Greeter) {}
}

@Component({
  selector: 'ng-component-outlet-complete-example',
  template: `
    <ng-container *ngComponentOutlet="CompleteComponent; 
                                      injector: myInjector; 
                                      content: myContent"></ng-container>`
})
class NgTemplateOutletCompleteExample {
  // This field is necessary to expose CompleteComponent to the template.
  CompleteComponent = CompleteComponent;
  myInjector: Injector;

  myContent = [[document.createTextNode('Ahoj')], [document.createTextNode('Svet')]];

  constructor(injector: Injector) {
    this.myInjector = ReflectiveInjector.resolveAndCreate([Greeter], injector);
  }
}
```

### NgModuleFactoryExample
```ts
@Component({selector: 'other-module-component', template: `Other Module Component!`})
class OtherModuleComponent {
}

@Component({
  selector: 'ng-component-outlet-other-module-example',
  template: `
    <ng-container *ngComponentOutlet="OtherModuleComponent;
                                      ngModuleFactory: myModule;"></ng-container>`
})
class NgTemplateOutletOtherModuleExample {
  // This field is necessary to expose OtherModuleComponent to the template.
  OtherModuleComponent = OtherModuleComponent;
  myModule: NgModuleFactory<any>;

  constructor(compiler: Compiler) { this.myModule = compiler.compileModuleSync(OtherModule); }
}
```
