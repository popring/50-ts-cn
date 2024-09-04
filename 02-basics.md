

# 2. TypeScript 的基本错误

**本章内容**

- 过于频繁地使用 `any`，忽视编译器警告
- 不使用严格模式，变量使用不当，误用可选链
- 过度使用空值合并运算符（nullish coalescing）
- 错误使用模块导出和类型不当
- 混淆 `==` 和 `===`
- 忽视类型推断

“你知道，开始是任何工作的最重要部分。”柏拉图说。我补充道：“尤其是在学习 TypeScript 的情况下”。当许多人以错误的方式学习基础知识（不仅仅是 TypeScript），比从一开始就正确学习要难得多。例如，高山滑雪（也称为下坡滑雪，不要和越野滑雪混淆）很难学得正确。然而，随意滑雪很容易。事实上，滑雪比单板滑雪更容易，因为你可以使用两块滑雪板（滑雪板），而不是一块（单板滑雪）。在滑雪中，像角度调整（倾斜身体并将膝盖和臀部调整到转弯方向）这样的技术并不容易掌握。我见过许多人滑雪多年而技术不正确，这导致了受伤、疲劳和控制能力下降的风险。我们可以将这种隐喻扩展到 TypeScript 上。那些忽视基础的开发者会感到更多的挫折（这不是科学的说法，只是我的观察）。顺便问一下，为什么 JavaScript 文件和 TypeScript 文件分手了？因为它不能应对“类型”的承诺。说到承诺，我们需要它来完成这本书。是的，基础知识很少是标题中承诺的严重错误，但我保证我们会逐步解决它们！

## 2.1. 过于频繁地使用 `any`

TypeScript 相对于 JavaScript 的主要优势在于其强大的静态类型系统，这使得开发者能够在开发过程中捕获与类型相关的错误。然而，开发者常犯的一个错误是过于频繁地使用 `any` 类型。本节将讨论为什么过度依赖 `any` 类型是有问题的，并提供更有效处理动态类型的替代解决方案。顺便提一下，JavaScript 变量曾经对 TypeScript 变量说：“我可以成为任何我想成为的东西！” ;-)

在 TypeScript 中，`any` 类型允许变量是任何 JavaScript 类型，这实际上绕过了 TypeScript 的类型检查器。这基本上是 JavaScript 所做的——允许变量是任何类型，并且在运行时可以更改类型。甚至有人说，JavaScript 中的变量没有类型，但它们的值有类型。虽然在特殊情况下这可能看起来很方便，但它会导致如下问题：

- **类型安全性较弱**：使用 `any` 会减少 TypeScript 类型系统的好处，因为它禁用了对变量的类型检查。这可能导致未被注意的运行时错误，从而使使用 TypeScript 的目的落空。

- **代码可维护性降低**：当过度使用 `any` 时，开发者很难理解代码的预期行为，因为类型信息缺失或不明确。

- **失去自动补全和重构支持**：TypeScript 的智能自动补全和重构支持依赖于准确的类型信息。使用 `any` 会使开发者失去这些有用的功能，增加在代码变更过程中引入错误的机会。

让我们考虑几个 TypeScript 代码示例，说明 `any` 的使用及其潜在的缺点：使用 `any` 作为函数参数、变量和数组中的元素：

```typescript
function logInput(input: any) {      // #A
  console.log(`Received input: ${input}`);
}

logInput("Hello");                  // #B
logInput(42);
logInput({ key: "value" });

let data: any = "This is a string"; // #C
data = 100;                         // #D

let mixedArray: any[] = [           // #E
  100, 
  true, 
  { key: "value" }
]; 
mixedArray.push("42"); 
mixedArray[mixedArray.length-1]+=1  // #F
```

`#A 使用 any 作为函数参数`  

`#B 无类型检查，允许任何值`  

`#C 使用 any 作为变量`  

`#D 无类型检查，我们可以将任何值赋给 data`  

`#E 使用 any 在数组中`  

`#F 没有类型检查，42+1 变成了 421 而不是 43`

这怎么会发生呢？思路可能是这样的：我有一些代码，但有个错误。我该怎么做？

```typescript
let data = "This is a string";
data = 100;       // #A
```

`#A 类型 '100' 不能分配给类型 'string'。`

为了修复这个问题，使用 `any` 是很诱人的：

```typescript
let data: any = "This is a string";
data = 100;
```

实际上，错误是否消失了？虽然 TypeScript 的错误消失了，但只有通过仔细检查代码或运行（并发现错误）才能识别的真正错误依然存在！因此，`any` *很少* 是一个好的解决方案。

在这些示例中，我们为函数参数、变量和数组使用了 `any`。虽然这允许我们处理任何类型的数据而不进行类型检查，但也引入了运行时错误的风险，因为在这些情况下 TypeScript 无法提供类型安全性或错误检测。

为了提高类型安全性，考虑使用具体类型或泛型代替 `any`。

当我们为函数参数使用具体类型时，即使在运行代码之前，也能清楚地知道哪一行是不兼容的。假设我们要除以 10，因此参数必须是数字。在这种情况下，传递一个 "Hello" 字符串不是一个好主意，因为它不能赋值给数字类型：

```typescript
function logInput(input: number) {
  console.log(`Received input: ${input}, divided by 10 is ${input/10}`);
}
logInput("Hello");    // #A
logInput(42);

```

`#A 错误：类型 '"Hello"' 的参数不能分配给类型 'number'`。

为变量使用具体类型也可以帮助我们避免调试时浪费时间。如果我们指定了字符串或数字的联合类型，那么其他任何类型都会引发 TypeScript 的警告：

```typescript
let data: string | number = "This is a string";
data = 100;         // #A
data = false;       // #B
```

`#A 可以：TypeScript 检查赋值的值是否为正确类型`  

`#B 错误：类型 'boolean' 不能分配给类型 'string | number'`

我们可以为数组元素创建一个特殊类型：

```typescript
type MixedArrayElement = boolean | number | object;
let mixedArray: MixedArrayElement[] = [
  100, 
  true, 
  { key: "value" }
];
mixedArray.push("42");      // #A
mixedArray.push(42)         // #B
```

`#A 错误：类型 '"42"' 的参数不能分配给类型 'MixedArrayElement'`  

`#B 可以`

正如你所见，通过避免使用 `any` 并使用具体类型或泛型，你可以利用 TypeScript 的类型检查和错误检测功能，使代码更加健壮和可维护。

开发者可以使用以下替代方案，而不是 resorting to `any` 类型：

- **类型注释**：尽可能为变量、函数参数或返回值显式指定类型。这使得 TypeScript 编译器能够在开发过程中早期捕获与类型相关的问题。

- **联合类型**：在变量可能有多种类型的情况下，使用联合类型（例如 `string | number`）来指定所有可能的类型。这提供了更好的类型安全性，同时仍然允许灵活性。

- **类型别名和接口**：如果你有一个在多个地方使用的复杂类型，创建一个类型别名（例如 `type TypeName`）或一个接口，使代码更具可读性和可维护性。稍后，我们将看到关于如何创建这两者的很多例子。

