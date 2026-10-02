RxJs Interview Questions
========================

#. **Q: What is RxJs?**

    A: RxJs stands for Reactive Extenstions for Javascript. It's a reactive programmig library that Angular uses heavily for handling asynchronous data streams.
    Instead of dealing with callbacks and promises everywhere, Angular leverages RxJs to unify async workflows.

#. **Q: What are Observables and Observers?**

    A: An **Observable** is a function that produce a stream of data that can emit multiple values over time. 
    
    It can emit:

    - next → data values (one or many, synchronous or asynchronous)
    - error → an error if something goes wrong
    - complete → a completion signal (no more values will be sent)

    Observable is the producer of data (values come over time).
    
    .. code-block:: typescript

        import { Observable } from 'rxjs';

        const observable = new Observable(subscriber => {
        subscriber.next('Hello');
        subscriber.next('World');
        setTimeout(() => {
            subscriber.next("Third Value (after 2s)");
            subscriber.complete();
        }, 2000);
        });

    An **Observer** is an object that subscribes to an Observable to receive those emitted values.
    
    It has three optional methods:

    - next(value) → called each time a value is emitted
    - error(err) → called if the observable throws an error
    - complete() → called when the observable is done

    .. code-block:: typescript

        const observer = {
            next: value => console.log(value),
            error: err => console.error(err),
            complete: () => console.log('Done')
        };

    **Subscribe**: 

    - subscribe() is the bridge that connects an Observer to an Observable.
    - Without subscribe(), an Observable is just a definition (nothing runs).
    - When you call .subscribe(observer), the Observable starts producing values and delivers them to the Observer.        

    .. code-block:: typescript 
    
        observable.subscribe(observer);

    Output:    

    .. code-block:: bash

        Received: First Value
        Received: Second Value
        Received: Third Value (after 2s)
        Completed!

#. **Q: How to unsubscribe in RxJs?**

   A: Unsubscribing is crucial because Observables (like HTTP, router events, valueChanges, etc.) can keep emitting values and hold memory if not stopped

    #. The Subscription Object:

    - When you call .subscribe(), RxJS returns a Subscription object.
    - This object has an .unsubscribe() method that stops receiving further values.

    .. code-block:: typescript

        import { Component, OnInit, OnDestroy } from '@angular/core';
        import { interval, Subscription } from 'rxjs';

        @Component({
            selector: 'app-demo',
            template: `<p>Check console for interval values</p>`
        })
        export class DemoComponent implements OnInit, OnDestroy {
            private subscription!: Subscription;

            ngOnInit() {
                const source$ = interval(1000); // emits every 1s
                this.subscription = source$.subscribe(value => {
                console.log('Interval:', value);
                });
            }

            ngOnDestroy() {
                // Important: Clean up
                // Prevents memory leaks when the component is destroyed.
                this.subscription.unsubscribe();
                console.log('Unsubscribed');
            }
        }

    #. Unsubscribing from Multiple Observables

    - If you have multiple subscriptions, you can: Use Subscription.add()

    .. code-block:: typescript

        this.subs = new Subscription();
        this.subs.add(obs1.subscribe(...));
        this.subs.add(obs2.subscribe(...));
        ...
        ngOnDestroy() {
            this.subs.unsubscribe(); // clears all
        }

    #. takeUntil Operator (Recommended)

    - Instead of manually unsubscribing everywhere, you can use takeUntil() with a Subject to auto-unsubscribe.

    .. code-block:: typescript

        import { Subject } from 'rxjs';
        import { takeUntil } from 'rxjs/operators';

        export class DemoComponent implements OnInit, OnDestroy {
            private destroy$ = new Subject<void>();

            ngOnInit() {
                someObservable$
                .pipe(takeUntil(this.destroy$))
                .subscribe(value => console.log(value));
            }

            ngOnDestroy() {
                this.destroy$.next(); // triggers completion
                this.destroy$.complete(); // optional cleanup
            }
        }

    #. Async Pipe (Best Practice in Templates)
    
    - If you’re just displaying observable values in a template, use Angular’s async pipe — it automatically subscribes/unsubscribes for you.    

    .. code-block:: html
        
        <!-- html -->
        <p>{{ timer$ | async }}</p>

    .. code-block:: typescript
        
        // typescript
        timer$ = interval(1000); // No need to unsubscribe manually

