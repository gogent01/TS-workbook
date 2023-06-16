# TS-workbook

This workbook focuses on different complex aspects of Typescript language.

## Table of Contents

- [Mixins](#mixins)

## Mixins

Mixins allow to add some common loosely coupled functionality to any classes. In Typescript mixins can be implemented as independent class extenders or constrained extenders, that can be applied only on classes with specific interfaces. In either case, use of a mixin produces a new subclass of an original class with traits of a mixin. The only problem with mixins is that they cannot produce private or protected properties (but can use ES2020 private fields).

### Unconstrained Mixins

First, make a type for any class. Second, write a mixin function. Third, use it.
```
type Constructor = new (...args: any[]) => {};

function InjectHelp<TBase extends Constructor>(Base: TBase, helpText: string) => {
  return class Help extends Base {
    _helpText: string = helpText;
    
    get helpText() {
      return this._helpText;
    }
    
    set helpText(helpText: string) {
      this._helpText = helpText;
    }
    
    showHelp(): void {
      console.log(this._helpText);
    }
  }
}

class Button {
  text: string;
  
  constructor(text: string) {
    this.text = text;
  }
  
  click(): void {
    console.log(`Button "${this.text}" clicked!`);
  }
}

const ButtonWithHelp = InjectHelp(Button, 'Click a button to see what it does!');
const intriguingButton = new ButtonWithHelp('What do I do?');
intriguingButton.showHelp(); // 'Click a button to see what it does!'
intriguingButton.click(); // Button "What do I do?" clicked!
```

### Constrained Mixins

Constrained mixins elaborate interfaces of classes (constructor functions) they can be applied to. In order to use constrained mixins one should make a Contructor type to accept a generic argument and create derivative types from it. Then a mixin definition should use one of the derived types.
```
type GConstructor<T = {}> = new (...args: any[]) => T;
type Positionable = GConstructor<{ x: number; y: number }>;
function InjectRandomlyPlacible<TBase extends Positionable>(Base: TBase) {
  return class RandomlyPlacible extends Base {
    placeRandomly(xmax: number, ymax: number): void {
      this.x = Math.floor(Math.random() * (xmax + 1));
      this.y = Math.floor(Math.random() * (ymax + 1));
    }
  }
}

class Shape {
  x: number;
  y: number;
  
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}

class Square extends Shape {
  width: number;
  
  constructor(x: number, y: number, width: number) {
    super(x, y);
    this.width = width;
  }
}

class Canvas {
  width: number;
  height: number;
  color?: string;
  shapes: Shape[];
  
  constructor(width: number, height: number, color?: string) {
    this.width = width;
    this.height = height;
    this.color = color;
    this.shapes = [];
  }
  
  add(shape: Shape): void {
    this.shapes.push(shape);
  }
}

const RandomSquare = InjectRandomlyPlacible(Square);

const canvas = new Canvas(1024, 768);
const square = new RandomSquare(0, 0, 40);
square.placeRandomly(canvas.width, canvas.height);
canvas.add(square);
```