- **类型保护**：使用类型保护（例如 `typeof`、`instanceof` 或自定义类型保护函数）来在特定范围内缩小变量的类型，提高类型安全性，同时不失去灵活性。我们会在后面详细讨论类型保护。

- **Unknown 类型**：如果你确实不知道变量的类型，可以考虑使用 `unknown` 类型，而不是 `any`，或者省略类型引用，让 TypeScript 推断类型。`unknown` 类型在使用变量之前强制进行显式类型检查，从而减少运行时错误的可能性。

总的来说，虽然 `any` 类型因其灵活性而具有吸引力，但应尽可能避免使用，以最大化 TypeScript 类型系统的好处。通过使用类型注释、联合类型、类型别名、接口、类型保护、类型推断和 `unknown` 类型，开发者可以在处理动态类型的同时保持类型安全。

## 2.2. 忽视编译器警告

TypeScript 旨在通过提供有洞察力的错误消息和警告，帮助开发者尽早识别代码中的潜在问题。一般来说，它们之间的关键区别在于警告是非阻塞编译、建议性和可选的，而错误是阻塞编译和严重的。但有趣的是，TypeScript 可以“允许”某些错误。例如，这段代码将编译并运行输出 100，尽管有关于类型不匹配的错误消息“类型 '100' 不能分配给类型 'tring'”：

```typescript
let data = "This is a string";
data = 100;
console.log(data)
```

从这个意义上说，TypeScript 与其他编译语言（如 C++）非常不同，在 C++中，如果程序无法编译，你就无法运行它！原因是在 TypeScript 中，类型检查和编译/转译是独立的过程。因此，看到我们的编辑器中有类型不匹配的错误，但 *仍然* 能够生成 JavaScript 代码并运行它（冒着巨大的风险），这是一个悖论。

为了简单起见，我们将错误和警告视为一个类别，尽管可以配置不同的 TypeScript ESLint 规则为“错误”、“警告”或“关闭”。以下是一些 TypeScript 和 ESLint TypeScript 错误和警告的示例：

- 类型不匹配：将值赋给类型不同的变量。

- 未知标识符：使用之前未定义的变量。

- 缺少属性：省略接口所需的属性。

- 未使用的变量：声明从未使用的变量或导入。

- 三重等于双等于：建议使用 `===` 和 `!==` 而不是 `==` 和 `!=` 来避免类型强制转换。

- `const` 超过 `let`：对于从未重新分配的变量使用 `let`。

因此，TypeScript 开发者可以忽略一些 TS 错误，但忽略这些编译器和类型检查错误和警告可能导致微妙的错误、降低代码质量和运行时错误。忽视警告有点违背了 TypeScript 的好处。本节将讨论解决编译器警告的重要性，并建议有效管理和解决它们的策略。

回顾忽视编译器警告的后果是很好的，因为忽视编译器警告可能会导致各种问题，包括：

- 运行时错误：许多编译器警告表明可能在运行时导致意外行为或错误的潜在问题。忽视这些警告会增加在生产中遇到难以调试的问题的可能性。

- 代码可维护性：未解决的编译器警告会使其他开发者难以理解代码的意图或识别潜在问题，导致可维护性下降。

- 类型安全：TypeScript 的类型系统旨在捕获与类型相关的潜在问题。忽视与类型安全相关的警告可能导致类型相关的错误。

- 增加噪音：代码中存在未解决的警告会迅速滚雪球般地变成巨大的技术债务，这将用无用的噪音污染你的构建终端，因为没有对它们采取任何行动。

以下是 TypeScript 代码示例，说明忽视编译器警告的潜在问题，其中一些问题在严格模式开启时出现，而其他问题可以在 tsconfig.json（TypeScript）或.eslintrc.js（TypeScript ESLint）中配置：

示例 1：未使用的变量：声明从未使用的变量会导致不必要的代码和混乱。

```typescript
function add(x: number, y: number): number {
  const result = x + y;
  const unusedVar = "This variable is never used.";           // #A
  return result;
}
```

`#A 'unusedVar' 被声明但其值从未被读取。`

示例 2：未使用的函数参数：声明从未使用的函数参数可能表明函数实现不完整或不正确。

```typescript
function multiply(x: number, y: number, z: number): number {      // #A
  return x * y;
}
```

`#A 'z' 被声明但其值从未被读取。`

示例 3：隐式 `any`：使用隐式 `any` 类型会导致缺乏类型安全，使代码更难维护。

```typescript
function logData(data) {              // #A
  console.log(`Data: ${data}`);
}
```

`#A 参数 'data' 隐式具有 'any' 类型。`

示例 4：不兼容的类型：分配或传递不兼容类型的值可能导致意外行为和运行时错误。

```typescript
function concatStrings(a: string, b: string): string {
  return a + b;
}

const result = concatStrings("hello", 42);            // #A
```

`#A 参数类型 'number' 不能分配给参数类型 'tring'。`

示例 5：缺少返回：当返回类型不包括 `undefined` 时，未为所有情况提供返回语句可能导致隐式返回 `undefined`，进而导致类型不匹配。

```typescript
function noReturn(a: number): string {      // #A
  if (a) return a.toString()
  else {
    console.log('no input')
  }
}
```

`#A 函数缺少结束返回语句，并且返回类型不包括 'undefined'。`

在这些示例中，我们看到了 TypeScript 开发过程中可能出现的不同类型的编译器警告。

通过解决这些编译器警告，您可以提高 TypeScript 代码的质量、可维护性和可靠性。忽视编译器警告可能会导致意想不到的后果和未来更难调试的问题。最重要的是，存在未解决的警告会增加衰减并降低代码质量（参见破窗理论）。错误的噪音会隐藏真正重要的错误。此外，它增加了必须记住哪些错误是“预期”的和可以的，哪些不是的精神负担。

为了应对这些警告，让我们来看看一些管理和解决编译器警告的策略：

- 配置编译器：调整 TypeScript 编译器配置（tsconfig.json）以满足项目的需求。启用严格模式和其他与严格性相关的标志，以确保最大的类型安全并尽早捕获潜在问题。将无警告作为构建、预提交、预合并和预部署 CI/CD 检查的一部分。最初，这将阻止工作，因为需要处理警告，但最终质量会提高，速度也会提高。

- 将警告视为错误：配置 TypeScript 编译器和 ESLint 将所有警告（非阻塞）视为错误（阻塞），执行一项政策，即任何带有警告的代码都不应推送到存储库。这种方法确保在合并更改之前解决所有警告。

- 定期审查警告：如果您现在无法解决所有警告，至少定期审查和解决编译器警告，即使它们当时看起来并不关键。这种做法将有助于保持代码质量并减少技术债务。如果您当前有大量的警告积压，可以每周或每月举行 TS 警告“派对”（自带饮料），让工程师在通话中花费 1 2 小时来清理警告。

- 重构代码：在某些情况下，解决编译器警告可能需要重构代码。始终努力提高代码质量和结构，确保它符合最佳实践和设计模式。

