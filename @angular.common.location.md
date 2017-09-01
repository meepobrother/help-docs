## @angular/common

#### location

```ts
export abstract class PlatformLocation {
  abstract getBaseHrefFromDOM(): string;
  abstract onPopState(fn: LocationChangeListener): void;
  abstract onHashChange(fn: LocationChangeListener): void;

  abstract get pathname(): string;
  abstract get search(): string;
  abstract get hash(): string;

  abstract replaceState(state: any, title: string, url: string): void;

  abstract pushState(state: any, title: string, url: string): void;

  abstract forward(): void;

  abstract back(): void;
}

export const LOCATION_INITIALIZED = new InjectionToken<Promise<any>>('Location Initialized');

export interface     { type: string; }

export interface LocationChangeListener { (e: LocationChangeEvent): any; }

```

#### LocationStrategy

```ts
export abstract class LocationStrategy {
  abstract path(includeHash?: boolean): string;
  abstract prepareExternalUrl(internal: string): string;
  abstract pushState(state: any, title: string, url: string, queryParams: string): void;
  abstract replaceState(state: any, title: string, url: string, queryParams: string): void;
  abstract forward(): void;
  abstract back(): void;
  abstract onPopState(fn: LocationChangeListener): void;
  abstract getBaseHref(): string;
}
// 自定义注入字符串
export const APP_BASE_HREF = new InjectionToken<string>('appBaseHref');
```

#### HashLocationStrategy
```ts

@Injectable()
export class HashLocationStrategy extends LocationStrategy {
  private _baseHref: string = '';
  constructor(
      private _platformLocation: PlatformLocation,
      @Optional() @Inject(APP_BASE_HREF) _baseHref?: string //注入自定义字符串
  ) {
    super();
    if (_baseHref != null) {
      this._baseHref = _baseHref;
    }
  }

  onPopState(fn: LocationChangeListener): void {
    this._platformLocation.onPopState(fn);
    this._platformLocation.onHashChange(fn);
  }

  getBaseHref(): string { return this._baseHref; }

  path(includeHash: boolean = false): string {
    // the hash value is always prefixed with a `#`
    // and if it is empty then it will stay empty
    let path = this._platformLocation.hash;
    if (path == null) path = '#';

    return path.length > 0 ? path.substring(1) : path;
  }

  prepareExternalUrl(internal: string): string {
    const url = Location.joinWithSlash(this._baseHref, internal);
    return url.length > 0 ? ('#' + url) : url;
  }

  pushState(state: any, title: string, path: string, queryParams: string) {
    let url: string|null =
        this.prepareExternalUrl(path + Location.normalizeQueryParams(queryParams));
    if (url.length == 0) {
      url = this._platformLocation.pathname;
    }
    this._platformLocation.pushState(state, title, url);
  }

  replaceState(state: any, title: string, path: string, queryParams: string) {
    let url = this.prepareExternalUrl(path + Location.normalizeQueryParams(queryParams));
    if (url.length == 0) {
      url = this._platformLocation.pathname;
    }
    this._platformLocation.replaceState(state, title, url);
  }

  forward(): void { this._platformLocation.forward(); }

  back(): void { this._platformLocation.back(); }
}

```


#### Location

```ts

/** @experimental */
export interface PopStateEvent {
  pop?: boolean;
  type?: string;
  url?: string;
}

@Injectable()
export class Location {
  /** @internal */
  _subject: EventEmitter<any> = new EventEmitter();
  /** @internal */
  _baseHref: string;
  /** @internal */
  _platformStrategy: LocationStrategy;

  constructor(platformStrategy: LocationStrategy) {
    this._platformStrategy = platformStrategy;
    const browserBaseHref = this._platformStrategy.getBaseHref();
    this._baseHref = Location.stripTrailingSlash(_stripIndexHtml(browserBaseHref));
    this._platformStrategy.onPopState((ev) => {
      this._subject.emit({
        'url': this.path(true),
        'pop': true,
        'type': ev.type,
      });
    });
  }