#. **Q: Explain the concepts of RxJs operators in Angular**

   A: In Angular, **RxJS operators** are the *real power tools* that let you transform, filter, and combine **Observables**. Let’s go step by step:

    1. What are RxJS Operators?

       * An **operator** is simply a **function** that takes an **Observable** as input and returns a **new Observable** as output.
       * Operators let you **transform** the data stream, **filter** values, **combine** multiple streams, or **handle errors**.
       * They are **pure functions** → they don’t modify the original Observable, but create a new one.

       👉 Think of operators as **pipes in plumbing**:

       * Water (data) flows through the pipe (Observable).
       * Along the way, filters, transformers, or splitters (operators) change the water (data stream).

    2. Types of Operators

        Operators in RxJS are grouped into categories:

        1. **Creation operators** → Create new observables

           * `of`, `from`, `interval`, `timer`

        2. **Transformation operators** → Change the data

           * `map`, `mergeMap`, `switchMap`, `concatMap`
        
        3. **Filtering operators** → Allow/deny certain values

           * `filter`, `take`, `debounceTime`, `distinctUntilChanged`
  
        4. **Combination operators** → Merge multiple streams

           * `merge`, `combineLatest`, `forkJoin`, `concat`
        
        5. **Error handling operators**

           * `catchError`, `retry`, `retryWhen`
        
        6. **Utility operators**

           * `tap`, `finalize`, `delay`

    3. How Operators Work (Pipeable Operators)

       - In modern RxJS (and Angular), operators are **pipeable** → used inside `.pipe()`.

       👉 Example (transform + filter):

       .. code-block:: typescript

           import { of } from 'rxjs';
           import { map, filter } from 'rxjs/operators';

           of(1, 2, 3, 4, 5)
           .pipe(
               filter(num => num % 2 === 0), // allow only even numbers
               map(num => num * 10)         // multiply by 10
           )
           .subscribe(result => console.log(result));

       Output:

       .. code-block:: bash

           20
           40

    4. RxJS Operators in Angular (Practical Use Cases)

        Example 1: HTTP Request Transformation

        .. code-block:: typescript

            this.http.get<User[]>('/api/users')
            .pipe(
                map(users => users.filter(user => user.active)), // only active users
                tap(activeUsers => console.log('Active Users:', activeUsers)) // log for debugging
            )
            .subscribe(data => this.users = data);

        Example 2: Handling Search with `debounceTime`

        - Prevents making an HTTP call on every keystroke.

        .. code-block:: typescript
            
            this.searchForm.get('query')?.valueChanges
            .pipe(
                debounceTime(300),              // wait for typing to pause
                distinctUntilChanged(),          // ignore duplicate values
                switchMap(query => this.http.get(`/api/search?q=${query}`))
            )
            .subscribe(results => this.searchResults = results);

        Example 3: Combining Streams

        .. code-block:: typescript

            combineLatest([
                this.authService.user$,
                this.settingsService.theme$
            ]).subscribe(([user, theme]) => {
                console.log('User:', user, 'Theme:', theme);
            });


    🔑 Summary

    * **RxJS Operators** = functions to **manipulate observable streams**.
    * They are used inside `.pipe()` in Angular.
    * Categories: Creation, Transformation, Filtering, Combination, Error handling, Utility.
    * Angular heavily uses operators in **HTTP calls, Reactive Forms, Routing events, async data flows**.