- 教育团队：确保所有团队成员理解解决编译器警告的重要性，并熟悉 TypeScript 最佳实践。鼓励知识共享和同行评审，以确保整个团队意识到潜在问题以及如何解决它们。在代码审查中坚持不懈，通过教育和防范带有警告的代码。

TypeScript 中的编译器警告旨在帮助开发者在开发过程的早期识别潜在问题。忽视这些警告可能会导致运行时错误、可维护性下降和代码质量降低。通过正确配置编译器、将警告视为错误、定期审查警告、重构代码和教育团队，开发者可以有效地管理和解决编译器警告，从而导致更健壮和可靠的代码库……并希望减少被“寻呼机”唤醒的不眠之夜，同时在待命时尽力保持系统的运行。

## 2.3. 不使用严格模式

TypeScript 提供了一种严格模式，通过实施更严格的类型检查和其他约束来提高代码质量并在开发过程中捕获潜在问题。通过在 tsconfig（例如，在 tsconfig.json 配置文件中将 "strict" 设置为 true，如 "strict": true）中设置 "strict" 标志为 true 来启用此模式。不幸的是，一些开发者忽视了使用严格模式的好处，导致代码库不太健壮，增加了遇到运行时错误的机会。

使用严格模式的好处如下：

- 增强类型安全：严格模式实施更严格的类型检查，减少类型相关错误的可能性，使代码库更可靠。

- 提高代码可维护性：通过更严格的类型检查，代码变得更可预测和更容易理解，从而提高了可维护性并减少了技术债务。

- 改进自动补全和重构支持：严格模式可以改进 TypeScript 的高级自动补全和重构功能，使开发者更容易编写和修改代码。这是因为像 noImplicitAny 这样的规则强制提供类型，从而有助于自动补全。

- 减少潜在的运行时错误：严格模式引入的更严格检查有助于早期捕获潜在问题，减少在生产中遇到运行时错误的机会。

- 鼓励最佳实践：通过使用严格模式，开发者被鼓励采用最佳实践并编写更干净、更健壮的代码。

现在，这里是 TypeScript 代码示例，说明严格模式和非严格模式之间的区别：

非严格模式允许 `fn` 具有隐式的 `any` 类型：

```typescript
function functionFactory(fn) {      // #A
  fn(100)
}

functionFactory(console.log);
functionFactory(42);                // #B
```

`#A 没有 TS 错误，但参数 'fn' 隐式具有 'any' 类型，但从使用情况可以推断出更好的类型。`

`#B 运行时错误，调用了一个非函数。`

严格模式（通过在 tsconfig.json 文件中设置 "strict": true 启用）和 noImplicitAny 对隐式的 any 表示关注：

```typescript
function functionFactory(fn) {      // #A
  fn(100)
}

functionFactory(console.log);
functionFactory(42);
```
`#A 参数 'fn' 隐式具有 'any' 类型。`

这个错误促使开发者选择一个类型，从而有助于跟踪调用非函数时的运行时问题：

```typescript
function functionFactory(fn: Function) {
  fn(100)
}

functionFactory(console.log);
functionFactory(42);        // #A
```
`#A 参数类型 'number' 不能分配给参数类型 'Function'。`

这里有一个关于类 Person 的示例：

```typescript
class Person {
  name: string;
  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}
```

在非严格模式下，我们直到运行代码才会发现问题（如果我们足够细心发现这个错误的话！）：

```typescript
const person = new Person();
person.greet();     // #A
```

`#A 没有 TS 错误，但运行时不正常，输出为 Hello, my name is undefined。`

另一方面，在严格模式（通过在 tsconfig.json 文件中设置 "strict": true 启用）和 strictPropertyInitialization 下，我们可以发现有些不对劲：

```typescript
class PersonStrict {
  name: string;       // #A
  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}

const personStrict = new PersonStrict("Anastasia");
personStrict.greet();
```

`#A 属性 'name' 没有初始化器，并且在构造函数中没有明确赋值。`

要解决这个问题，我们可以使用属性名初始化器或在构造函数中进行初始化：

```typescript
name: string = 'Anastasia';
constructor(name: string) {
  this.name = name;
}
```

在这两个简洁的示例中，我们展示了 TypeScript 中严格模式和非严格模式在 noImplicitAny 和 strictPropertyInitialization 方面的区别。关键是在非严格模式下，一些与类型相关的问题可能会被忽略，例如隐式的 any 类型或未初始化的属性。但严格模式不仅仅包括这两个方面！那么，“严格”到底意味着什么呢？它是七个不同配置的集体简写。这些配置包括：

- noImplicitAny：没有显式类型注释的变量将具有隐式的 any 类型，这可能导致与类型相关的问题。严格模式不允许这种行为，并要求显式的类型注释。

- noImplicit：在严格模式下，函数中的 this 关键字必须显式类型化，减少了由于不正确的 this 使用导致的运行时错误的风险。

- strictNullChecks：严格的空值检查对可空类型实施更严格的检查，确保可空类型的变量不会被无意地当作非空类型使用。

- strictFunctionTypes：严格的函数类型标志对函数类型实施更严格的检查，有助于捕获与不正确的函数签名或返回类型相关的潜在问题。

- strictPropertyInitialization：在严格的属性初始化下，类属性必须在构造函数中初始化或具有默认值，减少了未初始化属性导致运行时错误的风险。

- strictBindCallApply：在 strict bind call apply 下，当使用 fn.apply()时，TypeScript 也会像在正常的 fn()调用中一样检查参数类型。

- alwaysStrict：在每个编译的 JS 文件顶部添加 "use strict"。

为了充分发挥 TypeScript 的功能，强烈建议在 tsconfig.json 配置文件中启用严格模式。顺便说一句，不使用 tsconfig.json 或未能将其提交到版本控制系统（项目存储库）是另一个大错误！
通过启用严格模式，开发者可以增强类型安全、提高代码可维护性并减少运行时错误的可能性。这有助于您及早捕获潜在问题，并提高 TypeScript 代码的质量和可维护性。这种做法最终会导致更健壮和可靠的代码库，确保充分利用 TypeScript 的静态类型系统的潜力。

## 2.4. 变量声明不正确

TypeScript（和 JavaScript 也是）提供了多种声明变量的方式，包括 let、const 和 var。然而，使用不正确的变量声明可能会导致意外行为、错误和更难维护的代码库。本节将讨论这些变量声明之间的区别以及使用它们的最佳实践。顺便说一下，为什么 TypeScript 变量会感到不安全？因为它没有“类型”！ :-)

let、const 和 var 的区别：

- let：用 let 声明的变量具有块级作用域，意味着它们只能在声明它们的块内访问。let 变量在初始声明后可以重新赋值。

- const：与 let 类似，const 变量具有块级作用域。然而，它们在初始声明后不能重新赋值，使其适合于在程序执行过程中不应更改的值。

- var：用 var 声明的变量具有函数级作用域，意味着它们在声明它们的整个函数内都可以访问。这可能会导致意外行为和更难理解的代码，因为变量提升，即变量声明会被移动到它们所在作用域的顶部。

