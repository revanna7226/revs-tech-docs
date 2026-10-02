# Lifecycle Hooks

In Angular, components and directives go through a lifecycle from creation → rendering → updating → destruction.
Angular provides **lifecycle hook methods** so you can run custom logic at specific moments.

---

## 🔥 **List of Angular Lifecycle Hooks**

| Hook                      | Purpose                            | When It Runs                                           |
| ------------------------- | ---------------------------------- | ------------------------------------------------------ |
| **ngOnChanges**           | Respond to input property changes  | When `@Input()` values change (including first change) |
| **ngOnInit**              | Initialization logic               | Once after the first `ngOnChanges`                     |
| **ngDoCheck**             | Custom change-detection logic      | During every CD cycle                                  |
| **ngAfterContentInit**    | After `ng-content` is projected    | Once after first projection                            |
| **ngAfterContentChecked** | After projected content is checked | Runs after _every_ check                               |
| **ngAfterViewInit**       | View is initialized                | Once after component's view + child views render       |
| **ngAfterViewChecked**    | After the view is checked          | Runs after every check                                 |
| **ngOnDestroy**           | Cleanup                            | Just before component is destroyed                     |

---

## 🟦 **1. ngOnChanges()**

`ngOnChanges()` is an Angular lifecycle hook that runs **whenever an @Input() property of a component or directive changes**.

It gives you:

- Old value
- New value
- Whether it's the **first change**
- A `SimpleChanges` object describing all changed inputs

### Signature:

```ts
ngOnChanges(changes: SimpleChanges): void
```

---

### When Does ngOnChanges Run?

It runs:

1. **Before ngOnInit** (first time)
2. **Every time any @Input() value changes**
3. **Even when the parent triggers change detection**
   (new reference for objects/arrays triggers changes)

It **does NOT run** if:

- The @Input value is mutated **without changing the reference**
  (e.g., pushing into same array)

---

### Basic ngOnChanges Example

#### **Parent Component**

```ts
@Component({
  selector: "app-parent",
  template: `
    <button (click)="count++">Increase</button>
    <app-child [counter]="count"></app-child>
  `,
})
export class ParentComponent {
  count = 0;
}
```

#### **Child Component**

```ts
import { Component, Input, OnChanges, SimpleChanges } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<p>Counter: {{ counter }}</p>`,
})
export class ChildComponent implements OnChanges {
  @Input() counter!: number;

  ngOnChanges(changes: SimpleChanges): void {
    console.log("ngOnChanges called");
    console.log(changes);
  }
}
```

#### Output Example in Console:

```
{
  counter: {
    currentValue: 1,
    previousValue: 0,
    firstChange: false
  }
}
```

---

## 🟦 **2. ngOnInit()**

`ngOnInit()` is a lifecycle hook provided by Angular through the `OnInit` interface.

It is invoked **once**, after Angular:

1. Creates the component
2. Sets all `@Input()` properties
3. Calls `ngOnChanges()` for the first time

This makes `ngOnInit()` the best place for:

- Initializing component variables
- Making API calls
- Fetching data from services
- Setting default values
- Writing startup logic

---

### How to Use ngOnInit()

A component uses it by implementing the `OnInit` interface and writing a method called `ngOnInit()`.

### Syntax

```ts
import { Component, OnInit } from "@angular/core";

export class MyComponent implements OnInit {
  ngOnInit() {
    // initialization logic here
  }
}
```

---

### Why `ngOnInit()` Instead of Constructor?

#### Constructor

- Used for dependency injection (services)
- Should NOT contain Angular logic
- Called BEFORE Angular binds inputs

#### ngOnInit()

- Safe place to run initialization logic
- Runs AFTER Angular inputs are ready

#### Example of why it matters:

```ts
constructor() {
  console.log("Constructor:", this.inputValue); // ❌ undefined
}

ngOnInit() {
  console.log("ngOnInit:", this.inputValue); // ✅ has correct value
}
```

---

### Example: Using ngOnInit() to Fetch API Data

```ts
import { Component, OnInit } from "@angular/core";
import { UserService } from "../services/user.service";