#.  **Q: Which are the operators you used in Angular from RxJS?**

    A: **cheat sheet of the Top 10 Most Common RxJS Operators** you’ll use in Angular apps, with short explanations and code examples.

    #. ``map`` → Transform each emitted value

        .. code-block:: typescript

            this.http.get<User[]>('/api/users')
            .pipe(
                map(users => users.map(u => u.name)) // extract just names
            )
            .subscribe(names => console.log(names));


    #. ``filter`` → Allow only certain values

        .. code-block:: typescript

            of(1, 2, 3, 4, 5)
            .pipe(filter(num => num % 2 === 0))
            .subscribe(val => console.log(val)); // 2, 4

    #. ``tap`` → Side effects (debugging/logging)

        .. code-block:: typescript

            this.http.get('/api/data')
            .pipe(
                tap(() => console.log('HTTP Request Sent'))
            )
            .subscribe(data => console.log(data));

    #. ``switchMap`` → Cancel previous observable, switch to new one (useful for HTTP search)

        .. code-block:: typescript

            this.search.valueChanges
            .pipe(
                debounceTime(300),
                distinctUntilChanged(),
                switchMap(query => this.http.get(`/api/search?q=${query}`))
            )
            .subscribe(results => this.results = results);


    #. ``mergeMap`` → Flatten observables (all inner subscriptions run in parallel)

        .. code-block:: typescript

            from([1, 2, 3])
            .pipe(
                mergeMap(id => this.http.get(`/api/user/${id}`))
            )
            .subscribe(user => console.log(user));

    #. ``concatMap`` → Flatten observables (queue, one after another)

        .. code-block:: typescript

            from([1, 2, 3])
            .pipe(
                concatMap(id => this.http.get(`/api/user/${id}`))
            )
            .subscribe(user => console.log(user));

    #. ``debounceTime`` → Wait before emitting (useful in forms/search)

        .. code-block:: typescript

            this.form.get('email')?.valueChanges
            .pipe(debounceTime(500))
            .subscribe(value => console.log('Typed:', value));

    #. ``distinctUntilChanged`` → Ignore duplicate values

        .. code-block:: typescript

            of(1, 1, 2, 2, 3)
            .pipe(distinctUntilChanged())
            .subscribe(val => console.log(val)); // 1, 2, 3

    #. ``take`` / ``takeUntil`` → Limit or auto-unsubscribe

        .. code-block:: typescript

            interval(1000)
            .pipe(take(3))
            .subscribe(val => console.log(val)); // 0, 1, 2

        With Angular component cleanup:

        .. code-block:: typescript

            private destroy$ = new Subject<void>();

            interval(1000)
            .pipe(takeUntil(this.destroy$))
            .subscribe(val => console.log(val));

            ngOnDestroy() {
            this.destroy$.next();
            this.destroy$.complete();
            }

    #.  ``catchError`` → Handle errors gracefully

        .. code-block:: typescript

            this.http.get('/api/maybe-fails')
            .pipe(
                catchError(err => {
                console.error('Error:', err);
                return of([]); // return fallback observable
                })
            )
            .subscribe(data => console.log(data));

    🔑 Quick Reference

    +-----------------------+--------------------------------------------------------+
    | Operator              | Use Case in Angular                                    |
    +=======================+========================================================+
    | ``map``               | Transform data (e.g., extract fields from HTTP result) |
    +-----------------------+--------------------------------------------------------+
    | ``filter``            | Ignore unwanted values                                 |
    +-----------------------+--------------------------------------------------------+
    | ``tap``               | Debugging/logging side effects                         |
    +-----------------------+--------------------------------------------------------+
    | ``switchMap``         | Live search, cancel previous requests                  |
    +-----------------------+--------------------------------------------------------+
    | ``mergeMap``          | Parallel HTTP requests                                 |
    +-----------------------+--------------------------------------------------------+
    | ``concatMap``         | Sequential HTTP requests                               |
    +-----------------------+--------------------------------------------------------+
    | ``debounceTime``      | Reduce frequency of events (forms, search)             |
    +-----------------------+--------------------------------------------------------+
    | ``distinctUntilChanged`` | Ignore duplicate values (form inputs, router params)|
    +-----------------------+--------------------------------------------------------+
    | ``takeUntil``         | Auto-unsubscribe on component destroy                  |
    +-----------------------+--------------------------------------------------------+
    | ``catchError``        | Handle API or stream errors                            |
    +-----------------------+--------------------------------------------------------+



#.  **Q: How to install RxJs?**

    A: Angular already includes **RxJS** by default because the framework itself depends on it.

    When you create a new Angular app with the CLI (``ng new my-app``), you’ll see ``rxjs`` listed in ``package.json``.

    👉 If you want to update or reinstall it:

    .. code-block:: bash

        npm install rxjs

    👉 To install a specific version (e.g., RxJS 7.8.1, which Angular 17 uses):

    .. code-block:: bash

        npm install rxjs@7.8.1

