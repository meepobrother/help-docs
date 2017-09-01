```ts
export {
  NgClass,
  NgComponentOutlet,
  NgFor,
  NgForOf,
  NgForOfContext,
  NgIf,
  NgIfContext,
  NgPlural,
  NgPluralCase,
  NgStyle,
  NgSwitch,
  NgSwitchCase,
  NgSwitchDefault,
  NgTemplateOutlet
};



/**
 * A collection of Angular directives that are likely to be used in each and every Angular
 * application.
 */
export const COMMON_DIRECTIVES: Provider[] = [
  NgClass,
  NgComponentOutlet,
  NgForOf,
  NgIf,
  NgTemplateOutlet,
  NgStyle,
  NgSwitch,
  NgSwitchCase,
  NgSwitchDefault,
  NgPlural,
  NgPluralCase,
];

/**
 * A collection of deprecated directives that are no longer part of the core module.
 */
export const COMMON_DEPRECATED_DIRECTIVES: Provider[] = [NgFor];
```


#### [ngClass]

```ts
import {
  Directive, 
  DoCheck, 
  ElementRef, 
  Input, 
  IterableChanges, 
  IterableDiffer, 
  IterableDiffers, 
  KeyValueChanges, 
  KeyValueDiffer, 
  KeyValueDiffers, 
  Renderer, 
  ɵisListLikeIterable as isListLikeIterable, 
  ɵstringify as stringify
} from '@angular/core';

@Directive({selector: '[ngClass]'})
export class NgClass implements DoCheck {
  private _iterableDiffer: IterableDiffer<string>|null;
  private _keyValueDiffer: KeyValueDiffer<string, any>|null;
  private _initialClasses: string[] = [];
  private _rawClass: string[]|Set<string>|{[klass: string]: any};

  constructor(
      private _iterableDiffers: IterableDiffers, 
      private _keyValueDiffers: KeyValueDiffers,
      private _ngEl: ElementRef, 
      private _renderer: Renderer
  ) {}
  // [class]="" string
  @Input('class')
  set klass(v: string) {
    this._applyInitialClasses(true);
    this._initialClasses = typeof v === 'string' ? v.split(/\s+/) : [];
    this._applyInitialClasses(false);
    this._applyClasses(this._rawClass, false);
  }
  // ngClass Array,Object
  @Input()
  set ngClass(v: string|string[]|Set<string>|{[klass: string]: any}) {
    this._cleanupClasses(this._rawClass);

    this._iterableDiffer = null;
    this._keyValueDiffer = null;

    this._rawClass = typeof v === 'string' ? v.split(/\s+/) : v;

    if (this._rawClass) {
      if (isListLikeIterable(this._rawClass)) {
        this._iterableDiffer = this._iterableDiffers.find(this._rawClass).create();
      } else {
        this._keyValueDiffer = this._keyValueDiffers.find(this._rawClass).create();
      }
    }
  }
  // 检查变化
  ngDoCheck(): void {
    if (this._iterableDiffer) {
      const iterableChanges = this._iterableDiffer.diff(this._rawClass as string[]);
      if (iterableChanges) {
        this._applyIterableChanges(iterableChanges);
      }
    } else if (this._keyValueDiffer) {
      const keyValueChanges = this._keyValueDiffer.diff(this._rawClass as{[k: string]: any});
      if (keyValueChanges) {
        this._applyKeyValueChanges(keyValueChanges);
      }
    }
  }
  // 清理类
  private _cleanupClasses(rawClassVal: string[]|{[klass: string]: any}): void {
    this._applyClasses(rawClassVal, true);
    this._applyInitialClasses(false);
  }
  // key value 改变
  private _applyKeyValueChanges(changes: KeyValueChanges<string, any>): void {
    changes.forEachAddedItem((record) => this._toggleClass(record.key, record.currentValue));
    changes.forEachChangedItem((record) => this._toggleClass(record.key, record.currentValue));
    changes.forEachRemovedItem((record) => {
      if (record.previousValue) {
        this._toggleClass(record.key, false);
      }
    });
  }
  // 字符串改变
  private _applyIterableChanges(changes: IterableChanges<string>): void {
    changes.forEachAddedItem((record) => {
      if (typeof record.item === 'string') {
        this._toggleClass(record.item, true);
      } else {
        throw new Error(
            `NgClass can only toggle CSS classes expressed as strings, got ${stringify(record.item)}`);
      }
    });

    changes.forEachRemovedItem((record) => this._toggleClass(record.item, false));
  }

  private _applyInitialClasses(isCleanup: boolean) {
    this._initialClasses.forEach(klass => this._toggleClass(klass, !isCleanup));
  }
  //支持 Object,Array,String,Set
  private _applyClasses(
      rawClassVal: string[]|Set<string>|{[klass: string]: any}, 
      isCleanup: boolean
  ) {
    if (rawClassVal) {
      if (Array.isArray(rawClassVal) || rawClassVal instanceof Set) {
        (<any>rawClassVal).forEach((klass: string) => this._toggleClass(klass, !isCleanup));
      } else {
        Object.keys(rawClassVal).forEach(klass => {
          if (rawClassVal[klass] != null) this._toggleClass(klass, !isCleanup);
        });
      }
    }
  }
  //添加删除class
  private _toggleClass(klass: string, enabled: any): void {
    klass = klass.trim();
    if (klass) {
      klass.split(/\s+/g).forEach(klass => { 
        this._renderer.setElementClass(this._ngEl.nativeElement, klass, !!enabled); 
      });
    }
  }
}


```