@Component({
  selector: "app-users",
  template: `<div *ngFor="let u of users">{{ u.name }}</div>`,
})
export class UsersComponent implements OnInit {
  users: any[] = [];

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.userService.getUsers().subscribe((data) => {
      this.users = data;
    });
  }
}
```

**Why in ngOnInit?**

- Inputs and services are available
- Safe time to make HTTP calls

---

## 🟦 **3. ngDoCheck()**

`ngDoCheck()` is a lifecycle hook that Angular calls **on every change detection cycle**.

This means:

✔ It runs **very frequently**
✔ It is triggered even if nothing changed
✔ It allows **custom change detection** logic beyond what Angular detects by default

---

### Why Does Angular Need ngDoCheck()?

Angular normally uses **default change detection**, which automatically detects changes in:

- Primitive values (`string`, `number`, `boolean`)
- Object **reference** changes (but NOT deep property changes)
- Template expression changes
- @Input() binding reference changes

But Angular **does NOT** detect:

- Mutation inside an object or array
- Replacing items inside an array
- Updating a property inside a nested object

❗For these cases, you use **ngDoCheck()**.

---

### **When to Use ngDoCheck()**

Use it when you need to detect changes that Angular does **not** detect automatically:

#### Example use cases:

- Detecting **deep changes** inside objects/arrays
- Performance optimizations
- Custom form validation logic
- Detect uncontrolled data changes from 3rd party APIs
- Detect changes without relying on @Input() reference updates

---

### Example: Detect Deep Changes Inside Array (Mutation)

Angular **won’t detect** that array values changed because reference stayed same.

#### Angular will NOT detect this:

```ts
this.items.push("New item");
```

But `ngDoCheck()` can.

### Full Working Example

##### parent.component.ts

```ts
@Component({
  selector: "app-parent",
  template: `
    <button (click)="addItem()">Add Item</button>
    <app-child [items]="items"></app-child>
  `,
})
export class ParentComponent {
  items = ["A", "B"];

  addItem() {
    this.items.push("C"); // reference not changed → Angular does not detect
  }
}
```

##### child.component.ts

```ts
import { Component, DoCheck, Input } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<p>Items: {{ items | json }}</p>`,
})
export class ChildComponent implements DoCheck {
  @Input() items!: string[];
  previousLength = 0;

  ngDoCheck() {
    if (this.items.length !== this.previousLength) {
      console.log("Array changed:", this.items);
      this.previousLength = this.items.length;
    }
  }
}
```

### What happens?

- Parent mutates array (`push`)
- Angular does NOT trigger `ngOnChanges`
- `ngDoCheck()` still detects the change manually
- Now you can implement custom logic

---

## 🟦 **4. ngAfterContentInit()**

`ngAfterContentInit()` is a lifecycle hook from the **AfterContentInit** interface.

It is called **exactly once** after Angular **projects external content** (i.e., content passed between component tags using `<ng-content>`) into the component.

---

### When Does It Run?

`ngAfterContentInit()` runs:

- After Angular inserts projected content into the component's view
- Before `ngAfterContentChecked()`
- Only one time in the component lifecycle

---

### Why Do We Need It?

Use `ngAfterContentInit()` when you want to:

- Access or modify projected content
- Read child elements projected from parent
- Use `@ContentChild` or `@ContentChildren`
- Perform initialization dependent on projected elements

`ngAfterContentInit()` lets you interact with this projected content.

---

### Basic Example with @ContentChild

**Parent HTML**

```html
<app-wrapper>
  <p #paraRef>This is projected content</p>
</app-wrapper>
```

**Child Component Template (`wrapper.component.html`)**

```html
<div>
  <ng-content></ng-content>
</div>
```

**Child Component TS (`wrapper.component.ts`)**

```ts
import {
  Component,
  ContentChild,
  ElementRef,
  AfterContentInit,
} from "@angular/core";

@Component({
  selector: "app-wrapper",
  templateUrl: "./wrapper.component.html",
})
export class WrapperComponent implements AfterContentInit {
  @ContentChild("paraRef") projectedPara!: ElementRef;

  ngAfterContentInit() {
    console.log("ngAfterContentInit called");
    console.log(
      "Projected Content:",
      this.projectedPara.nativeElement.textContent
    );
  }
}
```

