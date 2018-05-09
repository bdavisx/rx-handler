# rx-handler

```bash
npm i rx-handler
```

## Hello World

[Hello World Demo](https://stackblitz.com/edit/typescript-bw3o98?file=index.ts)

```js
import { from } from "rxjs"
import { handler } from "rx-handler"

const button = document.querySelector("button")

const onClick = handler()
button.addEventListener("click", onClick)

from(onClick).subscribe(event => console.log(event))
```

## With Operators

[With Operators Demo](https://stackblitz.com/edit/typescript-wxvwhg?file=index.ts)

```js
import { from } from "rxjs"
import { delay, pluck, withLatestFrom } from "rxjs/operators"
import { handler } from "rx-handler"

const $ = document.querySelector.bind(document)

const input = $("input")
const button = $("button")

const onInput = handler(pluck("target", "value"), delay(250))
input.addEventListener("input", onInput)

const onClick = handler(withLatestFrom(onInput, (click, text) => text))
button.addEventListener("click", onClick)

from(onInput).subscribe(text => ($(".output").innerText = text))
from(onClick).subscribe(event => console.log(event))
```

## Angular Hello World

[Angular Hello World Demo](https://stackblitz.com/edit/angular-a8kydb?file=src/app/app.component.ts)

```js
import { Component } from "@angular/core"
import { handler } from "rx-handler"
import { from } from "rxjs"
import { map } from "rxjs/operators"

@Component({
  selector: "my-app",
  template: `<div>
  <button (click)="onClick()">Click me</button>
  {{date$ | async}}
  </div>`
})
export class AppComponent {
  onClick = handler()
  date$ = from(this.onClick).pipe(map(() => Math.random()))
}
```

## Angular Multiple Handlers

[Angular Multiple Handlers Demo](https://stackblitz.com/edit/angular-chybye?file=src%2Fapp%2Fapp.component.ts)

```js
import { Component } from "@angular/core"
import { handler } from "rx-handler"
import { combineLatest, from, merge } from "rxjs"
import {
  delay,
  mapTo,
  pluck,
  scan,
  startWith,
  withLatestFrom
} from "rxjs/operators"

@Component({
  selector: "my-app",
  template: `<div>
  
  <button (click)="onInc()">+</button>
  <button (click)="onDec()">-</button>
  <div>{{count$ | async}}</div>

  <input placeholder="Type something" (input)="onInput($event)"/>
  <div>{{text$ | async}}</div>
  
  <h2>{{slicedText$ | async}}</h2>
  </div>`
})
export class AppComponent {
  onInc = handler(mapTo(1))
  onDec = handler(mapTo(-1))

  count$ = merge(this.onInc, this.onDec).pipe(
    startWith(0),
    scan((count, curr) => count + curr)
  )

  onInput = handler(pluck("target", "value"), delay(500))

  text$ = from(this.onInput)

  slicedText$ = combineLatest(this.count$, this.text$, (count, text: string) =>
    text.substring(0, count)
  )
}
```

### Special Thanks to [Nicholas Jamieson](https://github.com/cartant)

He took my idea which used originally used Proxies and [vastly improved it](https://github.com/johnlindquist/react-streams/issues/6#issuecomment-380665956).