这里是 TypeScript 代码示例，说明不正确的变量声明以及如何修复它们。

示例 1：使用 var 而不是 let 或 const。假设我们有一个带有 var i 的代码：

```typescript
for (var i = 0; i < 10; i++) {
  console.log('counting ', i);
}

console.log(i);
```

当我们输出 i 为 10 时，i 变量可以在循环作用域之外访问，这可能会由于可能的名称冲突导致意外行为（例如，如果在 i 变量的道路上有另一个循环）。解决方法是使用 let 或 const 进行变量声明，这样当我们尝试在作用域之外访问变量时会得到一个错误：

```typescript
for (let j = 0; j < 10; j++) {
  console.log('counting ', j);
}

console.log(j);     //  #A
```

`#A 错误：无法找到名称 'j'。`j` 变量的作用域仅限于循环，在循环之外不可访问。`

示例 2：错误地声明常量变量。接下来，我们有一个用 let 定义的常量（而不是 const）：

```typescript
let constantValue = 42;
// 很多代码在这里...
```

后来在代码中，开发者错误地更新了变量 `constantValue = 84;`，然后可能会出现错误。

解决方法是使用 const 来声明常量变量：

```typescript
const constantValueFixed = 42;
constantValueFixed = 84;    // #A
```

`#A 错误：不能给 'constantValueFixed' 赋值，因为它是一个常量。`

示例 3：错误地使用 let 来声明一个不重新赋值的变量。例如，我们有一个 userName 变量，它不重新赋值，但我们使用了 let：

```typescript
let userName = "Anastasia";
console.log(`Hello, ${userName}!`);
```

解决方法是使用 const 来声明不重新赋值的变量：

```typescript
const userNameFixed = "Anastasia";
console.log(`Hello, ${userNameFixed}!`);
```

值得注意的是，在幕后，const 允许 TypeScript 通过推断更精确的类型来确定类型。考虑这个例子：

```typescript
const propLiteral = "type";   // 类型是 "type" 字面量，而不是字符串
let propString = "type";      // 类型是字符串，而不是 "type" 字面量
```

正如你所看到的，对于 propLiteral，TypeScript 推断的类型是字符串字面量，而不仅仅是一般的字符串类型。

需要注意的是，const 声明提供的不变性确保了变量绑定（变量名）是不可变的，但不一定意味着值本身是不可变的。当涉及到值时，尤其是当值是对象时，这可能非常表面。对于对象、类似对象的值和数组，虽然你不能直接重新赋值，但它们的属性仍然可以修改。换句话说，在 JavaScript/TypeScript 中，const 确保变量是不可变的，但不一定是它引用的值（对于对象就是这种情况）。让我们看几个例子。

对于原始值（如数字、字符串、布尔值、`undefined` 和 `null`），这种区别有点微妙，因为值和变量绑定实际上是同一件事情。一旦一个原始值被分配，它就不能改变。例如，当你这样做：`const x = 10;` 你不能将 x 重新赋值为一个新的值。

然而，对于非原始值（如对象和数组），const 关键字只意味着你不能改变变量指向的引用，但对象或数组的内部可以被修改。这里有一个例子：

```typescript
const obj = { key: 'value' };
obj.key = 'newValue';       // #A
const arr = [1, 2, 3];
arr.push(4);                // #A
obj = { key: 'anotherValue' };      // #B
arr = [4, 5, 6];                    // #B
```

`#A 这是允许的`

`#B TypeError: 给常量变量赋值。`

在上面的例子中，虽然我们不能将 obj 和 arr 重新赋值为新的对象或数组，但我们仍然可以修改它们的内部值。

如果你想使对象的属性（或数组的值）本身不可变，你需要采取额外的措施，如 Object.freeze()。然而，即使 Object.freeze() 也只提供浅层的不可变性。如果一个对象包含嵌套对象，你需要一个深度冻结函数来使一切都不可变。这里是一个使用浅层 Object.freeze() 的递归深度冻结函数的示例：

```typescript
function deepFreeze(obj: any): any {

  const propNames = Object.getOwnPropertyNames(obj);            //  #A  
  propNames.forEach(name => {                                   //  #B
    const prop = obj[name];    
    if (typeof prop == 'object'                                 //  #C
      && prop !== null 
      && !Object.isFrozen(prop)) {
      deepFreeze(prop);
    }
  });

  return Object.freeze(obj);                                    // #D
}
```

`#A 获取在 obj 中定义的属性名`

`#B 在冻结对象本身之前冻结属性`

`#C 如果 prop 是一个对象或数组且尚未冻结，则递归`

`#D 冻结对象`

作为一个经验法则，这里是变量声明的最佳实践：

- 默认优先使用 const：在声明变量时，默认使用 const，因为它强制不变性并减少无意值更改的可能性。这可以导致更干净、更可预测的代码。

- 在必要时使用 let：如果一个变量需要重新赋值，使用 let。这确保了变量具有块级作用域，并避免了与函数级作用域相关的潜在问题。

- 避免使用 var：在大多数情况下，避免使用 var，因为它可能会由于函数级作用域和变量提升导致意外行为。相反，使用 let 或 const 来受益于块级作用域和更清晰的代码。

- 使用描述性变量名：选择清晰、描述性的变量名，传达变量的目的和值。这有助于提高代码的可读性和可维护性。

- 初始化变量：只要可能，在声明变量时使用（默认）值进行初始化。这有助于防止与未初始化变量相关的问题，并确保从变量的声明中可以清楚地了解其目的。

通过遵循这些变量声明的最佳实践，开发者可以创建更可维护、可预测和可靠的 TypeScript 代码库。通过优先使用 const，在必要时使用 let，避免使用 var，并选择描述性变量名，开发者可以最大限度地减少与变量声明相关的潜在问题，并提高代码的整体质量。

## 2.5. 误用可选链

可选链是 TypeScript 3.7 引入的一个强大功能，后来在 ECMAScript 2020（也称为 ES11）中引入到 JavaScript 中。可选链允许开发者在访问对象内的深层嵌套属性时，无需检查沿途每个属性的存在性。虽然这个功能可以导致更干净和更简洁的代码，但也可能被误用，导致意外行为和潜在问题。本节将讨论可选链的正确使用和要避免的常见陷阱。

可选链使用问号?运算符来访问对象的属性或函数调用的结果，如果链中的任何部分为 null 或 undefined，则返回 undefined。这可以大大简化涉及访问深层嵌套属性的代码。

在这个例子中，我们使用三种方法。第一种是没有可选链，可能会导致代码中断。第二种更好，因为它使用了 &&，这将有助于避免代码中断。最后一个例子使用可选链，比使用 && 的例子更紧凑：

