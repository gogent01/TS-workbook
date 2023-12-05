# TS-workbook

This workbook focuses on different complex aspects of Typescript language.

## Table of Contents

- [Mixins](#mixins)

## Mixins

Mixins in TypeScript enable the incorporation of loosely coupled, shared functionality into classes. Mixins can be written as standalone class extenders or restricted extenders, applicable only to classes with specific interfaces. In both scenarios, applying a mixin results in the creation of a new subclass that inherits traits from both the original class and the mixin. The only important limitations is that they cannot introduce private or protected properties (they can leverage ES2020 private fields for this purpose, but they are not handled by TypeScript).

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

Constrained mixins specifically define the interfaces of classes (constructor functions) to which they can be applied. To employ constrained mixins, it's necessary to create a Constructor type that accepts a generic argument, and subsequently generate derivative types based on it. The mixin definition can then utilize one of these derived types, establishing the constraints for its application.
```
type Constructor<T = {}> = new (...args: any[]) => T;
type Positionable = Constructor<{ x: number; y: number }>;
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