#### ngComponentOutlet


```ts


import {
  ComponentFactoryResolver, 
  ComponentRef, 
  Directive, 
  Injector, 
  Input, 
  NgModuleFactory, 
  NgModuleRef, 
  OnChanges, 
  OnDestroy, 
  SimpleChanges, 
  StaticProvider, 
  Type, 
  ViewContainerRef
} from '@angular/core';



/**
 * Instantiates a single {@link Component} type and inserts its Host View into current View.
 * `NgComponentOutlet` provides a declarative approach for dynamic component creation.
 *
 * `NgComponentOutlet` requires a component type, if a falsy value is set the view will clear and
 * any existing component will get destroyed.
 *
 * ### Fine tune control
 *
 * You can control the component creation process by using the following optional attributes:
 *
 * * `ngComponentOutletInjector`: Optional custom {@link Injector} that will be used as parent for
 * the Component. Defaults to the injector of the current view container.
 *
 * * `ngComponentOutletContent`: Optional list of projectable nodes to insert into the content
 * section of the component, if exists.
 *
 * * `ngComponentOutletNgModuleFactory`: Optional module factory to allow dynamically loading other
 * module, then load a component from that module.
 *
 * ### Syntax
 *
 * Simple
 * ```
 * <ng-container *ngComponentOutlet="componentTypeExpression"></ng-container>
 * ```
 *
 * Customized injector/content
 * ```
 * <ng-container *ngComponentOutlet="componentTypeExpression;
 *                                   injector: injectorExpression;
 *                                   content: contentNodesExpression;">
 * </ng-container>
 * ```
 *
 * Customized ngModuleFactory
 * ```
 * <ng-container *ngComponentOutlet="componentTypeExpression;
 *                                   ngModuleFactory: moduleFactory;">
 * </ng-container>
 * ```
 * ## Example
 *
 * {@example common/ngComponentOutlet/ts/module.ts region='SimpleExample'}
 *
 * A more complete example with additional options:
 *
 * {@example common/ngComponentOutlet/ts/module.ts region='CompleteExample'}

 * A more complete example with ngModuleFactory:
 *
 * {@example common/ngComponentOutlet/ts/module.ts region='NgModuleFactoryExample'}
 *
 * @experimental
 */
@Directive({selector: '[ngComponentOutlet]'})
export class NgComponentOutlet implements OnChanges, OnDestroy {
  @Input() ngComponentOutlet: Type<any>;
  @Input() ngComponentOutletInjector: Injector;
  @Input() ngComponentOutletContent: any[][];
  @Input() ngComponentOutletNgModuleFactory: NgModuleFactory<any>;

  private _componentRef: ComponentRef<any>|null = null;
  private _moduleRef: NgModuleRef<any>|null = null;

  constructor(private _viewContainerRef: ViewContainerRef) {}

  ngOnChanges(changes: SimpleChanges) {
    this._viewContainerRef.clear();
    this._componentRef = null;

    if (this.ngComponentOutlet) {
      const elInjector = this.ngComponentOutletInjector || this._viewContainerRef.parentInjector;

      if (changes['ngComponentOutletNgModuleFactory']) {
        if (this._moduleRef) this._moduleRef.destroy();

        if (this.ngComponentOutletNgModuleFactory) {
          const parentModule = elInjector.get(NgModuleRef);
          this._moduleRef = this.ngComponentOutletNgModuleFactory.create(parentModule.injector);
        } else {
          this._moduleRef = null;
        }
      }

      const componentFactoryResolver = this._moduleRef ? this._moduleRef.componentFactoryResolver :
                                                         elInjector.get(ComponentFactoryResolver);

      const componentFactory =
          componentFactoryResolver.resolveComponentFactory(this.ngComponentOutlet);

      this._componentRef = this._viewContainerRef.createComponent(
          componentFactory, this._viewContainerRef.length, elInjector,
          this.ngComponentOutletContent);
    }
  }

  ngOnDestroy() {
    if (this._moduleRef) this._moduleRef.destroy();
  }
}


```