```typescript
interface User {
  name: string;
  address?: {                                     // #A
    street: string;
    city: string;
    country: string;
  },
}

const userWithAddress: User = {
  name: "Sergei",
  address: {                                    // #A
    street: "Main St",
    city: "New York",
    country: "USA",
  },
};

const user: User = {
  name: "Sergei",
};

const cityDirectly = userWithAddress.address.city;
const cityDirectlyUndefined = user.address.city;                        // #B

const city = userWithAddress.address && userWithAddress.address.city;   // #C
const cityUndefined = user.address && user.address.city;

const cityChaining = userWithAddress?.address?.city;                    // #D
const cityChainingUndefined = user?.address?.city;
```
`#A 可选字段`

`#B 运行时错误：undefined 不是一个对象（评估 'user.address.city'）`

`#C 没有可选链 - 工作代码`

`#D 使用可选链`

在前面的代码片段中，city 和 cityChaining 的值是 "New York"，cityUndefined 和 CityChainingUndefined 的值是 "undefined"。代码在 cityDirectlyUndefined 处会在运行时中断。根据 TypeScript 的配置，开发者可以得到一个非常方便的警告“'userWithAddress.address' 可能是 'undefined'，'user.address' 可能是 'undefined'”，对于 cityDirectly 和 cityDirectlyUndefined 都是如此。因此，可选链提供了访问对象属性的最安全和最优雅的方式。让我们介绍一下可选链的正确使用，包括以下几点：

- 使用可选链访问深层嵌套属性：当访问多层深的属性时，使用可选链来简化代码并使其更具可读性。

- 结合可选链和空值合并：使用空值合并运算符??与可选链结合，在属性为 null 或 undefined 时提供默认值。

虽然我们将在接下来的部分中更多地介绍空值合并，但这里是一个简短的例子：

```typescript
const city = user?.address?.city ?? "Unknown";
```

这里是使用 TypeScript 的可选链时要避免的常见陷阱列表：

- 过度使用可选链：虽然可选链可以简化代码，但过度使用它会使代码更难读、理解和调试。例如，当太多嵌套属性是可选的，而不是一些或大多数是必需的，这会损害可读性。谨慎使用可选链，只有在它提供明显好处时才使用。

- 忽略潜在问题：可选链可以掩盖代码中的潜在问题，例如不正确的属性名称或意外的 null 或 undefined 值。回到前面关于 user 的例子，如果 API 将属性名 address 更改为 userAddress，那么代码仍然会工作，但总是返回 undefined，这使得很难识别问题的根源，如不一致或错别字。确保你的代码能够优雅地处理这些情况，并考虑是否需要额外的错误处理或检查。

- 误用于非可选属性：在使用应该始终存在的属性时要谨慎使用可选链。这可能会导致运行时的意外行为（例如，缺少必需的属性导致崩溃），并可能表明代码中存在更深层次的问题需要解决。

通过正确使用可选链并避免常见陷阱，开发者可以在访问深层嵌套属性时编写更干净和更简洁的代码。将可选链与空值合并（??）结合使用可以进一步提高代码的可读性，并确保在必要时提供默认值。然而，至关重要的是要谨慎使用可选链，并意识到它可能掩盖的潜在问题。

## 2.6. 不使用空值合并

空值合并是一个有用的功能，允许开发者在给定表达式计算为 null 或 undefined 时提供默认值。空值合并运算符??简化了在某些情况下处理默认值的方式。然而，过度使用空值合并可能会导致代码可读性降低和潜在问题。本节将讨论何时使用空值合并以及何时考虑替代方法。

让我们首先了解空值合并。空值合并运算符??在左操作数为 null 或 undefined 时返回右操作数。如果左操作数是任何其他值，包括 false、0 或空字符串，它将返回左操作数。

示例：

```typescript
const name = userInput?.name?? "Anonymous";
```

空值合并的适当使用：

- 提供默认值：使用空值合并来提供当属性或变量可能为 null 或 undefined 时的默认值。

- 简化条件表达式：空值合并可以简化检查 null 或 undefined 值的条件表达式，使代码更简洁。

- 与可选链结合：使用空值合并与可选链结合，以访问深层嵌套属性并在必要时提供默认值。

在使用空值合并时要避免的陷阱和替代方法：

- 误解假值：空值合并仅检查 null 和 undefined。在使用它与被认为是假值但不是空值的值（如 false、0 或空字符串）时要小心。在这些

情况下，需要审查需要实现的逻辑。如果假值需要被视为未初始化的值（这种情况很少见），那么可以考虑使用逻辑或运算符（||）代替。我们已经介绍了逻辑或和空值合并的区别。

值得更多关注的是，在初始化变量的默认值时，好的旧逻辑或（||）和空值合并（??）在 TypeScript（以及从 ES2020 开始的 JavaScript）中的区别。两者都可以用于提供默认值，但在特定场景下行为不同。下面是它们的区别：
使用逻辑或（||）时，如果左操作数为假值，即 `false`、`null`、`undefined`、`0`、`NaN`、`""`（空字符串）和 `-0`，则返回右操作数（默认/初始）值。
这里是逻辑或与空字符串、零、空和未定义的示例：

```typescript
const result1 = "" || "default";    // result1 = "default"
const result2 = 0 || 42;            // result2 = 42
const result3 = null || 42;         // result3 = 42
const result4 = undefined || 42;    // result4 = 42
```

另一方面，空值合并（??）的行为略有不同。它仅在左操作数为 null 或 undefined 时返回右操作数。它不考虑其他假值（如 `0`、`""` 或 `NaN`）作为触发条件。这意味着它的操作比 || 更具体。

这里是空值合并与空字符串、零、空和未定义的示例：

```typescript
const result1 = ""?? "default";    // result1 = ""（因为空字符串既不是 null 也不是 undefined）
const result2 = 0?? 42;            // result2 = 0（因为 0 既不是 null 也不是 undefined）
const result3 = null?? 42;         // result3 = 42
const result4 = undefined?? 42;    // result4 = 42
```

总之：当你想为任何假值提供默认值时使用 ||；当你只想为 null 或 undefined 提供默认值时使用??（推荐）。

在 TypeScript 中，由于类型系统的存在，这种区别变得更加重要，它允许更具体地处理值及其类型。空值合并运算符（??）在处理可能设置为 null 或 undefined 的可选属性或值时特别有用。

为了说明逻辑或的潜在陷阱，考虑另一个示例，其中 0 可能是正确的输入，例如 0 音量、0 数组索引、0 折扣。然而，由于逻辑或（||）的真值检查，我们的等式将回退到默认值（这不是我们想要的）。以下代码是 *不正确的*，因为如果 inputValue 为 0，defaultValue 将被使用：

```typescript
const value = inputValue || defaultValue;
```

在这种情况下（其中 0 可能是有效值），正确的方法是使用??或检查 null。以下两个替代方案是正确的，因为只有当 inputValue 为 null 或 undefined 时，defaultValue 才会被使用，而当它为 0 时不会：

```typescript
const value = inputValue?? defaultValue;
const value = inputValue!= null? inputValue : defaultValue;
```

虽然空值合并是 TypeScript 程序员工具箱中的一个美妙补充，但值得注意两个需要注意的事项：