### Console Output

```
ngAfterContentInit called
Projected Content: This is projected content
```

---

## 🟦 **5. ngAfterContentChecked()**

`ngAfterContentChecked()` is an Angular lifecycle hook that runs:

- After Angular checks the projected content inside a component(i.e., anything passed between `<ng-content></ng-content>` tags)
- It runs on **every change detection cycle**, not just once.
- It is often used to:

  - Detect changes in **content projected from parent**
  - Perform operations after content is updated
  - Debug parent → child content interactions

---

### IMP: What does “Projected Content” mean?

If you have:
**Parent View**

```html
<app-card>
  <!-- This is projected content -->
  <p #desc>Description text</p>
</app-card>
```

Then inside `app-card`, there is:
**Child View**

```html
<ng-content></ng-content>
```

That `<p>` is **projected content**.

---

### Example - Logging Each Time Content Is Checked

**`card.component.ts`**

```ts
import { Component, AfterContentChecked } from "@angular/core";

@Component({
  selector: "app-card",
  template: `
    <h3>Card Component</h3>
    <ng-content></ng-content>
  `,
})
export class CardComponent implements AfterContentChecked {
  ngAfterContentChecked() {
    console.log("ngAfterContentChecked - projected content checked.");
  }
}
```

**`parent.component.html`**

```html
<app-card>
  <p>This is projected content!</p>
</app-card>
```

**Output (console)**

```
ngAfterContentChecked - projected content checked.
ngAfterContentChecked - projected content checked.
ngAfterContentChecked - projected content checked.
...
```

It runs **many times** (every time Angular runs change detection).

---

### Example - Using @ContentChild

Track projected content changes.

**`card.component.ts`**

```ts
import {
  Component,
  ContentChild,
  ElementRef,
  AfterContentChecked,
} from "@angular/core";

@Component({
  selector: "app-card",
  template: `
    <div class="card">
      <ng-content></ng-content>
    </div>
  `,
})
export class CardComponent implements AfterContentChecked {
  @ContentChild("desc") description!: ElementRef;

  ngAfterContentChecked() {
    console.log("Content:", this.description?.nativeElement.textContent);
  }
}
```

** `parent.component.html`**

```html
<app-card>
  <p #desc>{{ message }}</p>
</app-card>

<button (click)="update()">Update Content</button>
```

**`parent.component.ts`**

```ts
message = "Hello Angular";

update() {
  this.message = "Updated description!";
}
```

**Result**

Every time the message updates:

```
Content: Updated description!
Content: Updated description!
...
```

---

## 🟦 **6. ngAfterViewInit()**

`ngAfterViewInit()` is an Angular lifecycle hook that runs **once** after:

✔ The component’s view (template) is fully initialized
✔ All child components & directives in the view are also initialized
✔ All `@ViewChild()` and `@ViewChildren()` references are available

---

### 📌 Important Points

- Runs Only Once: It is called **after the first rendering** of the view.
- Safe place to access DOM:

You can finally interact with:

- `@ViewChild()` DOM elements
- Child components
- Template-driven forms
- Material components
- Charts and JS libraries (Leaflet, Chart.js, etc.)

Why not use ngOnInit?

`ngOnInit()` runs before child views are created →
so `@ViewChild()` will be **undefined** inside `ngOnInit()`.

---

### Examples

#### Simple Example — Accessing a DOM Element

**HTML**

```html
<h2 #heading>Angular Lifecycle Hook Demo</h2>
```

**Component**

```ts
import { Component, AfterViewInit, ViewChild, ElementRef } from "@angular/core";

@Component({
  selector: "app-demo",
  templateUrl: "./demo.component.html",
})
export class DemoComponent implements AfterViewInit {
  @ViewChild("heading") heading!: ElementRef;

  ngAfterViewInit() {
    console.log("ngAfterViewInit called");
    console.log("Heading Element:", this.heading.nativeElement);

    this.heading.nativeElement.style.color = "blue";
  }
}
```

What happens?

- You can now read or modify DOM safely.
- ViewChild has a value ONLY after this hook.

---

#### Example 2 — Accessing Child Component Methods

**child.component.ts**