#### [ngFor][ngForOf]

```ts


import {
  ChangeDetectorRef, 
  Directive, 
  DoCheck, 
  EmbeddedViewRef, 
  Input, 
  IterableChangeRecord, 
  IterableChanges, 
  IterableDiffer, 
  IterableDiffers, 
  NgIterable, 
  OnChanges, 
  SimpleChanges, 
  TemplateRef, 
  TrackByFunction, 
  ViewContainerRef, 
  forwardRef, 
  isDevMode
} from '@angular/core';

/**
 * @stable
 */
export class NgForOfContext<T> {
  constructor(
      public $implicit: T, public ngForOf: NgIterable<T>, public index: number,
      public count: number) {}

  get first(): boolean { return this.index === 0; }

  get last(): boolean { return this.index === this.count - 1; }

  get even(): boolean { return this.index % 2 === 0; }

  get odd(): boolean { return !this.even; }
}

/**
 * The `NgForOf` directive instantiates a template once per item from an iterable. The context
 * for each instantiated template inherits from the outer context with the given loop variable
 * set to the current item from the iterable.
 *
 * ### Local Variables
 *
 * `NgForOf` provides several exported values that can be aliased to local variables:
 *
 * - `$implicit: T`: The value of the individual items in the iterable (`ngForOf`).
 * - `ngForOf: NgIterable<T>`: The value of the iterable expression. Useful when the expression is
 * more complex then a property access, for example when using the async pipe (`userStreams |
 * async`).
 * - `index: number`: The index of the current item in the iterable.
 * - `first: boolean`: True when the item is the first item in the iterable.
 * - `last: boolean`: True when the item is the last item in the iterable.
 * - `even: boolean`: True when the item has an even index in the iterable.
 * - `odd: boolean`: True when the item has an odd index in the iterable.
 *
 * ```
 * <li *ngFor="let user of userObservable | async as users; index as i; first as isFirst">
 *    {{i}}/{{users.length}}. {{user}} <span *ngIf="isFirst">default</span>
 * </li>
 * ```
 *
 * ### Change Propagation
 *
 * When the contents of the iterator changes, `NgForOf` makes the corresponding changes to the DOM:
 *
 * * When an item is added, a new instance of the template is added to the DOM.
 * * When an item is removed, its template instance is removed from the DOM.
 * * When items are reordered, their respective templates are reordered in the DOM.
 * * Otherwise, the DOM element for that item will remain the same.
 *
 * Angular uses object identity to track insertions and deletions within the iterator and reproduce
 * those changes in the DOM. This has important implications for animations and any stateful
 * controls (such as `<input>` elements which accept user input) that are present. Inserted rows can
 * be animated in, deleted rows can be animated out, and unchanged rows retain any unsaved state
 * such as user input.
 *
 * It is possible for the identities of elements in the iterator to change while the data does not.
 * This can happen, for example, if the iterator produced from an RPC to the server, and that
 * RPC is re-run. Even if the data hasn't changed, the second response will produce objects with
 * different identities, and Angular will tear down the entire DOM and rebuild it (as if all old
 * elements were deleted and all new elements inserted). This is an expensive operation and should
 * be avoided if possible.
 *
 * To customize the default tracking algorithm, `NgForOf` supports `trackBy` option.
 * `trackBy` takes a function which has two arguments: `index` and `item`.
 * If `trackBy` is given, Angular tracks changes by the return value of the function.
 *
 * ### Syntax
 *
 * - `<li *ngFor="let item of items; index as i; trackBy: trackByFn">...</li>`
 * - `<li template="ngFor let item of items; index as i; trackBy: trackByFn">...</li>`
 *
 * With `<ng-template>` element:
 *
 * ```
 * <ng-template ngFor let-item [ngForOf]="items" let-i="index" [ngForTrackBy]="trackByFn">
 *   <li>...</li>
 * </ng-template>
 * ```
 *
 * ### Example
 *
 * See a [live demo](http://plnkr.co/edit/KVuXxDp0qinGDyo307QW?p=preview) for a more detailed
 * example.
 *
 * @stable
 */
@Directive({selector: '[ngFor][ngForOf]'})
export class NgForOf<T> implements DoCheck, OnChanges {
  @Input() ngForOf: NgIterable<T>;
  @Input()
  set ngForTrackBy(fn: TrackByFunction<T>) {
    if (isDevMode() && fn != null && typeof fn !== 'function') {
      // TODO(vicb): use a log service once there is a public one available
      if (<any>console && <any>console.warn) {
        console.warn(
            `trackBy must be a function, but received ${JSON.stringify(fn)}. ` +
            `See https://angular.io/docs/ts/latest/api/common/index/NgFor-directive.html#!#change-propagation for more information.`);
      }
    }
    this._trackByFn = fn;
  }

  get ngForTrackBy(): TrackByFunction<T> { return this._trackByFn; }

  private _differ: IterableDiffer<T>|null = null;
  private _trackByFn: TrackByFunction<T>;

  constructor(
      private _viewContainer: ViewContainerRef, private _template: TemplateRef<NgForOfContext<T>>,
      private _differs: IterableDiffers) {}

  @Input()
  set ngForTemplate(value: TemplateRef<NgForOfContext<T>>) {
    // TODO(TS2.1): make TemplateRef<Partial<NgForRowOf<T>>> once we move to TS v2.1
    // The current type is too restrictive; a template that just uses index, for example,
    // should be acceptable.
    if (value) {
      this._template = value;
    }
  }

  ngOnChanges(changes: SimpleChanges): void {
    if ('ngForOf' in changes) {
      // React on ngForOf changes only once all inputs have been initialized
      const value = changes['ngForOf'].currentValue;
      if (!this._differ && value) {
        try {
          this._differ = this._differs.find(value).create(this.ngForTrackBy);
        } catch (e) {
          throw new Error(
              `Cannot find a differ supporting object '${value}' of type '${getTypeNameForDebugging(value)}'. NgFor only supports binding to Iterables such as Arrays.`);
        }
      }
    }
  }

  ngDoCheck(): void {
    if (this._differ) {
      const changes = this._differ.diff(this.ngForOf);
      if (changes) this._applyChanges(changes);
    }
  }

  private _applyChanges(changes: IterableChanges<T>) {
    const insertTuples: RecordViewTuple<T>[] = [];
    changes.forEachOperation(
        (item: IterableChangeRecord<any>, adjustedPreviousIndex: number, currentIndex: number) => {
          if (item.previousIndex == null) {
            const view = this._viewContainer.createEmbeddedView(
                this._template, new NgForOfContext<T>(null !, this.ngForOf, -1, -1), currentIndex);
            const tuple = new RecordViewTuple<T>(item, view);
            insertTuples.push(tuple);
          } else if (currentIndex == null) {
            this._viewContainer.remove(adjustedPreviousIndex);
          } else {
            const view = this._viewContainer.get(adjustedPreviousIndex) !;
            this._viewContainer.move(view, currentIndex);
            const tuple = new RecordViewTuple(item, <EmbeddedViewRef<NgForOfContext<T>>>view);
            insertTuples.push(tuple);
          }
        });

    for (let i = 0; i < insertTuples.length; i++) {
      this._perViewChange(insertTuples[i].view, insertTuples[i].record);
    }

    for (let i = 0, ilen = this._viewContainer.length; i < ilen; i++) {
      const viewRef = <EmbeddedViewRef<NgForOfContext<T>>>this._viewContainer.get(i);
      viewRef.context.index = i;
      viewRef.context.count = ilen;
    }

    changes.forEachIdentityChange((record: any) => {
      const viewRef =
          <EmbeddedViewRef<NgForOfContext<T>>>this._viewContainer.get(record.currentIndex);
      viewRef.context.$implicit = record.item;
    });
  }

  private _perViewChange(
      view: EmbeddedViewRef<NgForOfContext<T>>, record: IterableChangeRecord<any>) {
    view.context.$implicit = record.item;
  }
}

class RecordViewTuple<T> {
  constructor(public record: any, public view: EmbeddedViewRef<NgForOfContext<T>>) {}
}

/**
 * @deprecated from v4.0.0 - Use NgForOf<any> instead.
 */
export type NgFor = NgForOf<any>;

/**
 * @deprecated from v4.0.0 - Use NgForOf instead.
 */
export const NgFor = NgForOf;

export function getTypeNameForDebugging(type: any): string {
  return type['name'] || typeof type;
}


```