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
    _helpText = helpText;
    
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

