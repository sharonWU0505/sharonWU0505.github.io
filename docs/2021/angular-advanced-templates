---
title: "Angular Advanced Templates - ng-template, ng-container and ngTemplateOutlet"
date: "2021-09-25T11:00:00+08:00"
draft: false
slug: "angular-advanced-templates"
tags: ["Angular", "Angular Templates"]
post_keywords: "Angular,Angular Templates,ng-template,ng-container,ngTemplateOutlet"
---

撰寫 Angular templates 時一定會用到 `ng-template` 和 `ngTemplateOutlet` 來實現進階的功能，而它們經常與 `ng-container` 搭配使用。

我們將基於 [Angular ng-template, ng-container and ngTemplateOutlet - The Complete Guide To Angular Templates](https://blog.angular-university.io/angular-ng-template-ng-container-ngtemplateoutlet/) 這篇寫得非常好的文章來介紹它們的用途和使用方式。

<!--more-->

## `ng-template` Directive

顧名思義，`ng-template` directive 代表一段 Angular template，它們可以和其他 templates 組合成最終的 component template。`ng-template` 其實被廣泛應用在 Angular 自身的 structural directives, e.g. `ngIf`, `ngFor`, `ngSwitch` 當中。

如果我們今天撰寫一段 `ng-template`：

```typescript
@Component({
  selector: 'app-root',
  template: `      
      <ng-template>
          <button class="tab-button" (click)="login()">{{loginText}}</button>
          <button class="tab-button" (click)="signUp()">{{signUpText}}</button>
      </ng-template>
  `})
export class AppComponent {
    loginText = 'Login';
    signUpText = 'Sign Up'; 
    lessons = ['Lesson 1', 'Lessons 2'];

    login() {
        console.log('Login');
    }

    signUp() {
        console.log('Sign Up');
    }
}
```

大家注意到的第一件事應該會是**以上程式碼並沒有 render 任何東西到畫面中**。因為 `ng-template` 只是單純用來定義 `template`，而我們尚未使用它。

#### The `ng-template` Directive and `ngIf`

```angular
<div class="lessons-list" *ngIf="lessons else loading">
  ... 
</div>

<ng-template #loading>
    <div>Loading...</div>
</ng-template>
```

這是一個最常見的例子。我們透過定義 `loading` (`#loading`) 為 template reference，在沒有 `lessons` 資料時顯示 `loading` template。這段程式碼實際上會被 Angular 解讀為以下形式：

```angular
<ng-template [ngIf]="lessons" [ngIfElse]="loading">
   <div class="lessons-list">
     ... 
   </div>
</ng-template>

<ng-template #loading>
    <div>Loading...</div>
</ng-template>
```

此轉換也見於 `ngFor` 和 `ngSwitch`，這也是為什麼我們無法使用多個 structural directives 在同一元素上的原因，Angular 無法順利將它們解讀成 implicit `ng-template` 的格式。

如果要使用多個 structural directives 我們可以這樣寫：

```angular
<div *ngIf="lessons">
    <div class="lesson" *ngFor="let lesson of lessons">
        <div class="lesson-detail">
            {{lesson | json}}
        </div>
    </div>
</div>
```

不過你應該會想問：可以不要有多餘的 `div` 嗎？答案是可以的，而正是 `ng-container` 讓我們可以這麼做！

## `ng-container` Directive

```angular
<ng-container *ngIf="lessons">
    <div class="lesson" *ngFor="let lesson of lessons">
        <div class="lesson-detail">
            {{lesson | json}}
        </div>
    </div>
</ng-container>
```

**`ng-container` 使我們得以在不必生成多餘元素的情況下，將由 structural directive 控制的元素附帶到畫面上**。而它也能用來動態地注入 (inject) template 到畫面，一樣是透過 template reference `#loading`，並藉由 `ngTemplateOutlet` directive，我們能在頁面任何地方實例化 template。

```angular
<ng-container *ngTemplateOutlet="loading"></ng-container>
```

## Template Context

這時衍生出一個關鍵的問題：template 有自己的 variable scope 嗎？template 能取用哪些變數呢？

**`ng-template` instances 能取得它們被內嵌 (embedded) 之處的 context**，像是之前的例子中，我們能在 `ng-template` 裡面使用 `lessons`。然而**各個 template 亦可定義它們自己的 context object**。

```typescript
@Component({
  selector: 'app-root',
  template: `      
    <ng-template #estimateTemplate let-lessonsCounter="estimate">
      <div> Approximately {{lessonsCounter}} lessons ...</div>
    </ng-template>
    <ng-container *ngTemplateOutlet="estimateTemplate;context:ctx"></ng-container>
  `})
export class AppComponent {
    totalEstimate = 10;
    ctx = {estimate: this.totalEstimate};
}
```

Template 中我們以 prefix `let-` 定義它的 property `lessonsCounter`，此變數可在 template 中使用。而變數的 context 來自於一個在實例化時跟著 template 一起傳給 `ngTemplateOutlet` 的 **context object**。

除了在 template 中使用 `ng-template`，我們也能在 component level 經由 template references 跟 template 互動。

## Template References

我們用 `ViewChild` decorator 把 template 直接地注入到 component。

```typescript
@Component({
  selector: 'app-root',
  template: `      
      <ng-template #defaultTabButtons>
          <button class="tab-button" (click)="login()">{{loginText}}</button>
          <button class="tab-button" (click)="signUp()">{{signUpText}}</button>
      </ng-template>
  `})
export class AppComponent implements OnInit {
    @ViewChild('defaultTabButtons')
    private defaultTabButtonsTpl: TemplateRef<any>;

    ngOnInit() {
        console.log(this.defaultTabButtonsTpl);
    }
}
```

由於能在 component 中取得 template，我們就能將該 template 傳遞給 child components。

這常見於當我們想建立更客製化的 component，我們不只傳遞 configuration parameter 或 configuration object，我們亦能將 template 作為 input parameter。

#### Parent Component

```typescript
@Component({
  selector: 'app-root',
  template: `      
  <ng-template #customTabButtons>
    <div class="custom-class">
        <button class="tab-button" (click)="login()">{{loginText}}</button>
        <button class="tab-button" (click)="signUp()">{{signUpText}}</button>
    </div>
  </ng-template>
  <tab-container [headerTemplate]="customTabButtons"></tab-container>      
`})
export class AppComponent implements OnInit {}
```

#### Child Component `TabContainerComponent`

在沒有 input `headerTemplate` 時，render `defaultTabButtons`：

```typescript
@Component({
  selector: 'tab-container',
  template: `   
    <ng-template #defaultTabButtons>
      <div class="default-tab-buttons">
        ...
      </div>
    </ng-template>
    <ng-container *ngTemplateOutlet="headerTemplate ? headerTemplate: defaultTabButtons"></ng-container>
    ... rest of tab container component ...
  `})
export class TabContainerComponent {
    @Input()
    headerTemplate: TemplateRef<any>;
}
```

以上就是關於 `ng-template`、`ng-container` 和 `ngTemplateOutlet` 的介紹，希望有讓大家更理解它們能做到什麼事。
