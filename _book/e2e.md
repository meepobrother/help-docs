## expect

```ts
import { browser, by, element } from 'protractor';

export class NgWidthTestPage {
  //浏览器路由到某路径
  navigateTo() {
    return browser.get('/');
  }
  getParagraphText() {
    // 检查h1里面的内容
    return element(by.css('app-root h1')).getText();
  }
}

```


```ts
import { NgWidthTestPage } from './app.po';

describe('描述',()=>{
    let page: NgWidthTestPage;
    // 相当于初始化
    beforeEach(() => {
        page = new NgWidthTestPage();
    });
    //断言
    it('should display welcome message', () => {
        page.navigateTo();
        expect(page.getParagraphText()).toEqual('Welcome to app!');
    });
})
```