#.  **Q: What is a Callback in JavaScript?**

    A: A callback is a function that you pass as an argument to another function, so that it can be executed later (after some task is completed).

    **In short:** *“Call me back when you’re done.”*

    #. Simple Example (Synchronous Callback)

        .. code-block:: javascript

            function greet(name, callback) {
                console.log("Hello " + name);
                callback(); // call the function passed in
            }

            function sayBye() {
                console.log("Goodbye!");
            }

            // Passing sayBye as a callback
            greet("Alice", sayBye);

        **Output:**

        .. code-block:: text

            Hello Alice
            Goodbye!

        Here, ``sayBye`` is the callback function.

    #. Asynchronous Callback (Real Use Case: setTimeout)

        .. code-block:: javascript

            console.log("Start");

            setTimeout(() => {
                console.log("This runs later (async)");
            }, 2000);

            console.log("End");

        **Output:**

        .. code-block:: text

            Start
            End
            This runs later (async)

        Here, the arrow function inside ``setTimeout`` is a callback executed after 2 seconds.

    #. Why Do We Need Callbacks?

        **Event handling** – Reacting to user actions.

        .. code-block:: javascript

            document.getElementById("btn").addEventListener("click", () => {
                console.log("Button clicked!");
            });

        *(Here the function is a callback for the click event.)*

        **Async operations** – Handling results after time-consuming tasks like API calls, file reads, or timers.

    #. Callback Hell (Problem with Callbacks)

        When you nest too many callbacks, code becomes hard to read and maintain:

        .. code-block:: javascript

            getUser(1, user => {
                getOrders(user.id, orders => {
                getOrderDetails(orders[0], details => {
                    console.log(details);
                });
                });
            });

        This deeply nested style is called *callback hell*.

    #. Summary

      - A callback is a function passed into another function to be executed later.
      - Used in both synchronous (function execution order) and asynchronous (API calls, events, timers) contexts.
      - They can lead to callback hell, which is why **Promises** and **async/await** were introduced to make async code cleaner.

#. **Q: What is Promises in Javascript?**

    A: Promises and async/await in JavaScript

    1. Promises in JavaScript

        A **Promise** represents a value that may be available **now, later, or never**.  
        It has 3 states:

        * **Pending** → initial state (waiting)
        * **Fulfilled** → completed successfully (resolved)
        * **Rejected** → failed (error)

        Example: Promise

        .. code-block:: javascript

            function fetchData() {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                const success = true;
                if (success) {
                    resolve("Data fetched!");
                } else {
                    reject("Error fetching data");
                }
                }, 2000);
            });
            }

            // Using the promise
            fetchData()
            .then(result => console.log(result))   // "Data fetched!"
            .catch(error => console.error(error)); // If rejected

        👉 ``.then()`` handles success, ``.catch()`` handles errors.

    2. async/await in JavaScript

        ``async/await`` is **syntactic sugar** built on top of Promises.  
        It allows you to write asynchronous code that **looks synchronous**, making it easier to read.

        * An ``async`` function **always returns a Promise**.
        * Inside it, ``await`` pauses execution until the Promise is resolved or rejected.

        Example: async/await
        ~~~~~~~~~~~~~~~~~~~~

        .. code-block:: javascript

            function fetchData() {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                const success = true;
                if (success) {
                    resolve("Data fetched!");
                } else {
                    reject("Error fetching data");
                }
                }, 2000);
            });
            }

            async function getData() {
            try {
                const result = await fetchData(); // wait until resolved
                console.log(result);              // "Data fetched!"
            } catch (error) {
                console.error(error);             // handles rejection
            }
            }

            getData();

    3. Real Use Case: API Call with fetch

        #. Using Promise

            .. code-block:: javascript

                fetch("https://jsonplaceholder.typicode.com/posts/1")
                .then(response => response.json())
                .then(data => console.log(data))
                .catch(err => console.error(err));

        #. Using async/await

            .. code-block:: javascript

                async function getPost() {
                    try {
                        const response = await fetch("https://jsonplaceholder.typicode.com/posts/1");
                        const data = await response.json();
                        console.log(data);
                    } catch (err) {
                        console.error(err);
                    }
                }

                getPost();

            👉 Same thing, but ``async/await`` looks cleaner and easier to follow.

    #. Promises vs async/await

        +---------------------+-------------------------------+-------------------------------------+
        | Feature             | **Promise**                   | **async/await**                     |
        +=====================+===============================+=====================================+
        | Syntax              | ``.then()``, ``.catch()``     | Looks synchronous with ``await``    |
        +---------------------+-------------------------------+-------------------------------------+
        | Readability         | Can get messy with chains     | More readable, structured           |
        +---------------------+-------------------------------+-------------------------------------+
        | Error handling      | ``.catch()``                  | ``try...catch``                     |
        +---------------------+-------------------------------+-------------------------------------+
        | Parallel execution  | Easy with ``Promise.all()``   | ``await Promise.all()`` also works  |
        +---------------------+-------------------------------+-------------------------------------+

    #. Summary

       * **Promises** handle async results using ``.then()`` and ``.catch()``.
       * **async/await** is a cleaner way to work with Promises.
       * Both solve **callback hell** and make async programming manageable.