- 过度使用空值合并：过度依赖空值合并可能会导致代码可读性降低，并可能表明存在更深层次的问题，例如变量初始化不当或代码逻辑不清晰。评估空值合并是否是最佳解决方案，或者更明确的方法是否会更清晰。
- 
- 忽略适当的错误处理：空值合并有时可能被用于掩盖代码中的潜在问题或错误。确保你的代码能够优雅地处理值为 null 或 undefined 的情况，并考虑是否需要额外的错误处理或检查。

通过适当地使用空值合并并注意其陷阱，开发者可以在处理默认值时编写更干净和更简洁的代码。然而，理解空值合并的细微差别并在必要时考虑替代方法对于保持代码的可读性和健壮性至关重要。

## 2.7. 模块导出和导入不当

模块化是编写可维护和可扩展的 TypeScript 代码的重要方面。通过将代码分离到模块中，开发者可以更好地组织和管理他们的代码库。然而，一个常见的错误是错误地使用模块的导出或导入，这可能会导致各种问题和错误。本节将讨论正确导出和导入模块的重要性，并提供避免错误的最佳实践。

让我们从定义模块和导出的含义开始。模块是一个包含 TypeScript 代码的文件，包括变量、函数、类或接口。模块允许开发者将代码分离成更小、更易于管理的部分，并促进代码的重用。导出模块（或更确切地说，是模块中的符号）意味着使其内容可被其他模块导入和使用。导入模块允许开发者在他们的代码中使用该模块导出的内容。TypeScript 中的一个模块可以有几个导出的符号（例如，对象、类、函数、类型）和/或一个默认导出的符号。

在 TypeScript（和 JavaScript）中有现代语法来导出和导入模块，使用 export 和 import 语句。这被称为 ES6/ECMAScript 2015 模块导入。截至本文撰写时，所有现代浏览器和所有主要的捆绑器（创建 Web“二进制”的工具）都支持它。

旧的且 *不推荐* 的方法包括但不限于：

- CommonJS/CJS：该语法使用 require 和 module.exports；它是 Node.js 的遗产。

- 加载器，如异步模块定义、通用模块定义、SystemJS 等

- 立即执行函数表达式（IIFE）和 HTML 脚本标签：虽然 IIFE 是为了包装导出的模块以避免变量名的全局范围污染而创建的，但脚本标签加载（无论是在 HTML 中静态加载还是使用 JS 注入 DOM 元素动态加载）仍然广泛用于分析和营销服务。

这些方法不再推荐的原因有很多，主要原因是 ES6 模块是广泛采用的标准，并得到许多库和浏览器的支持。ESMs 具有静态分析（这意味着导入和导出在编译时确定，而不是在运行时），这为我们提供了树摇、可预测性、自动完成和更快的查找。此外，当在所有代码库中一致使用 ES6 模块时，我们可以避免错误，因为 ES6 模块的机制和语法与 CJS 不同。甚至 Node.js 现在也支持 ESM！

导出和导入模块的最佳实践：

- 使用命名导出：优先使用命名导出而不是默认导出，这允许从单个模块导出多个变量、函数或类。此外，命名导出使导出的项目更加清晰，并允许更好的代码组织。

```typescript
// user-module.ts：导出命名符号
export const user = { /*...*/ };
export function createUser() { /*...*/ };
// 导入命名符号
import { user, createUser } from './user-module';
```

- 避免使用默认导出，即使对于单个导出：如果一个模块只导出一个项目，例如一个类或函数，可能会倾向于使用默认导出。这可以简化导入并使代码更具可读性。

```typescript
// user.ts：导出默认符号
export default class User { /*...*/ };
// 导入默认符号
import User from './user';
```

然而，选择默认导出需要导入者为符号决定一个名称。这可能会导致在代码库中对同一个符号使用不同的名称。建议使用命名导出来促进统一的命名约定。这里是一个导入默认符号时命名不好（令人困惑）的示例：

```typescript
// developer.ts：导出默认符号
export default class Developer { /*...*/ };
// tester.ts：导出默认符号
export default class Tester { /*...*/ };
// 导入默认符号
import User1 from './developer';
import User2 from './tester';
```

- 组织导入和导出：将导入和导出保持在模块文件的顶部组织。这有助于开发者一眼就了解模块的依赖关系，并使更新或修改它们更容易。

- 注意循环依赖：循环依赖发生在两个或多个模块相互依赖，直接或间接。这可能会导致意外行为和运行时错误。为了避免循环依赖，重构你的代码以创建一个清晰的依赖层次结构，并最小化模块之间的直接耦合。值得一提的是，由于静态 ESM 没有评估顺序，循环依赖在静态 ESM 中问题较少。这是使用它们而不是 CJMs、脚本标签注入或动态 ESM（import()）的另一个好理由，这导致我们进入下一点。

- 尽可能避免使用动态导入：动态导入主要是 CJMs、脚本标签注入和 import()。确实，动态导入可以通过允许在运行时加载模块提供一定的灵活性，因此在运行程序之前不需要确切知道模块的名称。然而，它们可能会带来比好处更多的麻烦。

- 正确导出类型：使用 type 关键字仅用于导出类型，并使用 consistent-type-imports ESLint 插件来警告不使用它。type 关键字将告诉 TypeScript 这个模块仅存在于类型系统中，而不在运行时，并且在转译过程中可以删除它。

- 避免导入未使用的变量：导入在代码中未使用的变量可能会导致代码膨胀、性能下降和可维护性降低。许多 IDE 和 lint 工具可以警告你关于未使用的导入，使你更容易识别和删除它们。

- 利用捆绑进行树摇：使用像 Webpack 或 Rollup 这样的工具可以帮助进行树摇，这是在捆绑过程中删除未使用代码的过程。注意未使用的导入并及时解决它们，开发者可以保持代码干净高效，并且他们的 Web 二进制文件（捆绑包）更小，这导致最终用户的加载时间得到改善。

在普通 JavaScript 中导入和导出模块时常见的错误有很多：

- 忘记导出：确保从模块中导出所有必要的变量、函数、类或接口，以使它们可用于其他模块的导入。

- 导入不正确：在导入模块时要小心，并仔细检查你是否使用了正确的命名或默认导出的导入语法。误用导入语法可能会导致错误或未定义的值。

- 缺少导入语句：确保在代码中导入所有必需的模块。忘记导入模块可能会导致运行时错误或未定义的值。

幸运的是，在 TypeScript 中这些问题不是大问题，因为它会为我们提供支持。省略或格式不正确的导入将导致类型检查错误。TypeScript 的一个重要好处是它能够提供安全网，捕获这些类型的错误，这使我们比使用普通 JavaScript 更有信心地编码。
通过正确导出和导入模块，开发者可以创建可维护和可扩展的 TypeScript 代码库。遵循最佳实践并注意常见错误有助于避免与模块管理相关的问题，确保更健壮和有组织的代码库。

## 2.8. 不使用或误用类型断言