```ts
@Component({
  selector: "app-child",
  template: `<p>Child Component Loaded</p>`,
})
export class ChildComponent {
  sayHello() {
    console.log("Hello from Child Component");
  }
}
```

**parent.component.ts**

```ts
import { Component, AfterViewInit, ViewChild } from "@angular/core";
import { ChildComponent } from "../child/child.component";

@Component({
  selector: "app-parent",
  template: `<app-child></app-child>`,
})
export class ParentComponent implements AfterViewInit {
  @ViewChild(ChildComponent) child!: ChildComponent;

  ngAfterViewInit() {
    this.child.sayHello();
  }
}
```

✔ Parent can safely call child methods
✔ Parent → Child interaction must happen here

---

#### Example 3 — Using ViewChildren

**HTML**

```html
<li #items *ngFor="let item of numbers">{{ item }}</li>
```

**Component**

```ts
import {
  Component,
  AfterViewInit,
  QueryList,
  ViewChildren,
  ElementRef,
} from "@angular/core";

@Component({
  selector: "app-list",
  templateUrl: "./list.component.html",
})
export class ListComponent implements AfterViewInit {
  numbers = [10, 20, 30];

  @ViewChildren("items") items!: QueryList<ElementRef>;

  ngAfterViewInit() {
    this.items.forEach((el, i) => {
      el.nativeElement.style.background = "#f2f2f2";
      console.log(`Element ${i} initialized:`, el.nativeElement);
    });
  }
}
```

✔ Access multiple DOM nodes
✔ Runs once after all items render

---

---

## 🟦 **7. ngAfterViewChecked()**

`ngAfterViewChecked()` is a lifecycle hook in Angular that is called: After Angular has checked the component’s view and all child views.

This includes:

- The component’s template
- All child component templates
- Runs **after every change detection cycle**

---

### When Does It Run?

It runs:

- On initial render
- On every update (property change, event, async call, etc.)
- Many times during the lifecycle (frequently!)

---

### Important Notes

1. It can be triggered **multiple times per second** → use sparingly.
2. Never update data-bound properties here. Doing so causes an infinite loop:

   ```
   ExpressionChangedAfterItHasBeenCheckedError
   ```

3. Best used to:

   - Detect view changes
   - Read/update DOM elements (in a non-mutating way)
   - Integrate third-party UI libraries

---

### Examples

#### Ex 1: Basic Usage of `ngAfterViewChecked()`

```ts
import { Component, AfterViewChecked } from "@angular/core";

@Component({
  selector: "app-demo",
  template: `<h2>{{ title }}</h2>`,
})
export class DemoComponent implements AfterViewChecked {
  title = "Angular View Checked Demo";

  ngAfterViewChecked() {
    console.log("ngAfterViewChecked() - View was checked");
  }
}
```

**Output (in console)**

```
ngAfterViewChecked() - View was checked
ngAfterViewChecked() - View was checked
ngAfterViewChecked() - View was checked
```

This happens every time change detection runs.

---

#### Ex 2: Using `@ViewChild` to Inspect the DOM

```ts
import {
  Component,
  AfterViewChecked,
  ViewChild,
  ElementRef,
} from "@angular/core";

@Component({
  selector: "app-check-view",
  template: `
    <input #myInput [(ngModel)]="name" />
    <p>You typed: {{ name }}</p>
  `,
})
export class CheckViewComponent implements AfterViewChecked {
  @ViewChild("myInput") inputRef!: ElementRef;
  name = "";

  ngAfterViewChecked() {
    console.log("Input value:", this.inputRef.nativeElement.value);
  }
}
```

What this shows:

- You can safely access DOM values **after every view update**.
- No DOM data can be trusted before this hook.

---

#### Ex 3: Detecting Changes in Child Components

Parent Component:

```ts
@Component({
  selector: "app-parent",
  template: `
    <h3>Counter: {{ counter }}</h3>
    <app-child [value]="counter"></app-child>
    <button (click)="increment()">Increment</button>
  `,
})
export class ParentComponent {
  counter = 0;
  increment() {
    this.counter++;
  }
}
```

Child Component:

