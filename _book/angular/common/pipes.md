### import 

```ts
import {Component} from '@angular/core';
import {Observable} from 'rxjs/Observable';
import {Subscriber} from 'rxjs/Subscriber';
```


### AsyncPipePromise
```ts
@Component({
  selector: 'async-promise-pipe',
  template: `<div>
    <code>promise|async</code>: 
    <button (click)="clicked()">{{ arrived ? 'Reset' : 'Resolve' }}</button>
    <span>Wait for it... {{ greeting | async }}</span>
  </div>`
})
export class AsyncPromisePipeComponent {
  greeting: Promise<string>|null = null;
  arrived: boolean = false;

  private resolve: Function|null = null;

  constructor() { this.reset(); }

  reset() {
    this.arrived = false;
    this.greeting = new Promise<string>((resolve, reject) => { this.resolve = resolve; });
  }

  clicked() {
    if (this.arrived) {
      this.reset();
    } else {
      this.resolve !('hi there!');
      this.arrived = true;
    }
  }
}
```


### AsyncPipeObservable
```ts
@Component({
  selector: 'async-observable-pipe',
  template: '<div><code>observable|async</code>: Time: {{ time | async }}</div>'
})
export class AsyncObservablePipeComponent {
  time = new Observable<string>((observer: Subscriber<string>) => {
    setInterval(() => observer.next(new Date().toString()), 1000);
  });
}
```

### common

```ts

function setInterval(fn: Function, delay: number) {
  const zone = Zone.current;
  let rootZone = zone;
  while (rootZone.parent) {
    rootZone = rootZone.parent;
  }
  rootZone.run(
      () => { window.setInterval(function() { zone.run(fn, this, arguments as any); }, delay); });
}


```


### DatePipe
```ts
@Component({
  selector: 'date-pipe',
  template: `<div>
    <p>Today is {{today | date}}</p>
    <p>Or if you prefer, {{today | date:'fullDate'}}</p>
    <p>The time is {{today | date:'jmZ'}}</p>
  </div>`
})
export class DatePipeComponent {
  today: number = Date.now();
}

```


### I18nPluralPipeComponent

```ts
@Component({
  selector: 'i18n-plural-pipe',
  template: `<div>{{ messages.length | i18nPlural: messageMapping }}</div>`
})
export class I18nPluralPipeComponent {
  messages: any[] = ['Message 1'];
  messageMapping:
      {[k: string]: string} = {'=0': 'No messages.', '=1': 'One message.', 'other': '# messages.'};
}
```

### I18nSelectPipeComponent
```ts
@Component(
    {selector: 'i18n-select-pipe', template: `<div>{{gender | i18nSelect: inviteMap}} </div>`})
export class I18nSelectPipeComponent {
  gender: string = 'male';
  inviteMap: any = {'male': 'Invite him.', 'female': 'Invite her.', 'other': 'Invite them.'};
}
```


### JsonPipe
```ts
@Component({
  selector: 'json-pipe',
  template: `<div>
    <p>Without JSON pipe:</p>
    <pre>{{object}}</pre>
    <p>With JSON pipe:</p>
    <pre>{{object | json}}</pre>
  </div>`
})
export class JsonPipeComponent {
  object: Object = {foo: 'bar', baz: 'qux', nested: {xyz: 3, numbers: [1, 2, 3, 4, 5]}};
}
```

### LowerUpperPipe
```ts
@Component({
  selector: 'lowerupper-pipe',
  template: `<div>
    <label>Name: </label><input #name (keyup)="change(name.value)" type="text">
    <p>In lowercase: <pre>'{{value | lowercase}}'</pre>
    <p>In uppercase: <pre>'{{value | uppercase}}'</pre>
  </div>`
})
export class LowerUpperPipeComponent {
  value: string;
  change(value: string) { this.value = value; }
}
```

### NumberPipe
```ts

@Component({
  selector: 'number-pipe',
  template: `<div>
    <p>e (no formatting): {{e}}</p>
    <p>e (3.1-5): {{e | number:'3.1-5'}}</p>
    <p>pi (no formatting): {{pi}}</p>
    <p>pi (3.5-5): {{pi | number:'3.5-5'}}</p>
  </div>`
})
export class NumberPipeComponent {
  pi: number = 3.141592;
  e: number = 2.718281828459045;
}
// #enddocregion

// #docregion PercentPipe
@Component({
  selector: 'percent-pipe',
  template: `<div>
    <p>A: {{a | percent}}</p>
    <p>B: {{b | percent:'4.3-5'}}</p>
  </div>`
})
export class PercentPipeComponent {
  a: number = 0.259;
  b: number = 1.3495;
}
// #enddocregion

// #docregion CurrencyPipe
@Component({
  selector: 'currency-pipe',
  template: `<div>
    <p>A: {{a | currency:'USD':false}}</p>
    <p>B: {{b | currency:'USD':true:'4.2-2'}}</p>
  </div>`
})
export class CurrencyPipeComponent {
  a: number = 0.259;
  b: number = 1.3495;
}

```

### SlicePipe_string

```ts

@Component({
  selector: 'slice-string-pipe',
  template: `<div>
    <p>{{str}}[0:4]: '{{str | slice:0:4}}' - output is expected to be 'abcd'</p>
    <p>{{str}}[4:0]: '{{str | slice:4:0}}' - output is expected to be ''</p>
    <p>{{str}}[-4]: '{{str | slice:-4}}' - output is expected to be 'ghij'</p>
    <p>{{str}}[-4:-2]: '{{str | slice:-4:-2}}' - output is expected to be 'gh'</p>
    <p>{{str}}[-100]: '{{str | slice:-100}}' - output is expected to be 'abcdefghij'</p>
    <p>{{str}}[100]: '{{str | slice:100}}' - output is expected to be ''</p>
  </div>`
})
export class SlicePipeStringComponent {
  str: string = 'abcdefghij';
}
```

### SlicePipe_list
```ts
@Component({
  selector: 'slice-list-pipe',
  template: `<ul>
    <li *ngFor="let i of collection | slice:1:3">{{i}}</li>
  </ul>`
})
export class SlicePipeListComponent {
  collection: string[] = ['a', 'b', 'c', 'd'];
}
```