类型断言是 TypeScript 中的一个功能，允许开发者覆盖值的推断类型，本质上是告诉编译器相信他们对值类型的判断。类型断言使用 as 关键字。虽然类型断言在某些情况下可能有用，但不恰当的使用可能会导致运行时错误、降低类型安全性和使代码库更难维护。本节将讨论何时使用类型断言以及如何避免其误用。
类型断言是一种告知 TypeScript 编译器开发者对值的类型有更多信息的方式，而这些信息超出了类型推断系统的能力。类型断言不会执行任何运行时检查或转换；它们纯粹是编译时的构造。
这里是一个示例，其中我们定义了一个未知类型的变量，但在进一步操作之前必须断言（或使用类型守卫），以避免出现“类型 'unknown' 不能分配给类型 'tring'”的错误：

```typescript
const unknownValue: unknown = "Hello, TypeScript!";
const stringValue: string = unknownValue as string;
```

类型断言的适当使用包括：

- 处理未知类型：当处理 unknown 类型时，类型断言可能会很有帮助，在使用之前可能需要显式的类型断言或类型守卫。

- 缩小类型：类型断言可以用于将联合类型或其他复杂类型缩小到更具体的类型，前提是开发者有充分的理由相信类型是准确的。最常见的用例是从联合类型（T|null）中删除 null 和 undefined，即使用空值断言运算符 val!。

- 与外部库交互：当与类型定义不足或不正确的外部库交互时，可能需要使用类型断言来纠正类型信息。

这里是一个类型断言使用良好的示例，我们确信元素是一个图像：

```typescript
const el = document.getElementById("foo")
console.log(el.src)     // #A
const el = document.getElementById("foo") as HTMLImageElement
console.log(el.src)     // #B
```

`#A 属性'src' 在类型 'HTMLElement' 上不存在。`

`#B 现在这里一切都好`

关于类型转换和类型断言的一个小附注。在 TypeScript 中，它们可以是同义词。然而，TypeScript 中的类型断言与其他语言（如 Java、C 或 C++）中的类型转换不同，在其他语言中，转换会在运行时将值的类型更改为不同的类型。这与 TypeScript 的“转换”不同，它不会改变程序在运行时的行为，而是提供了一种在 TypeScript 类型检查阶段覆盖推断类型的方法。这是因为 TypeScript 的“转换”在运行普通 JavaScript 时会在运行时被剥离。

这里是 TypeScript 中类型断言的一些陷阱和替代方法：

- 过度使用类型断言：过度依赖类型断言会导致代码的类型安全性降低，并可能表明代码设计存在更深层次的问题。评估类型断言是否是最佳解决方案，或者更明确的方法是否会更清晰和更安全。

- 忽略类型错误：类型断言可能被误用以绕过 TypeScript 的类型检查系统，这可能会导致运行时错误和降低类型安全性。在使用类型断言之前，始终确保它是有效的和必要的。

- 使用类型断言代替类型声明：如果可行，类型声明更受欢迎，因为它们执行过多的属性检查，而类型断言执行的类型检查要有限得多。
这里是一个使用类型声明和类型断言进行变量初始化的示例。它们都有效，但类型声明更受欢迎：

```typescript
const declaredInstanceOfSomeCrazyType: SomeCrazyType = {...};
const assertedInstanceOfSomeCrazyType = {...} as SomeCrazyType;
```

- 绕过适当的类型守卫：而不是使用类型断言，考虑实现类型守卫来执行运行时检查并提供更好的类型安全性。类型守卫是返回布尔值的函数，指示值是否为特定类型。我们将在后面更详细地介绍它们。

这里是一个示例，其中我们说明了两种方法：类型守卫和类型断言。假设我们有一些接受字符串和数字的代码。当我们使用 value 作为数字时，我们会得到一个错误“Object is of type 'unknown'”，因为 TypeScript 不确定：

```typescript
function plusOne(value: unknown) {
  console.log(value+'+1')
  console.log((value)+1)      // #A
}

plusOne('abc')
plusOne(123)
```

`#A Object is of type 'unknown' because TypeScript is unsure about the value type inferred from usage`

要解决这个问题，我们可以使用类型断言，如下所示：

```typescript
console.log((value as number)+1)
```

或者我们可以使用类型守卫来指导 TypeScript 关于 value 的类型，如下所示：

```typescript
function plusOne(value: unknown) {
  if (typeof value === 'string') {
    console.log(value+'+1')
  } else if (typeof value ==='number') {
    console.log(value+1)
  }
}

plusOne('abc')
plusOne(123)
```

在某些情况下，开发者可能会发现自己使用一种模式，如 unknownValue as unknown as knownType，在断言值的类型时绕过中间类型。虽然这种技术在某些情况下可能有用，例如处理类型不佳的外部库或复杂的类型转换，但它也会引入风险。以这种方式链接类型断言会破坏 TypeScript 的类型安全性，并可能掩盖错误。谨慎使用这种模式，仅在必要时使用，并确保断言是有效的和合理的。只要可能，考虑利用适当的类型守卫、改进类型定义或为外部库提供更好的类型，以避免这种模式并保持类型安全。
通过正确使用类型断言并注意其陷阱，开发者可以编写更干净、更安全和更可维护的 TypeScript 代码。确保仅在必要时使用类型断言，并考虑替代方法，如类型守卫，以提供更好的类型安全性和运行时检查。

## 2.9. 不正确地检查相等性

在 TypeScript 中比较值时，理解相等运算符（==）和严格相等运算符（===）之间的区别至关重要。混淆这两个运算符可能会导致意外行为和难以发现的错误。此外，我们应该记住对对象（和类似对象的类型，即数组）进行深度比较的必要性。本节将讨论这两个运算符的区别、它们的适当用例以及如何避免常见陷阱，然后以深度比较的示例结束。

相等运算符（==）用于比较两个值是否相等，如果相等则返回 true，否则返回 false。然而，==在比较不同类型的值时会进行类型强制转换，这可能会导致意外结果。例如，这行代码会打印/记录 true，因为数字 42 被强制转换为字符串"42"：

```typescript
console.log(42 == "42");
```

另一方面，严格相等运算符（===）用于比较两个值是否相等，同时考虑它们的值和类型。不进行类型强制转换，使===成为更安全和更可预测的比较选择。例如，下面的语句会打印/记录 false，因为 42 是数字，而"42"是字符串：

```typescript
console.log(42 === "42");
```

请注意，TypeScript 会警告我们：这个比较似乎是无意的，因为类型'number'和'string'没有重叠。干得好，TypeScript！
使用==和===的最佳实践如下：

- 优先使用===进行比较：在大多数情况下，使用===来比较值，因为它提供了更可预测和更安全的比较，而不会进行类型强制转换。

- 谨慎使用==（如果使用的话）：虽然在某些情况下使用==可能很方便（例如，x == null可以方便地检查`x`是否为`null`或`undefined`），但要谨慎并确保理解类型强制转换的影响。如果需要比较不同类型的值，请考虑在使用==之前将它们显式转换为共同的类型。

- 利用代码检查工具：像ESLint这样的工具可以帮助强制使用===的一致性，并在使用==时发出警告，降低引入错误的风险。

在比较值时要避免的常见陷阱：

