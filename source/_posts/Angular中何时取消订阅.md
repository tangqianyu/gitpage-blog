---
title: Angular中何时取消订阅（译）
date: 2020-06-16 20:48:23
tags: 
- Angular
- rxjs
categories: js      
---


在js中，当你订阅observable或event时，你需要及时取消订阅来避免内存泄漏。

下面是几种需要在生命周期`ngOnDestroy`中取消订阅的情况。

## Forms

```ts
export class TestComponent {

  ngOnInit() {
    this.form = new FormGroup({...});
    this.valueChanges  = this.form.valueChanges.subscribe(console.log);
    this.statusChanges = this.form.statusChanges.subscribe(console.log);
  }

  ngOnDestroy() {
    this.valueChanges.unsubscribe();
    this.statusChanges.unsubscribe();
  }

}

```
这同样适用于任一的form control 。

## The Router

```ts
export class TestComponent {
  constructor(private route: ActivatedRoute, private router: Router) { }

  ngOnInit() {
    this.route.params.subscribe(console.log);
    this.route.queryParams.subscribe(console.log);
    this.route.fragment.subscribe(console.log);
    this.route.data.subscribe(console.log);
    this.route.url.subscribe(console.log);
    
    this.router.events.subscribe(console.log);
  }

  ngOnDestroy() {
    // You should unsubscribe from each observable here
  }

}

```
根据官方文档，Angular应该为你取消了订阅，但显而易见的，这是一个[bug](https://github.com/angular/angular/issues/16261)

## Renderer Service

```ts
export class TestComponent {
constructor(private renderer: Renderer2, 
            private element : ElementRef) { }

  ngOnInit() {
    this.click = this.renderer.listen(this.element.nativeElement, "click", handler);
  }

  ngOnDestroy() {
    this.click.unsubscribe();
  }

}

```

## Infinite Observables

当你使用无限发出流的Observables，你应该取消订阅（除非有特殊情况）。eg:`interval()`或`fromEvent()`

```ts
export class TestComponent {

  constructor(private element : ElementRef) { }

  interval: Subscription;
  click: Subscription;

  ngOnInit() {
    this.interval = Observable.interval(1000).subscribe(console.log);
    this.click = Observable.fromEvent(this.element.nativeElement, 'click').subscribe(console.log);
  }

  ngOnDestroy() {
    this.interval.unsubscribe();
    this.click.unsubscribe();
  }

}

```

## Redux Store

```ts
export class TestComponent {

  constructor(private store: Store) { }

  todos: Subscription;

  ngOnInit() {
     this.todos = this.store.select('todos').subscribe(console.log);  
  }

  ngOnDestroy() {
    this.todos.unsubscribe();
  }

}

```

# 不能取消订阅

## Async pipe

```ts
@Component({
  selector: 'test',
  template: `<todos [todos]="todos$ | async"></todos>`
})
export class TestComponent {

  constructor(private store: Store) { }

  ngOnInit() {
     this.todos$ = this.store.select('todos');
  }

}

```
当组件销毁的时候`async`管道会自动取消订阅，以避免内存泄漏。

## @HostListener

```ts
export class TestDirective {

  @HostListener('click')
  onClick() {
    ....
  }

}


```

## Finite Observable

当你使用发出有限流的Observable时，通常情况下不需要取消订阅，eg:`HTTP`服务或`timer`

```ts
export class TestComponent {

  constructor(private http: Http) { }

  ngOnInit() {
    Observable.timer(1000).subscribe(console.log);
    this.http.get('http://api.com').subscribe(console.log);
  }


}

```

## 最后小结
你应该尽可能少的调用`unsubscribe `方法

```ts
export class TestComponent {

  constructor(private store: Store) { }

  private componetDestroyed: Subject = new Subject();
  todos: Subscription;
  posts: Subscription;

  ngOnInit() {
     this.todos = this.store.select('todos').takeUntil(this.componetDestroyed).subscribe(console.log); 

     this.posts = this.store.select('posts').takeUntil(this.componetDestroyed).subscribe(console.log); 
  }

  ngOnDestroy() {
    this.componetDestroyed.next();
    this.componetDestroyed.unsubscribe();
  }

}
```

> 原文链接: https://netbasal.com/when-to-unsubscribe-in-angular-d61c6b21bad3

