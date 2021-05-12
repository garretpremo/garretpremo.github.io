---
theme: jekyll-theme-tactile
layout: post
title: RxJS Observed Decorator
published: true
---

## A Simple `Subject` State Management Utility

If you're using `Subject` or `BehaviorSubject` state management in Angular and you're anything like me, you've probably written something like this at some point:

```typescript
export class UserService {
    
    private usersSubject = new BehaviorSubject<User[]>(null);
    readonly users$ = this.usersSubject.asObservable();

    set users(users: User[]) {
        this.usersSubject.next(users);
    }

    get users(): User[] {
        return this.usersSubject.getValue();
    }
}
```

Needless to say, this is quite a lot of boilerplate code for setting up a property the way I wanted it. I wrote this exact sequence so many times to the point where I made myself a live-template in intelliJ, and a VSCode snippet that auto-generated this block with a variable name.

I digress. I saw room for improvement, and thus the `Observed` decorator was born.

#### With this decorator, the above code block could be written in two lines:

```typescript
export class UserService {
    
    @Observed() users: User[] = null;
    readonly users$: Observable<User[]>;
}
```

Multiple state managed variables become very easy to read.

```typescript
export class UserService {
    
    @Observed() users: User[] = null;
    @Observed() selectedUser: User = null;
    @Observed() filter: string = null;

    readonly users$: Observable<User[]>;
    readonly selectedUser$: Observable<User>;
    readonly filter$: Observable<string>;
}
```

#### How does it work?

`@Observed()` will create a `BehvaiorSubject` behind the scenes, and whenever you change the value of `users`, you're effectively calling `userSubject.next();`

It will also generate an observable for you, and assigned to a variable named as your `@Observed()` variable name with a `$` as the suffix.

For example: 

```typescript
@Observed() prop = '';

// this will generate an observable and assign it to 'prop$'
```

#### Using Subject/ReplaySubject

`@Observed()` will use a `BehaviorSubject` under the hood by default, but you can customize it by passing an option into the decorator

```typescript
@Observed({ type: 'subject' })
@Observed({ type: 'replay', replayOptions: {} })
@Observed({ type: 'behavior' }) // default
```

For details on `replayOption`, see the repository or see [RxJS ReplaySubject](https://rxjs-dev.firebaseapp.com/api/index/class/ReplaySubject)

#### Installation/Source Code

For installation details, [see here](https://www.npmjs.com/package/rxjs-observed-decorator). Should be as simple as:
```
npm i rxjs-observed-decorator
```

For the github repo, [see here](https://github.com/garretpremo/rxjs-observed-decorator#readme)