- 依赖类型强制转换：避免在使用==时依赖类型强制转换。类型强制转换可能会导致意外结果和难以发现的错误。相反，使用===或在比较之前显式将值转换为共同的类型。

- 忽略严格不等运算符（!==）：与严格相等运算符===类似，在比较值是否不相等时使用严格不等运算符（!===）。这确保在比较中同时考虑值和类型。

- 混淆引用和值的相等性检查：当比较对象或数组（特殊对象）时，它们是通过引用传递的，因此使用===是不够的，因为在这种情况下，将比较引用而不是值。换句话说，当比较两个对象时，只有当它们是同一个对象时才会返回true（在其他所有情况下，即使它们的属性不相等，也会返回false）。因此，重要的是要记住，对于对象，我们需要进行深度比较，即无论嵌套结构有多深，都要对每个子值进行比较。像lodash.deepEqual或至少JSON.stringify()这样的方法可能会很有用。

通过理解==和===的区别并遵循最佳实践，开发者可以编写更可预测和可靠的TypeScript代码。使用严格相等和严格不等运算符确保类型强制转换不会引入意外行为，从而导致更可维护和健壮的代码库。

在某些情况下，您可能想要对对象或复杂类型进行深度比较，而==和===都不适合。在这种情况下，您可以使用流行库提供的实用方法，例如Lodash的isEqual函数。isEqual函数对两个值进行深度比较，以确定它们是否等价，考虑对象和数组的结构和内容。这在比较具有嵌套属性的对象或具有非原始值的数组时特别有用。请记住，使用像isEqual这样的实用方法可能会带来性能成本，特别是对于大型或深度嵌套的数据结构。

这里是一个在TypeScript中对具有嵌套级别的对象进行深度相等比较的简单实现：

```typescript
function deepEqual(obj1: any, obj2: any): boolean {
  if (obj1 === obj2) {
    return true;
  }
  if (typeof obj1 !== 'object' 
    || obj1 === null 
    || typeof obj2 !== 'object' 
    || obj2 === null) {
    return false;
  }
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);
  if (keys1.length !== keys2.length) {
    return false;
  }
  for (const key of keys1) {
    if (!keys2.includes(key)) {
      return false;
    }
    if (!deepEqual(obj1[key], obj2[key])) {
      return false;
    }
  }
  return true;
}
```

这个deepEqual函数递归地比较两个对象，检查它们是否具有相同的键，并且每个键的值是否相同。它适用于具有嵌套级别和数组的对象，以及原始值。然而，这个实现不处理某些边缘情况，例如处理循环引用或比较函数。
请记住，深度比较可能在计算上是昂贵的，特别是对于大型或深度嵌套的数据结构。在生产代码中处理复杂数据结构时，请谨慎使用此方法，并考虑使用优化的库，如Lodash。

## 2.10. 不理解类型推断

TypeScript以其强大的类型系统而闻名，该系统有助于开发者在编译时捕获潜在错误并提高代码的可维护性。TypeScript类型系统的一个强大功能是类型推断，它允许编译器根据值的使用自动推断值的类型。不理解类型推断可能会导致意外错误。本节将讨论类型推断并提供有效利用它的最佳实践。

让我们从理解TypeScript的类型推断开始。类型推断是TypeScript自动确定值的类型而无需开发者显式提供类型注释的过程。这在各种上下文中发生，例如变量赋值、函数返回值和泛型类型参数。

这里是一个简单的类型推断示例：

```typescript
const x = 42;      // #A
let y;             // #B
y = 1;
let z = 10;        // #C
z = 2;
function double(value: number) {    // #D
  return value * 2;
}
```
`#A TypeScript推断x的类型为数字字面量，因为x是不可变的`

`#B TypeScript推断y的类型为any`

`#C TypeScript推断z的类型为数字（扩展），而不是字面量，因为z是可变的`

`#D TypeScript推断函数的返回类型为数字`

利用类型推断的最佳实践：

- 在必要时提供类型注释：在某些情况下，TypeScript的类型推断可能无法推断出正确的类型，或者您可能想要强制特定的类型。在这些情况下，提供显式的类型注释来指导编译器。

- 尽可能提供类型注释：显式注释可以使意图更清晰，特别是在更复杂的场景中。（这一点可能是我个人的观点，关于是否明确注释函数的返回类型或依赖TypeScript的推断在很多情况下是有争议的。接受类型推断可以让TypeScript推断值的类型，从而减少代码的冗长性。）

- 利用上下文类型：TypeScript的上下文类型允许编译器根据值使用的上下文推断类型。例如，当将函数分配给具有特定类型Callback的变量时，TypeScript可以推断函数参数data和返回值的类型。

```typescript
type Callback = (data: string) => void;
const myCallback: Callback = (data) => {
  console.log(data);
};

myCallback('123')       // #A
```
`#A 这里不需要类型注释！`

这种情况的一个典型场景是当您将回调函数传递给map或filter等方法时。在这种情况下，TypeScript可以从数组的类型推断函数参数的类型：

```typescript
const numbers = [1, 2, 3];
const squared = numbers.map(num => num * num);      // #A
```
`#A 这里不需要类型注释！`

- 利用类型推断进行泛型：TypeScript可以根据传递给泛型函数或类的参数类型推断泛型类型参数。利用这个特性来编写更简洁和灵活的代码。
```typescript
function identity<T>(value: T): T {
  return value;
  const result = identity([1, 2, 3]);     // #A
}
```
`#A TypeScript推断类型参数T为数字数组`

在使用TypeScript的类型推断时要避免的常见陷阱包括：

- 不要害怕过度注释：虽然过度注释会使代码更冗长、编写更不方便且更难维护，但不要害怕为即使TypeScript已经能够推断出正确类型的值提供类型注释。

- 忽略类型推断能力：注意TypeScript的类型推断能力以及它可能引入的潜在错误。例如，如果函数a定义为function a() { return b(); }，b的返回类型的任何更改都会自动反映在a的推断返回类型中。但是，如果您为a提供了显式的类型注释，这种自动更新就不会发生。

接受类型推断或过度注释，这是您的选择，但无论如何您都需要理解类型推断。类型推断允许编译器自动推断类型，并且仅在必要时提供类型注释。依赖类型推断，开发者可以编写更简洁和可维护的TypeScript代码，但这种依赖也可能会引入一些意外行为，而过度注释可能会捕获这些行为。

## 2.11. 总结

- 我们不应该过于频繁地使用any来增加TypeScript的好处

- 我们不应该忽视TypeScript编译器的警告

- 我们应该使用严格模式来捕获更多错误

- 我们应该正确地使用let和const来声明变量

- 我们应该在需要检查属性存在时使用可选链？

- 我们应该使用空值合并来检查??对于null和undefined，而不是||

- 我们应该使用ES6模块标记正确地导出和导入模块。

- 我们应该理解类型断言，并且不要过度依赖unknown

- 我们应该在==的位置使用===来确保正确的检查。

- 我们应该理解类型推断的能力，并在可行的情况下进行过度注释。