#.  **Q: Difference between Callbacks, Promises and Observables of RxJs?**

    A:

    .. list-table:: Callbacks vs Promises vs Observables
        :header-rows: 1
        :widths: 20 25 25 30

        * - Feature
          - **Callbacks**
          - **Promises**
          - **Observables (RxJS)**
        * - Definition
          - Function passed into another function to be executed later.
          - Represents a single future value (success or failure).
          - Represents a stream of values (0, 1, many, or infinite).
        * - Execution
          - Manual invocation inside the function.
          - Eager: starts immediately when created.
          - Lazy: nothing happens until ``subscribe()`` is called.
        * - Number of Values
          - Many, but requires nesting.
          - Only one value (or error).
          - Multiple values over time.
        * - Cancellation
          - Hard to manage.
          - Not cancellable once started.
          - Cancellable via ``unsubscribe()``.
        * - Error Handling
          - Must be handled manually in callback logic.
          - Handled via ``.catch()``.
          - Handled via ``error`` callback or operators.
        * - Readability
          - Can lead to "callback hell" with nested functions.
          - Cleaner than callbacks, but still chained.
          - More powerful but requires learning operators.
        * - Use Cases
          - Event listeners, timers, simple async tasks.
          - Single async operation like API request, file read.
          - Streams of events, live updates, WebSockets, user input streams.


#.  **Q: What is Push/Reactive vs Pull/Imperative?**

    A: Push (Reactive) vs Pull (Imperative)

    1. Pull / Imperative Model

        👉 **You (the consumer) ask for the data when you need it.**  
        The **consumer controls** when and how values are received.

        - The **producer is passive** and only provides a value when requested.
        - The **consumer pulls** values.

        **Examples**

        .. code-block:: js

            // Function example
            function getValue() {
                return 42;
            }

            const value = getValue(); // You "pull" the value
            console.log(value);       // 42

        .. code-block:: js

            // Iterator example
            function* numbers() {
                yield 1;
                yield 2;
                yield 3;
            }

            const it = numbers();
            console.log(it.next().value); // 1
            console.log(it.next().value); // 2


    2. Push / Reactive Model

        👉 **The producer sends data whenever it’s ready.**  
        The **producer controls** when and how values are delivered.

        - The **consumer subscribes** and waits.
        - The **producer pushes** values to the consumer.

        **Examples**

        .. code-block:: js

            // Callback example
            function fetchData(callback) {
                setTimeout(() => {
                callback("Data ready!");
                }, 1000);
            }

            fetchData(data => console.log(data)); // "Data ready!" is pushed

        .. code-block:: js

            // Promise example
            fetch("https://jsonplaceholder.typicode.com/posts/1")
                .then(res => res.json())
                .then(data => console.log(data));

        .. code-block:: js

            // Observable (RxJS) example
            import { interval } from 'rxjs';

            const obs$ = interval(1000); // emits 0,1,2,3...
            const subscription = obs$.subscribe(val => console.log(val));

            // Later you can stop it
            setTimeout(() => subscription.unsubscribe(), 5000);


    3. Key Difference (Summary)

    A:
    
    .. list-table:: Push vs Pull
        :header-rows: 1
        :widths: 25 35 35

        * - Aspect
          - **Pull / Imperative**
          - **Push / Reactive**
        * - Who controls flow?
          - Consumer asks for data
          - Producer sends data when ready
        * - Consumer’s role
          - Actively requests values
          - Subscribes and waits for values
        * - Producer’s role
          - Passive (waits for request)
          - Active (decides when to emit)
        * - Examples
          - Functions, Iterators
          - Callbacks, Promises, Observables
        * - Number of values
          - Usually one at a time
          - One, many, or infinite over time

    4. Summary

       - **Pull** = Consumer is in charge → "Give me data when I ask."
       - **Push** = Producer is in charge → "Here’s data whenever I have it."