  /**
   * Returns the normalized URL path.
   */
  // TODO: vsavkin. Remove the boolean flag and always include hash once the deprecated router is
  // removed.
  path(includeHash: boolean = false): string {
    return this.normalize(this._platformStrategy.path(includeHash));
  }

  /**
   * Normalizes the given path and compares to the current normalized path.
   */
  isCurrentPathEqualTo(path: string, query: string = ''): boolean {
    return this.path() == this.normalize(path + Location.normalizeQueryParams(query));
  }

  /**
   * Given a string representing a URL, returns the normalized URL path without leading or
   * trailing slashes.
   */
  normalize(url: string): string {
    return Location.stripTrailingSlash(_stripBaseHref(this._baseHref, _stripIndexHtml(url)));
  }

  /**
   * Given a string representing a URL, returns the platform-specific external URL path.
   * If the given URL doesn't begin with a leading slash (`'/'`), this method adds one
   * before normalizing. This method will also add a hash if `HashLocationStrategy` is
   * used, or the `APP_BASE_HREF` if the `PathLocationStrategy` is in use.
   */
  prepareExternalUrl(url: string): string {
    if (url && url[0] !== '/') {
      url = '/' + url;
    }
    return this._platformStrategy.prepareExternalUrl(url);
  }

  // TODO: rename this method to pushState
  /**
   * Changes the browsers URL to the normalized version of the given URL, and pushes a
   * new item onto the platform's history.
   */
  go(path: string, query: string = ''): void {
    this._platformStrategy.pushState(null, '', path, query);
  }

  /**
   * Changes the browsers URL to the normalized version of the given URL, and replaces
   * the top item on the platform's history stack.
   */
  replaceState(path: string, query: string = ''): void {
    this._platformStrategy.replaceState(null, '', path, query);
  }

  /**
   * Navigates forward in the platform's history.
   */
  forward(): void { this._platformStrategy.forward(); }

  /**
   * Navigates back in the platform's history.
   */
  back(): void { this._platformStrategy.back(); }

  /**
   * Subscribe to the platform's `popState` events.
   */
  subscribe(
      onNext: (value: PopStateEvent) => void, onThrow?: ((exception: any) => void)|null,
      onReturn?: (() => void)|null): Object {
    return this._subject.subscribe({next: onNext, error: onThrow, complete: onReturn});
  }

  /**
   * Given a string of url parameters, prepend with '?' if needed, otherwise return parameters as
   * is.
   */
  public static normalizeQueryParams(params: string): string {
    return params && params[0] !== '?' ? '?' + params : params;
  }

  /**
   * Given 2 parts of a url, join them with a slash if needed.
   */
  public static joinWithSlash(start: string, end: string): string {
    if (start.length == 0) {
      return end;
    }
    if (end.length == 0) {
      return start;
    }
    let slashes = 0;
    if (start.endsWith('/')) {
      slashes++;
    }
    if (end.startsWith('/')) {
      slashes++;
    }
    if (slashes == 2) {
      return start + end.substring(1);
    }
    if (slashes == 1) {
      return start + end;
    }
    return start + '/' + end;
  }

  /**
   * If url has a trailing slash, remove it, otherwise return url as is. This
   * method looks for the first occurence of either #, ?, or the end of the
   * line as `/` characters after any of these should not be replaced.
   */
  public static stripTrailingSlash(url: string): string {
    const match = url.match(/#|\?|$/);
    const pathEndIdx = match && match.index || url.length;
    const droppedSlashIdx = pathEndIdx - (url[pathEndIdx - 1] === '/' ? 1 : 0);
    return url.slice(0, droppedSlashIdx) + url.slice(pathEndIdx);
  }
}

function _stripBaseHref(baseHref: string, url: string): string {
  return baseHref && url.startsWith(baseHref) ? url.substring(baseHref.length) : url;
}

function _stripIndexHtml(url: string): string {
  return url.replace(/\/index.html$/, '');
}

```