```ts
import { Component, Input, AfterViewChecked } from "@angular/core";

@Component({
  selector: "app-child",
  template: `<p>Child Value: {{ value }}</p>`,
})
export class ChildComponent implements AfterViewChecked {
  @Input() value!: number;

  ngAfterViewChecked() {
    console.log("Child view checked. value =", this.value);
  }
}
```

**Explanation:**
Every time the parent updates the value, Angular checks the child view → trigger `ngAfterViewChecked()`.

---

#### Ex 4: The Mistake That Causes Infinite Loops

❌ **This is wrong and causes errors:**

```ts
ngAfterViewChecked() {
  this.title = "New Title";  // ❌ BAD
}
```

This will cause:

```
ExpressionChangedAfterItHasBeenCheckedError
```

### Why?

Because `ngAfterViewChecked()` runs after Angular finishes checking the view.
Updating a bound property requires rechecking → infinite loop → error.

---

### Correct Pattern (Using setTimeout)

If you want to change DOM or data AFTER the cycle:

```ts
ngAfterViewChecked() {
  setTimeout(() => {
    this.title = 'Updated Title';  // ✔ SAFE
  });
}
```

Using `setTimeout()` defers the update to the next tick, preventing the infinite loop.

## 🟦 **8. ngOnDestroy()**

`ngOnDestroy()` is a lifecycle hook that Angular calls **just before a component, directive, or pipe is destroyed**.

This happens when:

- The user navigates away from the component
- The component is removed from DOM
- `*ngIf` condition turns false
- A parent component is destroyed

You use `ngOnDestroy()` to perform **cleanup** such as:

✔ Unsubscribing from Observables
✔ Clearing intervals, timeouts
✔ Removing event listeners
✔ Stopping timers
✔ Closing WebSocket connections
✔ Cancelling ongoing API calls

---

### Why is ngOnDestroy() Important?

Because components stay in memory until destroyed.

If you don’t do cleanup:

❌ You get **memory leaks**
❌ Background timers keep running
❌ WebSockets keep listening
❌ API subscriptions continue even after moving away

`ngOnDestroy()` prevents all these issues.

---

### Example

```ts
import { Component, OnDestroy } from "@angular/core";

@Component({
  selector: "app-timer",
  template: `<p>{{ seconds }}</p>`,
})
export class TimerComponent implements OnDestroy {
  seconds = 0;
  interval: any;

  constructor() {
    this.interval = setInterval(() => {
      this.seconds++;
    }, 1000);
  }

  ngOnDestroy() {
    console.log("Component destroyed.");
    clearInterval(this.interval);
  }
}
```

### 📝 **Key Takeaways**

| Concept          | Meaning                                        |
| ---------------- | ---------------------------------------------- |
| **Purpose**      | Cleanup before component destruction           |
| **When it runs** | Navigation, \*ngIf false, parent destroyed     |
| **Main usage**   | Unsubscribe, remove listeners, clear intervals |
| **Prevents**     | Memory leaks, unwanted background processes    |

---

---

## 🔄 **Lifecycle Hook Call Order (Complete)**

Typical order when component is created:

```
1. ngOnChanges()
2. ngOnInit()
3. ngDoCheck()
4. ngAfterContentInit()
5. ngAfterContentChecked()
6. ngAfterViewInit()
7. ngAfterViewChecked()
```

During updates (change detection):

```
1. ngOnChanges() (if @Input changed)
2. ngDoCheck()
3. ngAfterContentChecked()
4. ngAfterViewChecked()
```

Before removal:

```
ngOnDestroy()
```

---

## 🎯 Summary Table

| Hook                      | Frequency | Purpose                     |
| ------------------------- | --------- | --------------------------- |
| **ngOnChanges**           | Many      | Respond to @Input() changes |
| **ngOnInit**              | Once      | Initialization (API calls)  |
| **ngDoCheck**             | Many      | Custom change detection     |
| **ngAfterContentInit**    | Once      | After ng-content projection |
| **ngAfterContentChecked** | Many      | Check projected content     |
| **ngAfterViewInit**       | Once      | View initialization         |
| **ngAfterViewChecked**    | Many      | View update checks          |
| **ngOnDestroy**           | Once      | Cleanup                     |
