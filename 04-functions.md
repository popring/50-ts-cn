# 4。函数和方法

**本章涵盖**

- 通过正确完成重载函数签名来增强类型安全性

- 指定函数的返回类型

- 在函数中正确使用剩余参数（...）

- 在bind、apply、call和StrictBindCallApply的支持下，掌握函数中this和globalThis的本质

- 安全地处理函数类型

- 对函数使用实用程序类型 ReturnType、Parameters、Partial、ThisParameterType 和 OmitThisParameter

好吧，准备好深入了解 TypeScript 和 JavaScript 的函数世界吧。您问为什么我们要关注功能？好吧，如果没有函数，JavaScript 和 TypeScript 将像巧克力茶壶一样毫无用处。那么，让我们言归正传吧——或者我应该说“功能”？呃，不是吗？我保证笑话会变得更好！

现在，就像没有片尾字幕的《复仇者联盟》电影一样，没有函数的 JavaScript 和 TypeScript 会让我们感到非常绝望。 TypeScript 是更老、更复杂的兄弟，它带来了各种功能风格，使编码不再只是一件平凡的苦差事。

首先，我们有一个简单的函数声明，这是 TypeScript 继承的 JavaScript 原始声明：

```typescript
function greet(name) {
  console.log(`Hello, ${name}!`);
}

greet("Tony Stark");      // #A
```

代码_1_占位符

然后，TypeScript 为了追求更严格的类型，向参数和返回值添加了类型：

```typescript
function greet(name: string): void {
  console.log(`Hello, ${name}!`);
}

greet("Peter Parker");    // #A
```

代码_3_占位符

顺便说一句，要赞美 T​​ypeScript 函数，只需告诉它它非常可以调用。

我们还有一个提升函数的概念。 JavaScript 中的函数提升是一种行为，即在执行代码之前，在编译阶段将函数声明移动到其包含范围的顶部。这就是为什么您可以在代码中声明函数之前调用该函数。但是，仅提升函数声明，而不提升函数表达式。

```typescript
hoistedFunction();        // #A

function hoistedFunction(): void {  
  console.log("Hello, I have been hoisted!");
}
```

`#A Outputs: "Hello, I have been hoisted!"`

JavaScript 中的函数表达式是将函数定义为表达式的一种方法，这意味着函数可以分配给变量、存储在对象中或作为参数传递给其他函数。

与提升到其作用域顶部的函数声明不同，函数表达式不会提升，这意味着在代码中定义函数表达式之前无法调用它。

这是函数表达式的一个简单示例：

```typescript
let greet = function (): void {
  console.log("Hello, world!");
};

greet();                  // #A
```

`#A Outputs: "Hello, world!"`

我们不要忘记迷人的箭头函数，它们将我们带入 ES6 的必杀技。简短、甜蜜且有约束力，它们是 TypeScript 世界的鹰眼：

```typescript
const greet = (name: string): void => {
  console.log(`Hello, ${name}!`);
};

greet("Bruce Banner");    // #A
```

代码_9_占位符

函数表达式还可以用作其他函数的回调参数或用作立即调用函数表达式 (IIFE)，而无需分配给变量。这通常用于创建新作用域并避免污染模块或库的全局作用域：

```typescript
(function (): void {
  const message = "Hello, world!";

  console.log(message);   // #A
})();

console.log(message);     // #B
```

`#A Outputs: "Hello, world!"`
`#B Uncaught ReferenceError: message is not defined`


当然 IIFE 可以写成粗箭头函数：

```typescript
(() => {
  const message = "Hello, World!";
  console.log(message);
})();
```

IIFE 是 JavaScript 的经典，在 ES2015 之后有些过时了，我们可以使用块（花括号）创建一个作用域，但仅适用于 let 和 const，而不适用于 var（无论如何我们都不应该使用 var）。该变量在该块之外不可访问！

```typescript
{
  let blockScopedVariable = "I'm block scoped!";
}
```

是时候开个玩笑了。 TypeScript 函数停止在手机上调用 JavaScript 函数的真正原因是它不想处理更多_意外参数_！

在本章中，我们将在与函数相关的 TypeScript 混乱迷宫中漫步，并提供一两个热情洋溢的笑话和可靠、可行的建议。您将踏上一次启迪之旅！我们将介绍函数中类型的重要性、剩余参数（不要与漫长的一天后的剩余参数混淆）、TypeScript 实用程序类型，以及臭名昭著的 this。它就像变色龙，根据所在位置改变颜色。现在是我们仔细观察并尝试了解其真实本质的时候了。

所以，放松一下，喝点浓缩咖啡，准备好咯咯地笑和“啊哈！”时刻。本章不仅会逗你开心，还会引导你穿过 TypeScript 函数和方法的迷宫，一次一笑。

## 4.1. 省略返回类型

在 TypeScript 中，在整个代码库中拥有明确定义的类型至关重要。这包括显式定义函数的返回类型以确保一致性并防止意外问题。省略返回类型可能会导致混乱，使开发人员难以理解函数的意图或其返回的数据的形状。本节将探讨省略返回类型可能出现的问题，并提供如何避免这些问题的指导。

当您没有指定函数的返回类型时，TypeScript 将尝试根据函数的实现来推断它。虽然 TypeScript 的类型推断功能很强大，但过度依赖它们可能会导致意想不到的后果。例如，如果函数的实现发生变化，推断的返回类型也可能会发生变化，这可能会在代码中引入错误和不一致。

通过显式定义返回类型，您可以防止对函数契约的意外更改。从长远来看，这使您的代码更加健壮且更易于维护，因为开发人员可以依靠返回类型来了解函数的预期行为。此外，在函数中提供返回类型可以使您的代码更加自文档化，并且对于您和可能参与该项目的其他开发人员来说更容易理解。这在大型代码库以及与多个开发人员协作时尤其重要。

此外，指定返回类型有助于确保整个代码库的一致性。这在与团队合作时特别有用，因为它就如何使用函数以及它们应该返回什么建立了明确的契约。我们不要忘记改进的开发人员体验，因为通过正确的函数返回类型，IDE 可以提供及时的自动完成和自动建议。尽管在大多数情况下，IDE 也可以使用推断的返回类型来执行此操作，即当我们没有显式指定返回类型但 TypeScript 尝试猜测返回类型是什么时。但是，当代码中的类型发生意外更改时，推断类型的问题（稍后您将看到）可能会潜移默化。推断类型在不应更改时会更改（这会导致错误）。

让我们看一些示例，说明指定返回类型的重要性。在第一个示例中，我们有一个典型的 hello world 函数greet，它没有返回类型，这意味着 TypeScript 将从代码中推断出类型，如下所示：greet 函数的返回类型被推断为字符串或未定义（即，函数问候（名称：字符串）：字符串|未定义）。

```typescript
function greet(name: string) {
  if (name) {
    return `Hello, ${name}!`;
  }
  return;                           // #A
}

console.log(greet("Afanasiy"));     // #B
console.log(greet(""));             // #C
```

`#A return undefined`
`#B Hello, Afanasiy!`
`#C undefined`

当我们传递一个真值字符串时，函数返回一个 hello 字符串，但是当字符串为空（假值）或不存在（未定义，也是假值）时，函数返回未定义。

您可能认为空返回是多余的，因为该函数无论如何都会返回。对于 JavaScript 来说确实如此，尽管 TypeScript 恰恰相反；如果没有空的 return 语句，TypeScript 就会向我们咆哮“并非所有代码路径都会返回值”，因为它看不到“else”场景的返回。

所以，我们愚蠢简单的 hello world 代码就可以工作了。尽管如此，通过在第二个示例中显式定义返回类型，您可以清楚地表明该函数可以返回字符串或未定义。这增强了可读性，有助于防止回归，并强制整个代码库的一致性。

```typescript
function greet(name: string): string | undefined {
  if (name) {
    return `Hello, ${name}!`;
  }
  return;
}
```

此外，具有显式返回类型可以防止错误。例如，如果一只猫用爪子敲打键盘，最后的返回值为 42，那么使用推断类型就可以了，不会出现错误：

```typescript
function greet(name: string) {
  if (name) {
    return `Hello, ${name}!`;
  }
  return 42;              // #A 
}
```

`#A No errors but it's not correct!`

相反，使用显式返回类型，我们很容易捕获错误：

```typescript
function greet(name: string): string | undefined {
  if (name) {
    return `Hello, ${name}!`;
  }
  return 42;              // #A
}
```

`#A Type 'number' is not assignable to type 'string'.`

接下来让我们看一个更复杂的示例来说明指定具有接口、类型和函数的返回类型的重要性。在此示例中，我们有类型/接口 Book、ApiResponse 和一个没有返回类型的函数 processApiResponse。在函数中，我们“即时”计算字段年龄并将其添加到每本书中（所谓的虚拟字段）：

```typescript
interface Book {
  id: number;
  title: string;
  author: string;
  publishedYear: number;
}

interface ApiResponse<T> {
  status: number;
  data: T;
}

function processApiResponse(response: ApiResponse<Book[]>) {  // #A
  if (response.status === 200) {
    return response.data.map((book) => ({
      ...book,
      age: new Date().getFullYear() - book.publishedYear,
    }));
  }
  return;
}
```

`#A Without explicit return type, inferred type is { age: number; id: number; title: string; author: string; publishedYear: number; }[] | undefined`

在此示例中，我们有一个 Book 接口和一个包装通用负载的 ApiResponse 类型。 processApiResponse 函数接受包含 Book 对象数组的 ApiResponse，并返回带有附加年龄属性的已处理书籍数组，但前提是响应状态为 200。

我们不指定返回类型，TypeScript 将返回类型推断为 ({ id: number; title: string;author: string;publishedYear: number;age: number; }[] | undefined)。虽然这可能是正确的，但其他开发人员很难理解该函数的意图。

在下面的改进版本中，我们创建一个 ProcessedBook 类型，并显式定义函数的返回类型为 ProcessedBook[] | undefined 使函数的目的和返回值更加清晰易懂，提高代码整体的可读性和可维护性：

```typescript
interface ProcessedBook extends Book {        // #A
  age: number;
}

function processApiResponseWithReturnType(
  response: ApiResponse<Book[]>
): ProcessedBook[] | undefined {              // #B
  if (response.status === 200) {
    return response.data.map((book) => ({
      ...book,
      age: new Date().getFullYear() - book.publishedYear,
    }));
  }
  return;
}
```

`#A Throwback to the previous chapter on extending the interfaces`
`#B Explicit function return type`

接下来，我将向您说明如何将 processApiResponse 和 processApiResponseWithReturnType 函数与示例数据结合使用，以了解两者在功能上是等效的：

```typescript
const apiResponse: ApiResponse<Book[]> = {                  // #A
  status: 200,
  data: [
    {
      id: 1,
      title: "The Catcher in the Rye",
      author: "J.D. Salinger",
      publishedYear: 1951,
    },
    {
      id: 2,
      title: "To Kill a Mockingbird",
      author: "Harper Lee",
      publishedYear: 1960,
    },
  ],
};

const processedBooks = processApiResponse(apiResponse);     // #B
console.log(processedBooks);
const processedBooksWithReturnType =
  processApiResponseWithReturnType(apiResponse);            // #C
console.log(processedBooksWithReturnType);
```

`#A Defining the sample data`
`#B Using processApiResponse (without return type)`
`#C Using processApiResponseWithReturnType (with return type)`

两个函数将产生相同的输出。尽管如此，通过使用具有显式定义的返回类型的 processApiResponseWithReturnType 函数，您可以为将来使用该函数的任何人提供更好的类型安全性、改进的代码可读性以及更可预测的行为。为了说明这一点，假设开发人员对响应函数的输出（使用推断的返回类型）进行了错误的更改。我们将无法发现错误：

```typescript
function processApiResponse(response: ApiResponse<Book[]>) {       // #A
  if (response.status === 200) {
    return response.data.map((book) => {
      return {
        id: book.id,
        title: 123,                                                // #B
        age: new Date().getFullYear() - book.publishedYear,
        invalidProp: true,                                         // #C
      };
    });
  }
  return;
}
```

`#A Without explicit return type`
`#B Wrong type number of a correct field title (should be string)`
`#C Wrong new property, while the correct field publishedYear is missing`

上面显示的没有返回类型的函数很容易出现错误，因为 TypeScript 无法捕获它们。在具有 type 的函数中，您将得到 Type '{ id: number;标题：编号；年龄：数字； invalidProp：布尔值； }[]' 不可分配给类型“ProcessedBook[]”。

此外，类型推断并不总是正常工作。例如，我们可以有一个用于主要书籍封面颜色的字段，它是数字或字符串的数组，因为历史上在我们的数据库中，我们首先以十六进制数字格式存储颜色，然后切换到十六进制字符串格式。因此，我们有一些书包含字符串数组，而其他书则包含数字数组。如果我们必须编写一个函数 processColors 来处理颜色，它需要一个字符串或数字数组，但错误地返回一个推断类型的数组，其中每个值都可以是字符串或数字：

```typescript
function processColors(elements: string[] | number[]) {
  let arr = [];                   // #A
  arr.push(...elements);          // #B
  return arr;                     // #C
}

console.log(
  processColors(                  // #D
    Math.random() > 0.5           // #E
      ? [                         
          `#${Math.floor(Math.random() * 0xffffff).toString(16)}`,
          `#${Math.floor(Math.random() * 0xffffff).toString(16)}`,
        ]
      : [
          Math.floor(Math.random() * 0xffffff),
          Math.floor(Math.random() * 0xffffff),
        ]
  )
);
```

`#A Type is any[]`
`#B Assign items of elements to arr`
`#C Array is incorrectly (string | number)[] but should be string[] | number[]`
`#D Usage of processColors function`
`#E Randomly choose between generating an array of hex color strings or an array of numbers`

为了解决这个问题，我们可以将返回类型添加到 processColors 并将 arr 定义为 string[] |数字[]。

总之，_允许_ TypeScript 推断返回类型通常可以使你的代码更短且更易于阅读。这是一个 TypeScript 功能，不使用它是愚蠢的。例如，通常不需要显式指定与 `map` 或 `filter` 一起使用的回调的返回类型。更重要的是，过于详细的返回类型可能会使您的代码在重构期间更容易崩溃（因为有更多地方可以更改代码）。然而，推断类型并不总是容易出错（正如我们在书籍颜色的示例中看到的那样），并且可能会让错误潜入（正如我们在 API 响应更改中看到的那样）。当涉及到函数返回类型的推断类型时，我们应该特别小心。

因此，一个实用的指南是显式注释代码中形成导出 API、组件/模块之间的契约、公共接口等的任何部分的返回类型。换句话说，显式返回类型作为重要位置的保护措施可以提高 TypeScript 代码的整体质量和可维护性。正如您所看到的，通过明确预期返回值，您可以防止潜在问题、增强可读性并促进项目的一致性。

## 4.2. 函数中类型处理不当

TypeScript 中的函数类型使您能够为回调函数定义预期的输入和输出类型，从而提供类型安全并使您的代码更加健壮。不使用函数类型进行回调可能会导致混乱、难以发现的错误和难以维护的代码。本节讨论使用函数类型进行回调的重要性，并提供如何正确执行此操作的示例。

### 4.2.1. 不指定回调函数类型

在定义接受回调的函数时，指定预期的回调函数类型非常重要，因为这有助于强制类型安全并防止潜在问题。

让我们看一个不好的例子，其中很容易出错，因为 TypeScript 不会出错。这里我们使用的回调函数的参数多于提供的参数：

```typescript
function processData(data: string, callback: Function): void {
  // Process data...
  callback(data);
}

processData("a", (b: string, c: string) => console.log(b + c)); // #A
```

`#A No TypeScript error and the output is "aundefined"`

在上面的示例中，TypeScript 将无法捕获与回调函数相关的任何错误，因为它被定义为通用函数。 TypeScript 会提醒我们回调函数类型不匹配的一个很好的例子需要正确定义回调函数类型：

```typescript
type DataCallback = (data: string) => void;           // #A

function processData(data: string, callback: DataCallback): void {
  // Process data...
  callback(data);
}

processData("a", (b: string) => console.log(b));      // #B
processData("a", (b: string, c: string) => console.log(b + c)); // #C
```

`#A Properly defined callback function type`
`#B Ok: no errors and the usage as it should be`
`#C Wrong usage and hence we get Error: Argument of type '(b: number, c: number) => void' is not assignable to parameter of type 'DataCallback'`

在好的示例中，我们定义了一个 DataCallback 函数类型，指定回调函数的预期输入和输出类型，确保类型安全。当然，我们可以内联定义回调函数类型，而不需要额外的 DataCallback 类型，如下所示：

### 4.2.2. 参数类型不一致

定义回调的函数类型时，确保参数类型在应用程序中保持一致至关重要。这有助于避免混乱和潜在的运行时错误。

下面是一个示例，其中我们有两个类用于 apiCall 函数的回调。但在实际的 apiCall 中，我们只使用其中一种，而不是同时使用两种类型。这使得函数参数 success 与定义的类型（可以在代码中的其他地方使用）不一致，进而可能导致错误。所以，这是一个不好的例子：

```typescript
type SuccessCallback = (result: string) => void;
type FailureCallback = (error: string) => void;
function apiCall(success: (data: any) => void, failure: FailureCallback) {
  // Implementation...
}
```

正如您所看到的，SuccessCallback 表示一个函数，该函数接受一个字符串类型的参数，并且不返回任何内容（void）。另一方面，第一个参数 success 是一个函数，它接受一个类型为 any 的参数并且不返回任何内容。它旨在成为 API 调用成功时调用的回调函数。让我们用一个很好的例子来解决这个问题：

```typescript
type SuccessCallback = (result: string) => void;
type FailureCallback = (error: string) => void;
function apiCall(success: SuccessCallback, failure: FailureCallback) {
  // Implementation...
}
```

通过一致地使用定义的回调函数类型，您可以确保您的代码更易于维护并且更不容易出错。

### 4.2.3. 回调缺乏清晰度

当您不使用函数类型进行回调时，预期的输入和输出可能不清楚，从而导致混乱和潜在的错误。

下面是一个次优示例，它定义了一个名为 processData 的函数，该函数采用两个参数。第一个参数 data 应该是一个字符串。这可以是需要处理的任何类型的数据，可能是文件内容、API 响应或表示为字符串的任何数据。第二个参数回调是一个函数。这是 Node.js 和 JavaScript 中处理异步操作的常见模式。在这种情况下，回调函数本身接受两个参数：

- error：它要么是 Error 对象（如果在数据处理过程中发生错误），要么是 null（如果没有发生错误）。

- result：它是一个字符串（表示已处理的数据）或 null（如果没有结果返回，可能是由于错误）。

在 processData 函数内部，我们通过将数据参数转换为大写来“处理”它（也许还可以做更多事情），一旦完成，我们将调用回调函数，向其传递错误（如果没有错误则传递 null）和结果（如果没有结果则为 null）：

```typescript
function processData(
  data: string,
  callback: (                               // #A
  error: Error | null,
  result: string | null
) => void) {
  let processedData = null;
  try {                                     // #B
    processedData = data.toUpperCase();     // #C
    callback(null, processedData);
  } catch (error) {
    callback(error, null);
  }
}
```

`#A Defining type inline for the callback`
`#B Hypothetical processing operation`
`#C Convert the data to uppercase`

假设代码中的许多其他地方都会遇到此回调。因此，一个更优化的示例将包括一个新的类型别名 ProcessDataCallback 以提高代码重用：

```typescript
type ProcessDataCallback = (                // #A
  error: Error | null,
  result: string | null
) => void;

function processData(                       // #B
  data: string,
  callback: ProcessDataCallback
) {
  // ...Process data and invoke the callback
}
```

`#A Defining type alias for the callback`
`#B Function using the type alias`

当然，正如我们在前面的章节中所看到的，对于这种情况，使用接口代替类型别名也是可行的。要将给定的函数和类型转换为接口，您可以为回调定义一个接口，然后在函数签名中使用该接口。它看起来是这样的：

```typescript
interface ProcessDataCallback {             // #A
  (error: Error | null, result: string | null): void;
}

function processData(                       // #B
  data: string, 
  callback: ProcessDataCallback) { 
  // ...Process data and invoke the callback
}
```

`#A Defining the interface for the callback`
`#B Function using the interface`

此结构允许与具有类型别名的原始版本相同的功能和类型安全性，但使用接口，这在某些编码风格或在更复杂的场景中扩展类型可能是首选。

总而言之，在 TypeScript 中使用函数类型进行回调对于在代码库中提供类型安全性、一致性和可维护性至关重要。依靠为回调定义适当的函数类型（内联或作为单独的类型别名）来防止潜在问题并创建更健壮的应用程序。通过使用回调函数类型（内联、别名或接口），我们使代码更加明确且更易于理解。

## 4.3. 滥用可选函数参数

TypeScript 中的可选参数是一个强大的功能，它允许您创建更灵活、更简洁的函数。然而，它们有时可能被滥用，导致潜在的问题和意外的行为。在本节中，我们将探讨开发人员在 TypeScript 中使用可选参数时常犯的错误以及如何避免这些错误。

让我们从使用可选参数的错误开始，因为默认参数更合适。默认参数是在没有提供值的情况下我们设置值的参数。可选参数可能会导致函数内部出现不必要的条件逻辑来处理未提供参数的情况，而默认参数则在函数签名中以简洁的方式设置值。下面的代码是带有可选参数但没有默认参数的代码，正如您所看到的，它需要一个额外的“if/else”之类的逻辑来正确处理超时：

```typescript
function fetchData(url: string, timeout?: number) {
  const actualTimeout = timeout ?? 3000;             // #A
}
```

`#A Default to 3000ms if timeout is not provided and fetch data with the actualTimeout`

我们使用可选参数来设置超时，如果未提供，则默认为 3000。接下来让我们看看默认参数如何改变我们的小例子。相反，我们可以使用默认参数来更简洁地实现相同的效果（一个额外的好处是我们可以删除 :number 类型注释，因为 TypeScript 会推断它）：

```typescript
function fetchData(url: string, timeout = 3000) {    // #A
  // ...
}
```

`#A Fetch data with the default timeout parameter`

之后，我们可以更深入地了解隐式未定义值的依赖。使用可选参数时，必须了解，默认情况下，如果未提供参数，它们将被隐式分配为 undefined 值。如果您的代码没有显式检查，这可能会导致意外行为。为了避免此问题，请显式处理未定义的值或为可选参数提供默认值（如前所示）。

考虑以下函数 createPerson 对象的情况，其中未定义的姓氏和年龄值可能会导致问题：

```typescript
function createPerson(firstName: string, lastName?: string, age?: number) {
  const person = {
    fullName: `${firstName} ${lastName}`,     // #A
    isAdult: age > 18,                        // #B
  };
  return person;
}

const person1 = createPerson("Pavel", "Ivanov", 30);
const person2 = createPerson("Kim", undefined, 16);
const person3 = createPerson("Satish", "");

console.log(person1);                         // #C
console.log(person2);                         // #D
console.log(person3);                         // #E
```

`#A No TypeScript warning/error when trying to use optional parameter that's possibly undefined`
`#B Problematic usage of implicit undefined value with TypeScript error: 'age' is possibly 'undefined'`
`#C Correct { fullName: 'Pavel Ivanov', isAdult: true }`
`#D Incorrect last name { fullName: 'Kim undefined', isAdult: false }`
`#E We don't know if Satish is an adult or not { fullName: 'Satish ', isAdult: false }`

在此示例中，隐式未定义值的使用存在问题是在检查年龄是否大于 18 时。由于未定义为假，因此比较未定义> 18 的计算结果为假。虽然这在这种特殊情况下可能有效，但在更复杂的情况下可能会引入错误。是的，我们确实有一个 TypeScript 错误警告，年龄可能是未定义的；您甚至可能会说，因为有警告，该错误很容易注意到并修复，这不是问题。为什么要在这个话题上浪费纸张呢？我同意你的观点，但是因为代码仍然可以编译和运行，所以_仅_如果开发人员没有其他可以隐藏此特定错误的错误，_并且_如果该开发人员遵守修复所有问题的纪律，则可以发现并修复该错误错误（我们都应该如此）。因此，值得强调错误的可行来源。

更好的方法是显式检查未定义以适当处理它（不适用）或提供年龄的默认值，从而提供 isAdult 的默认值。这是一个带有三元表达式 Age !== undefined 的示例：

```typescript
function createPerson(firstName: string, lastName?: string, age?: number) {
  const person = {
    fullName: lastName ? `${firstName} ${lastName}` : firstName,
    isAdult: age !== undefined ? age > 18 : "N/A", // #A
  };
  return person;
}

const person1 = createPerson("Pavel", "Ivanov", 30);
const person2 = createPerson("Kim", undefined, 16);
const person3 = createPerson("Satish");

console.log(person1); // #B
console.log(person2); // #C
console.log(person3); // #D
```

`#A Added an undefined check`
`#B Correct: { fullName: 'Pavel Ivanov', isAdult: false }`
`#C Correct: { fullName: 'Kim', isAdult: false }`
`#D Correct: { fullName: 'Satish', isAdult: 'N/A' }`

请注意，如果我们只使用真实检查 isAdult: (age) ? age > 18 : 'N/A'，那么所有年龄小于 1 岁（年龄为 0）的婴儿将被错误地假定为未确定（N/A），而实际上它们应该是 isAdult: false。这是因为 true 检查将值 0、NaN、falsy 和空字符串视为 falsy，而实际上它们可以是有效值（就像婴儿的 0 岁）。

最后，将必需参数放在可选参数之后是一种与可选参数相关的错误。尽管这是一个非常隐蔽的错误（即很难不注意到），因为如果我们尝试编写如下内容，TypeScript 会警告我们：

```typescript
function fetchData(
  url: string,
  timeout?: number,
  callback: () => void // #A
) { 
  // Fetch data and call the callback
}
```

`#A Error: A required parameter cannot follow an optional parameter.`

总之，可选参数是 TypeScript 中的一个强大功能，但正确使用它们以避免潜在的问题和混乱至关重要。通过遵循最佳实践（例如对参数进行排序、在适当的时候使用默认参数以及显式处理未定义的值），您可以在 TypeScript 代码中创建更灵活、更可靠的函数。

## 4.4. 休息参数使用不足

我们继续关注函数及其带有剩余参数的签名。它们是 TypeScript（和 JavaScript）中的一项便利功能，允许您将无限数量的参数捕获为数组。但是，剩余参数的不当使用可能会导致代码混乱和潜在问题。在本节中，我们将讨论使用剩余参数时的一些常见错误以及如何避免这些错误。

### 4.4.1. 将剩余参数与可选参数一起使用

第一个剩余错误是将剩余参数与可选参数结合使用。这种组合可能会令人困惑，并可能导致意外的行为。最好避免将剩余参数与可选参数一起使用并寻找替代解决方案。

当忘记可选参数但传递了其余参数时会令人困惑：

```typescript
function sendMessage(to: string, cc?: string, ...attachments: string[]) {
  console.log("to:", to, "cc: ", cc, "attachments: ", ...attachments);
}

sendMessage("a@qq.com");
sendMessage("a@qq.com", "b@qq.com");
sendMessage("a@qq.com", "attachment1", "attachment2");            // #A
sendMessage("a@qq.com", undefined, "attachment1", "attachment2"); // #B
```

`#A cc becomes "attachment1", and attachments has only ["attachment2"]`
`#B this is how it really should be called`

更好的方法是使用函数的对象参数（参数）而不是多个参数。这对于复杂的情况很有帮助，例如具有多个可选参数（包括休息）。在参数类型中，我们可以指定可选参数（类型的属性）：

```typescript
function sendMessage(params: {
  to: string;
  cc?: string;
  attachments?: string[];
}) {
  console.log(params);
}

sendMessage({         // #A
  to: "a@qq.com",    
}); 

sendMessage({         // #B
  to: "a@qq.com",     
  cc: "b@qq.com",
}); 

sendMessage({         // #C
  to: "a@qq.com",
  attachments: ["attachment1", "attachment2"],
}); 

sendMessage({         // #D
  to: "a",
  cc: "b",
  attachments: ["attachment1", "attachment2"],
}); 
```

`#A Just "to" is okay`
`#B "to" and "cc" is okay too`
`#C "to" without "cc" but with "attachments" is fine`
`#D Everything provided is fine too`

### 4.4.2. 使用正确的类型

剩余参数有时可能会与数组参数混淆，这可能会导致意外的行为。其余参数将各个参数收集到数组中，而数组参数则接受数组作为参数。确保根据您的要求使用正确的参数类型。

定义剩余参数的正确方法是使用数组，即在类型后面使用单个方括号，例如 string[]。这样，其余参数（例如 messages）是一个字符串数组，而参数则用逗号逐一传递：

```typescript
function logMessages(...messages: string[]) {
  // ...
}

logMessages("1", "2", "3", "a", "b", "c");      // #A
```

`#A Ok: Proper rest parameter usage with passing multiple parameters of type string`

我们还可以混合使用多种类型的参数，如下所示：

```typescript
function logMessages(...messages: (string |number)[]) {
  console.log(messages)

}

logMessages(1,2,3, 'a', 'b', 'c')
```

以下示例显示了*不正确的*用法和字符串数组类型数组 (string[][])：

```typescript
function logMessages(...messages: string[][]) {
  console.log(messages)
}

logMessages('1','2','3', 'a', 'b', 'c')         // #A
```

`#A Error: Argument of type 'string' is not assignable to parameter of type 'string[]'.`

在本节的开头，我说过剩余参数是一个方便的 JavaScript/TypeScript 功能。然而如今，虽然我仍然在一些需要（想要？）支持可变数量参数的库中看到它，但我很少看到它在应用程序代码中使用。此类库的示例有 lodash、D3 等。相反，在应用程序代码中，更流行的是使用数组参数，例如，

```typescript
function logMessages(messages: string[]) {
  // ...
}

logMessages(["1", "2", "3", "a", "b", "c"]);    // #A
```

`#A Invoked properly as single array of many string items`

我也倾向于更喜欢数组参数而不是其余参数，因为这种方法更强大（正如我们在可选参数的错误中看到的那样）。这引导我们进入下一个主题。

### 4.4.3. 使函数签名不必要地复杂化

使用剩余参数时的一个错误是使函数签名比需要的更加复杂。例如，考虑以下函数，它接受任意数量的字符串并将它们连接起来：

```typescript
function concatenateStrings(...strings: string[]): string {
  return strings.join("");
}
```

虽然此函数可以正常工作，但接受字符串数组而不是使用剩余参数可能更直接。通过接受数组，函数签名变得更加简洁且易于理解：

```typescript
function concatenateStrings(strings: string[]): string {
  return strings.join("");
}
```

虽然剩余参数可能很有用，但过度使用它们可能会导致函数过于灵活，难以理解和维护。具有大量剩余参数的函数可能难以推理，并且可能需要额外的文档或注释来解释其行为。

### 4.4.4. 过度使用休息参数

另一个错误是过度使用剩余参数，尤其是当函数只需要有限数量的参数时。这可能会导致难以理解函数的用途并增加出错的可能性。

```typescript
function createProduct(
  name: string,
  price: number,
  ...attributes: string[]
) {
  // Function implementation
}
```

在此示例中，createProduct 函数使用产品属性的剩余参数。然而，如果函数只需要一些特定的属性，那么最好对这些属性使用单独的参数或对象：

```typescript
function createProduct(
  name: string,
  price: number,
  color: string,
  size: string
) {
  // Function implementation
}
```

或者，使用嵌套属性：

```typescript
function createProduct(
  name: string,
  price: number,
  attributes: {
    color: string;
    size: string;
  }
) {
  // Function implementation
}
```

一般来说，最好谨慎使用剩余参数，并且仅当它们显着提高代码的清晰度或灵活性时才使用它们。通过在使用剩余参数时注意这些常见错误并遵循最佳实践，您可以在 TypeScript 代码中创建更灵活、更清晰的函数。

## 4.5. 不明白这一点

让我们从一个笑话开始吧。有时，当我用 JavaScript 编码时，我会感到想要放弃并感叹：“这太荒谬了！”但我总是忘记“这个”到底代表什么。 :-)

事实上，TypeScript 中的 this 和 JavaScript 中一样，指的是当前作用域的上下文。它在方法内部使用来引用该函数所属的对象。当您调用对象的方法时，该对象将作为 this 传递到该方法中。然而，这在 JavaScript 中有时会以不可预测的方式表现，特别是当函数作为参数传递或用作事件处理程序时。 TypeScript 允许您在函数签名中指定 this 的类型，从而帮助解决这些困难。

这些示例说明了如何在 TypeScript 中正确使用它。

您可以在类中使用 this 来引用该类：

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;                                 // #A
  }

  sayHello() {
    console.log(`Hello, my name is ${this.name}`);    // #B
  }
}

const person = new Person("Irina");
person.sayHello();                                    // #C
```

`#A 'this' refers to the instance of the class`
`#B 'this' refers to the instance of the class`
`#C Outputs: "Hello, my name is Irina"`

在 Person 中，我们用字符串类型定义了属性名称。然后我们在构造函数（初始化程序）中使用 this.name 设置 name 的值，以便在 Person 实例化期间将属性 name 设置为传递给 new Person() 的值。相同的方法可以用于其他方法，而不仅仅是构造函数。

在 JavaScript/TypeScript 中，您可以在（胖）箭头函数中使用 this。 （粗箭头函数一词来自我喜欢的 CoffeeScript，我们还有一个 _thin_ 箭头函数 -> 遗憾的是 JavaScript/TypeScript 中不存在该函数。）箭头函数没有自己的 this 上下文，因此 this 在箭头函数指的是来自周围范围的this。换句话说，箭头函数将 this“锁定”到上下文中，因为它是在代码中编写的，而不是在执行时。至少我记得是这样的。这对于事件处理程序和其他基于回调的代码非常有用，这些代码由于 JavaScript 事件循环或浏览器 DOM 的执行方式而与编写它们的上下文“分离”。例如，我们都知道 setTimeout 函数，并且事件循环稍后会调用它。此代码片段尝试从 setTimeout 中（成功）访问对象属性 (this.name)：

```typescript
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  waitAndSayHello() {
    setTimeout(() => {                                // #A
      console.log(`Hello, my name is ${this.name}`);  // #B
    }, 1000);
  }
}

const person = new Person("Elena");
person.waitAndSayHello();                             // #C
```

`#A Arrow function to "keep" the value of this`
`#B 'this' refers to the instance of the class, not the setTimeout function`
`#C Outputs: "Hello, my name is Elena" after 1 second`

在此示例中，如果我们对 setTimeout 回调使用常规函数，则 this.name 将是未定义的，因为 setTimeout 中的 this 引用全局范围（或​​者在严格模式下未定义）。但是，因为我们使用了箭头函数，所以 this 仍然引用 Person 类的实例。

在 TypeScript 中，但不是 JavaScript，您可以在函数签名中使用 this。事实上，如果函数使用 this（设置或访问 this），最好的做法是在函数签名中指定此类型。例如，我们有一个greet函数，它要求它具有特定的形状：

```typescript
function greet(this: { name: string }) {
  console.log(`Hello, my name is ${this.name}`);
}

const person = {
  name: "Nikolai",
  greet,        // #A
};

person.greet(); // #B
```

`#A Compact object notation for greet: greet`
`#B Outputs: "Hello, my name is Nikolai"`

在此示例中，我们指定这应该是具有 name 属性的对象。如果我们尝试对没有名称的对象调用greet()，TypeScript 将抛出错误。

最后但并非最不重要的一点是，您可以在 TypeScript 接口中利用它来引用当前类型。考虑以下示例，该示例实现了选项方法的方法链接。

```typescript
interface Chainable {
  option(key: string, value: any): this;
}

class Config implements Chainable {
  options: Record<string, any> = {};
  option(key: string, value: any): this {
    this.options[key] = value;
    return this;
  }
}

const config = new Config();
config.option("user", "Ivan").option("role", "admin");    // #A
```

`#A method chaining works because 'option' returns 'this'`

在此示例中，Chainable 接口中的选项定义为返回 this，这意味着它返回该类的当前实例。这允许方法链接，您可以在同一对象上调用一个又一个方法。我们还可以将函数返回类型设置为 Config 而不是 this，但选项方法中的实际返回必须是 this 而不是其他。

值得注意的是，这支持类中的多态行为。当基类中的方法引用“this”，并且从子类的实例调用该方法时，“this”引用子类的实例（对象）。这确保了访问正确的方法或属性，即使它们在子类中被覆盖或扩展，也允许动态分派方法调用。另外，在 TypeScript 中，子类化时，需要使用 super() 调用基类的构造函数。在子类的构造函数中，在调用 super() 之前不能使用“this”，因为必须首先执行基类的构造函数以确保对象正确初始化。在 super() 调用之后，this 指的是新的子类实例，已使用基类属性完全初始化，并且可用于进一步修改或设置子类实例。

```typescript
class SubConfig extends Config {
  appName: string;

  constructor(appName: string) {
    super();
    this.appName = appName;
  }
}

const mcFlurry = new SubConfig("McFlurry");
mcFlurry.option("user", "Ivan");
```

始终注意您使用它的上下文。如果在不同的上下文中调用使用此方法的方法（例如作为回调传递），这可能不是您所期望的。为了缓解这种情况，您可以将该方法绑定到此：

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
    this.sayHello = this.sayHello.bind(this);     // #A
  }

  sayHello() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

let sayHelloFn = new Person("Ivan").sayHello;
sayHelloFn();                                     // #B
```

`#A bind sayHello to the instance of the class, without it error: undefined is not an object`
`#B Outputs: "Hello, my name is Ivan"`

在前面的示例中，即使在全局上下文中调用 sayHello，它仍然正确引用 Person 类的实例，因为我们在构造函数中绑定了 this。

请记住，在类属性中使用箭头函数时，此绑定不是必需的，因为箭头函数不会创建自己的 this 上下文：

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  sayHello = () => {    // #A
    console.log(`Hello, my name is ${this.name}`);
  };
}

let sayHelloFn = new Person("Ivan").sayHello;
sayHelloFn();           // #B
```

`#A Using an arrow function`
`#B Outputs: "Hello, my name is Ivan"`

在上述示例中，sayHello 是一个箭头函数，因此它使用 Person 实例中的 this，而不是调用它的地方。这是在构造函数中显式绑定 this 的首选方法。我非常喜欢这种技术！

更重要的是，还有全局的 this，但它值得有自己的部分，我稍后会介绍它。

### 4.5.1. 错误地利用 ThisParameterType 来提高 this 上下文的类型安全性

在本节中，我们将探索名为 ThisParameterType 的高级 TypeScript 功能，该功能在处理函数或方法中的 this 上下文时提供增强的类型安全性。 TypeScript 提供了一个名为 ThisParameterType 的内置实用程序类型，它允许我们在函数或方法签名中提取 this 参数的类型。

当使用依赖于适当 this 上下文的函数或方法时，确保类型安全以防止潜在的运行时错误至关重要。通过利用 ThisParameterType，我们可以在开发过程中强制正确使用此上下文，从而在任何潜在问题发生之前捕获它们。

因此，TypeScript 中的 ThisParameterType 实用程序类型使我们能够在函数或方法签名中提取 this 参数的类型。通过使用 ThisParameterType，我们可以显式指定预期的 this 上下文类型，从而提供改进的类型安全性并防止潜在的运行时错误。定义依赖于特定 this 上下文的函数或方法时，请考虑使用 ThisParameterType 来确保准确键入并强制正确使用。

这是一个次优的示例，其中 this 在函数引入中隐式使用，并且 this 的类型为any，并且它没有类型注释。我们还使用对象字面量来创建具有此函数的对象 person，从某种意义上说，它变成了 person.introduce 方法。因此，向该方法提供必要的参数 name 和age：

```typescript
function introduce(): void {  // #A
  console.log(`Hi, my name is ${this.name} and I am ${this.age} years old.`); // #A
}

const person = {
  name: "Arjun",
  age: 30,
  introduce,
};

person.introduce();           // #B
```

`#A Function outside of our code that we cannot modify`
`#B Error: 'this' implicitly has type 'any' because it does not have a type annotation.`

上面的代码不是最理想的，因为我们将其作为any，并且因为如果有人尝试（错误地）使用不同的上下文调用该方法，我们不会看到任何问题，直到为时已晚。例如，这条未传递正确名称和年龄的语句将导致运行时错误，但不会导致 TypeScript 错误：

```typescript
person.introduce.call({});
```

一个更优化的示例将为此提供类型注释和一个用于增加类型安全性的接口 Person：

```typescript
function introduce(this: { name: string; age: number }): void {
  console.log(`Hi, my name is ${this.name} and I am ${this.age} years old.`);
}

interface Person {
  name: string;
  age: number;

  introduce(this: { 
    name: string; 
    age: number 
  }): void;
}

const person: Person = {
  name: 'Arjun',
  age: 30,
  introduce,
};

person.introduce();                     // #A
person.introduce.call({});              // #B
```

`#A Hi, my name is Arjun and I am 30 years old.`
`#B Error: Argument of type '{}' is not assignable to parameter of type '{ name: string; age: number; }'.`

但现在让我们记住，我们还有一个名为 ThisParameterType 的实用程序。它允许我们提取这个。因此，最优化（也是类型安全）的示例将使用 ThisParameterType 来避免在 Person 类型中重复名称和年龄的类型定义（请注意 & 的使用）：

```typescript
function introduce(this: { name: string; age: number }): void {
  console.log(`Hi, my name is ${this.name} and I am ${this.age} years old.`);
}

type introduceType = typeof introduce;
type introduceContext = ThisParameterType<introduceType>;

type Person = {     // #A

  introduce(this: introduceContext): void;
  email: string;    // #B
} & introduceContext;

const person: Person = {
  name: "Arjun",
  age: 30,
  email: "arjun@hotmail.com",
  introduce,
};

person.introduce();         // #C
person.introduce.call({});  // #D
```

`#A Type that has properties of this from introduce and introduce method`
`#B We can add extra properties to Person type, it doesn't have to be exactly as introduce context`
`#C Hi, my name is Arjun and I am 30 years old.`
`#D Argument of type '{}' is not assignable to parameter of type '{ name: string; age: number; }'.`

或者为了简洁（但可读性较差），我们可以像这样组合类型：

```typescript
type Person = {
  introduce(this: ThisParameterType<typeof introduce>): void;
} & ThisParameterType<typeof introduce>;
```

### 4.5.2. 不使用 OmitThisParameter 删除它

OmitThisParameter 是 TypeScript 中的一个实用程序类型，它从函数类型中删除 this 参数（如果存在）。当您处理具有 this 参数的函数，但您希望将其传递给某些不需要 this 参数的代码时，这非常有用。

例如，考虑一个包含 this 参数的函数类型：

```typescript
type MyFunctionType = (this: string, foo: number, bar: number) => void;
```

如果您尝试在不需要 this 参数的上下文中使用此函数，您将收到类型错误：

```typescript
function callFunction(fn: (foo: number, bar: number) => void) {
  fn(1, 2);
}

let myFunction: MyFunctionType = function (foo: number, bar: number) {
  console.log(this, foo, bar);
};

callFunction(myFunction);   // #A
```

`#A Error: Types of parameters '__0' and 'foo' are incompatible`

在这里，callFunction 需要一个带有两个数字参数的函数，但 myFunction 包含一个 this 参数，因此它不兼容。

您可以使用 OmitThisParameter 删除此参数：

```typescript
function callFunction(fn: OmitThisParameter<MyFunctionType>) {
  fn(1, 2);
}

let myFunction: MyFunctionType = function (foo: number, bar: number) {
  console.log(this, foo, bar);
};

callFunction(myFunction);   // #A
```

`#A No error`

这里，OmitThisParameter<MyFunctionType> 是一个相当于 (foo: number, bar: number) => void 的类型。这意味着您可以将 myFunction 传递给 callFunction 而不会出现任何类型错误。

请注意，OmitThisParameter 实际上并不改变 myFunction 的行为。当调用 myFunction 时，this 将是未定义的，因为 callFunction 调用 fn 时没有指定 this 值。如果 myFunction 依赖 this 作为字符串，您需要确保使用正确的 this 值调用它。

总之，在 TypeScript 中使用它需要理解它在 JavaScript 中的行为并利用 TypeScript 的功能来避免常见错误。通过声明 this 的类型，您可以避免许多常见错误，并使您的代码更加健壮且更易于理解。如果您可以避免使用它，也许您应该这样做，因为仍然有很多开发人员对它本质上感到困惑。 （取而代之的是，我们可以重写基于类的代码，以使用更多函数风格的代码，其中包含普通函数、闭包或通过函数参数显式传递的数据。）

### 4.6. 不知道 call、bind、apply 和 strictBindCallApply

在使用此上下文时，绑定、调用和应用非常有用。这是一个显示所有三个的示例。我们创建一个具有greet和greetWithMood函数的Person类。这两个函数都用到了这个。通过利用绑定、调用和应用，我们可以“改变” this 的值。

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;   // #A
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }

  greetWithMood(mood: string) {
    console.log(
      `Hello, my name is ${this.name}, and I'm currently feeling ${mood}`
    );
  }
}

let tim = new Person("Tim");
let alex = new Person("Alex");

tim.greet.call(alex);                       // #B
tim.greetWithMood.apply(alex, ["happy"]);   // #C

let boundGreet = tim.greet.bind(alex);      // #D
boundGreet();                               // #E
```

`#A 'this' refers to the instance of the class`
`#B Use call: calls greet with 'this' set to alex; Outputs: "Hello, my name is Alex"`
`#C Use apply: calls greetWithMood with 'this' set to alex and arguments as an array; Outputs: "Hello, my name is Alex, and I'm currently feeling happy"`
`#D Use bind: creates a new function with 'this' set to alex`
`#E Outputs: "Hello, my name is Alex"`

在这个例子中：

- call 是一种使用给定的 this 值和单独提供的参数来调用函数的方法。

- apply 与 call 类似，但它需要一个类似数组的参数对象。

-bind 创建一个新函数，在调用该函数时，将其 this 关键字设置为提供的值。

和以前一样，关键思想是我们能够调用属于 Person (Tim) 的一个实例的方法，并将其上下文更改为 Person (Alex) 的另一个实例。

TypeScript 3.2 引入了 strictBindCallApply 编译器选项，为绑定、调用和应用提供更严格的检查：

```typescript
function foo(a: number, b: string): string {
  return a + b;
}

let a = foo.apply(undefined, [10]);         // #A

let b = foo.call(undefined, 10, 2);         // #B

let c = foo.bind(undefined, 10, "hello")(); // #C
```

`#A Error: Expected 2 arguments, but got 1`
`#B Error: Argument of type '2' is not assignable to parameter of type 'string'`
`#C OK`

在此示例中，TypeScript 检查传递给 apply、call 和 bind 的参数是否与原始函数的参数匹配。

## 4.7. 不知道 globalThis

历史上，在 JavaScript 中访问全局对象一直是一件很混乱的事情。如果您在网络上，则可以使用窗口、自我或框架 - 但如果您使用网络工作者，则只能使用自我。那么在 Node.js 中呢？这些都行不通。你必须使用全局代替。您可以在非严格函数中使用 this 关键字，但在模块和严格函数中则不能使用 this 关键字。

让我们通过几个例子来看看这些。在 TypeScript（和 JavaScript）中，this 关键字的行为有所不同，具体取决于使用它的上下文。在全局范围内，this指的是全局对象。在浏览器中，全局对象是 window，因此在浏览器上下文中，全局级别的 this 将引用 window 对象：

```typescript
console.log(this === window);   // #A
```

`#A logs 'true' in a browser context`

在 Node.js 中，情况有点不同。 Node.js 遵循 CommonJS 模块系统，Node.js 中的每个文件都是它自己的模块。这意味着顶层的 this 不是指全局对象（在 Node.js 中是全局的），而是指当前模块的导出，默认是一个空对象。所以，在 Node.js 中：

```typescript
console.log(this === global);   // #A
console.log(this);              // #B
```

`#A logs 'false'`
`#B logs '{}' or { exports: {} }`

但是，在不属于任何对象的函数内部，this 默认为全局对象，除非该函数处于严格模式，在这种情况下，this 将是未定义的。这是一个例子：

```typescript
function logThis() {
  console.log(this);
}

logThis();                      // #A

function strictLogThis() {
  "use strict";
  console.log(this);
}

strictLogThis();                // #B
```

`#A logs 'global' in Node.js, 'window' in browser (if not in strict mode)`
`#B logs 'undefined'`

在 TypeScript 中，您可以在全局范围内使用它，但如果可能的话通常最好避免它，因为它可能会导致代码混乱。通常最好使用特定的全局变量，例如 window 或 global，或者完全避免全局状态。它的行为是 JavaScript 和 TypeScript 中更复杂的部分之一，理解它可以帮助避免许多常见的错误。

输入全局此。无论您身在何处，这都是获取全局 this 值（以及全局对象本身）的非常可靠的方法。与 window 和 self 不同，无论您是否在窗口上下文中（如 Node），它都可以正常工作。因此，您可以访问全局对象，而不必担心代码所在的环境。有什么简单的方法可以记住名称？就想“在全球范围内，这就是全球这个”。繁荣。

因此，在 JavaScript 中，globalThis 是一个全局属性，它提供了一种跨不同环境（包括浏览器、Node.js 和 Web Workers）访问全局范围（“全局对象”）的标准方法。这使得编写可在不同环境中运行的可移植 JavaScript 代码变得更加容易。在 TypeScript 中，您可以以相同的方式使用 globalThis。但是，由于 globalThis 是只读的，因此您不能直接覆盖它。您可以做的就是向 globalThis 添加新属性。

例如，如果向 globalThis 添加新属性，您将得到 Element 隐式具有“any”类型，因为类型“typeof globalThis”没有索引签名：

```typescript
globalThis.myGlobalProperty = "Hello, world!";
console.log(myGlobalProperty);    // #A
```

`#A Logs: "Hello, world!"`

如果您尝试 window.myGlobalProperty，那么您将得到“Property 'myGlobalProperty' does not exit on type 'Window & typeof globalThis'”。我们需要做的是声明类型：

```typescript
// typings/globals.d.ts (depending on your tsconfig.json)

export {};           // #A

interface Person {   // #B
  name: string;
}

declare global {
  var myGlobalProperty: string;
  var globalPerson: Person;
}
```

`#A We need this to make the file into a module`
`#B instances of a class can be global properties too`

上面的代码添加了以下类型：

```typescript
myGlobalProperty;
window.myGlobalProperty;
globalThis.myGlobalProperty;
globalPerson.name;
window.globalPerson.name;
globalThis.globalPerson.name;
```

在此示例中，声明 global 使用新变量 myGlobalProperty 扩展了全局范围。在此声明之后，您可以将 myGlobalProperty 添加到 globalThis，而不会出现任何类型错误。

请记住，修改全局范围可能会导致代码混乱，并且通常被认为是不好的做法。它可能会导致与其他脚本和库的冲突，并使代码更难以测试和调试。通常最好使用模块和本地作用域。但是，如果您有修改全局作用域的合法用例，TypeScript 提供了以类型安全的方式完成此操作的工具。

在 TypeScript 和 JavaScript 中，globalThis 的另一个常见用途是检查全局变量是否存在。例如，在浏览器环境中，您可能想检查 fetch 是否可用：

```typescript
if (!globalThis.fetch) {
  console.log("fetch is not available");
} else {
  fetch("https://example.com")
    .then((response) => response.json())
    .then((data) => console.log(data));
}
```

在这个例子中，globalThis.fetch指的是fetch函数，它是现代浏览器中的全局变量。如果 fetch 不可用，代码会向控制台记录一条消息。如果 fetch 可用，代码会发出 fetch 请求。

这对于功能检测非常有用，您可以在使用某些 API 之前检查它们是否可用。这有助于确保您的代码可以在不同的环境中运行。

请记住，如果可以的话，最好避免修改全局范围，并负责任地使用 globalThis。修改全局范围可能会导致与其他脚本和库发生冲突，并使您的代码更难以测试和调试。通常最好使用模块和本地作用域。在现代 JavaScript 和 TypeScript 开发中，模块提供了一种更好、更灵活的方式来在应用程序的不同部分之间共享代码。

## 4.8. 忽略对象类型中的函数签名

我们可以在对象类型中定义函数签名。这意味着该对象可以作为函数调用，并指定该函数的参数类型和返回类型。本质上，调用签名直接在对象类型的结构中定义了如何调用函数以及它返回什么。调用签名的编写方式与函数声明类似，但没有函数名称。它由参数列表周围的一组括号组成，后跟冒号和返回类型。这是一般结构（也可以是接口）：

```typescript
type FunctionSignatureObjectType = {
  // ... Some properties
  (param1: Type1, param2: Type2, ...): ReturnType;        // #A
};
```

`#A Function signature`

为了更好地理解这一点，让我们看一下对象类型 Greeter 中的函数签名示例：

```typescript
type Greeter = {
  (name: string): string;           // #A
};

const sayHello: Greeter = (name) => `Hello, ${name}!`;          // #B
console.log(sayHello("Alisa"));     // #C
```

`#A This is the call signature within the object type`
`#B A function that meets the type definition of Greeter`
`#C Using the function; output: Hello, Alisa!`

在此示例中，Greeter 是具有调用签名的对象类型。它指定 Greeter 类型的任何对象实际上都是一个接受单个字符串参数并返回字符串的函数。然后定义 sayHello 函数以匹配此调用签名。它接受一个字符串_name_并返回一条问候消息，也是一个字符串。

此外，在 TypeScript 中，您可以定义具有属性和调用签名的对象类型。这意味着该对象可以具有常规属性（如数字、字符串等），并且也可以作为函数调用。以下是如何定义和使用此类对象（使用类型别名或接口）的示例：

```typescript
interface UserCreator {
  defaultId: string;                // #A
  defaultName: string;
  (name: string, id: string): User; // #B
}

interface User {
  name: string;
  id: string;
}
```

`#A Properties`
`#B Call signature`

在此示例中，UserCreator 是一个对象类型，具有两个属性（defaultId 和 defaultName）以及一个调用签名，该调用签名在使用名称和 id 调用时创建用户。接下来我们来实现一个具体的UserCreator实例：

```typescript
const createUser: UserCreator = function (
  this: UserCreator,                // #A
  name: string,
  id: string
): User {
  return {
    name: name || this.defaultName, // #B
    id: id || this.defaultId,
  };
};

createUser.defaultId = "0000";      // #C
createUser.defaultName = "NewUser";

const user1 = createUser("Alisa", "1234"); // #D
console.log(user1);                // #E

const user2 = createUser("", "");  // #F
console.log(user2);                // #G
```

`#A Defining the type of "this"`
`#B Using this to access class properties`
`#C Assigning properties to the function object`
`#D Using the object`
`#E {name: "Alisa", id: "1234"}`
`#F Uses default values`
`#G {name: "NewUser", id: "0000"}`

在此示例中，UserCreator 类型被定义为一个对象，该对象既充当用户的创建者，又充当默认用户属性的持有者。 createUser 函数是 UserCreator 类型的实例，需要两个参数来返回 User，并且在内部，它使用自己上下文中的 defaultId 和 defaultName，称为 this，它引用函数对象。在使用这些默认属性之前，必须将它们专门分配给 createUser 对象。当调用 createUser 时，就像任何标准函数一样，它会生成新的 User 对象。值得注意的是，如果提供空字符串，createUser 将应用已为其属性​​设置的默认值。这是可行的，因为在 JavaScript 中所有函数都是对象，因此我们可以在函数对象 createUser 上拥有属性。

总而言之，在对象类型中使用函数签名以及附加属性，您可以创建丰富的、有状态的函数对象，以结构化的方式封装行为和数据。这在工厂函数、可配置函数等场景中或者在利用函数灵活性的同时模仿类时特别有用。

## 4.9. 不正确的函数重载

TypeScript 中的函数重载允许您为单个实现定义多个函数签名，从而实现更好的类型安全性和更精确的类型检查。为了实现这一点，创建多个函数签名（通常是两个或更多）并遵循它们来实现函数。

然而，函数重载的不正确使用可能会导致混乱、微妙的错误和代码复杂性的增加。在本节中，我们将讨论使用函数重载时的常见错误，并提供有关如何正确使用它们的指导。

### 4.9.1 使用不匹配的重载签名

创建函数重载时，必须确保提供的签名与实际函数实现相匹配。不匹配的签名可能会导致意外行为和类型错误。不匹配的重载签名：

```typescript
function greet(person: string): string;
function greet(person: string, age: number): string;
function greet(person: string, age?: number): string {
  if (age) {
    return `Hello, ${person}! You are ${age} years old.`;
  }
  return `Hello, ${person}!`;
}

const greeting = greet("Sergei", "Doe"); // #A
```

`#A Error: No overload matches this call`

在上面的示例中，第二个重载签名需要一个数字作为第二个参数，但函数调用传递一个字符串。这会导致类型错误，因为找不到匹配的重载。这可以通过添加匹配的重载签名来修复：

```typescript
function greet(person: string): string;
function greet(person: string, age: number): string;
function greet(person: string, lastName: string): string;     // #A
function greet(person: string, ageOrLastName?: number | string): string {
  if (typeof ageOrLastName === "number") {
    return `Hello, ${person}! You are ${ageOrLastName} years old.`;
  } else if (typeof ageOrLastName === "string") {
    return `Hello, ${person} ${ageOrLastName}!`;
  }

  return `Hello, ${person}!`;
}

const greeting = greet("Sergei", "Doe");                      // #B
```

`#A Added correct overload signature`
`#B No error`

### 4.9.2. 具有类似的过载

类似的重载可能会导致函数签名不明确，并且很难理解在特定上下文中使用哪个签名。

```typescript
function format(value: string, padding: number): string; // #A
function format(value: string, padding: string): string; // #A
function format(value: string, padding: string | number): string {
  if (typeof padding === "number") {
    return value.padStart(padding, " ");
  }

  return value.padStart(value.length + padding.length, padding);
}

const formatted = format("Hello", 5);                    // #B
```

`#A Overlapping overloads`
`#B It works but which overload is used?`

在上面的示例中，两个签名非常相似，因为两者都接受字符串作为第一个参数，而第二个参数具有不同的类型。这可能会导致软件工程师对给定上下文中使用的签名感到困惑。这是因为在调用 format("Hello", 5) 时并不能立即清楚正在使用哪个重载。虽然 TypeScript 编译器可以正确推断类型并使用适当的重载，但这种歧义可能会给尝试理解代码的开发人员造成混乱。

更好的方法是简单地删除重载，如以下代码清单所示：

```typescript
function format(value: string, padding: string | number): string {
  if (typeof padding === "number") {
    return value.padStart(padding, " ");
  }
  return value.padStart(value.length + padding.length, padding);
}

const formatted = format("Hello", 5); // Works!
```

如果需要更多参数，另一种方法是增强重载签名以避免歧义，在这种情况下用字符串填充并指定方向：

```typescript
function format(value: string, padding: number): string; // #A

function format(
  value: string,
  padding: string,
  direction: "left" | "right"
): string;                              // #B

function format(
  value: string,
  padding: string | number,
  direction?: "left" | "right"
): string {
  if (typeof padding === "number") {
    return value.padStart(padding, " ");
  } else {
    if (direction === "left") {
      return padding + value;
    } else {
      return value + padding;
    }
  }
}

const formatted = format("Hello", 5);   // #C
const formattedWithDirection = format("Hello", " ", "right");
```

`#A Padding with a number`
`#B Padding with a string and specifying direction`
`#C No ambiguity`

### 4.9.3. 施加过多的过载

使用太多重载会导致代码复杂性增加并降低可读性。在许多情况下，使用可选参数、默认值或联合类型可以简化函数签名和实现。

```typescript
function combine(a: string, b: string): string; // #A
function combine(a: number, b: number): number; // #A
function combine(a: string, b: number): string; // #A
function combine(a: number, b: string): string; // #A
function combine(a: string | number, b: string | number): string | number {
  if (typeof a === "string" && typeof b === "string") {
    return a + b;
  } else if (typeof a === "number" && typeof b === "number") {
    return a * b;
  } else {
    return a.toString() + b.toString();
  }
}

const result = combine("Hello", 5);             // #B
```

`#A Excessive overloads`
`#B Complex implementation with many overloads`

在上面的示例中，使用四个重载会增加函数的复杂性。通过利用联合类型、可选参数或默认值来简化实现可以提高可读性和可维护性。

可以通过消除重载并使用联合类型简化函数签名来修复过多的重载：

```typescript
function combine(a: string | number, b: string | number): string | number {
  if (typeof a === "string" && typeof b === "string") {     // #A
    return a + b;
  } else if (typeof a === "number" && typeof b === "number") {
    return a * b;
  } else {
    return a.toString() + b.toString();
  }
}

const result = combine("Hello", 5);                         // #B
```

`#A Adding union types`
`#B Simplified implementation with union types`

总之，有效地使用函数重载可以极大地提高 TypeScript 代码中的类型安全性和精度。但是，重要的是要避免常见错误，例如签名不匹配、重叠重载和过度重载，以确保您的代码保持干净、可维护且无错误。

## 4.10. 误用函数类型

TypeScript 允许开发人员定义自定义函数类型，这可以是增强代码一致性和正确性的强大方法。但是，正确使用这些函数类型以避免潜在问题或混淆非常重要。在本节中，我们将讨论函数类型的一些常见误用以及如何避免它们。

### 4.10.1. 使用函数类型重载

函数重载提供了一种为单个实现定义多个函数签名的方法。但是，不支持重载函数类型。相反，使用联合类型来表示不同的可能的输入和输出类型或替代解决方案。

下面是一个带有两个函数签名的类型别名 MyFunction（也可以是接口）的函数重载的错误示例。一个接受数字并返回一个数字。第二个接受字符串并返回一个字符串。然而，此代码会抛出“类型 '(x: string | number, y: string | number) => string | number' 不能分配给类型 'MyFunction'。”：

```typescript
type MyFunction = {
  (x: number, y: number): number;
  (x: string, y: string): string;
};

const myFunction: MyFunction = (x, y) => {  // #A
  if (typeof x === "number" && typeof y === "number") {
    return x + y;
  } else if (typeof x === "string" && typeof y === "string") {
    return x + " " + y;
  }
  throw new Error("Invalid arguments");
};

console.log(myFunction(1, 2));
console.log(myFunction("Hao", "Zhao"));
```

`#A Type '(x: string | number, y: string | number) => string | number' is not assignable to type 'MyFunction'.`

正确的示例应该具有带有联合的类型，其中类型别名 MyFunction 的声明被定义为采用两个参数 x 和 y 的函数类型。每个参数可以是数字或字符串（如 | 所示，表示联合类型）。该函数预计返回一个数字或一个字符串：

```typescript
type MyFunction = (x: number | string, y: number | string) => number | string;
```

我们还可以使用函数声明进行重载（而不是类型）：

```typescript
function myFunction(x: number, y: number): number;
function myFunction(x: string, y: string): string;
function myFunction(x: any, y: any): any {
  if (typeof x === "number" && typeof y === "number") {
    return x + y;
  } else if (typeof x === "string" && typeof y === "string") {
    return x.concat(" ").concat(y);
  }
  throw new Error("Invalid arguments");
}
```

在此版本的代码中，当 x 和 y 是字符串时，函数使用串联方法将它们组合起来，这确保操作被理解为字符串串联，而不是数字加法。

另一个示例将具有单独的函数以避免重载函数（和类型保护）：

```typescript
type MyFunctionNum = {
  (x: number, y: number): number;
};

type MyFunctionStr = {
  (x: string, y: string): string;
};

const myFunctionStr: MyFunctionStr = (x, y) => {
  return x.concat(" ").concat(y);
};

const myFunctionNum: MyFunctionNum = (x, y) => {
  return x + y;
};

myFunctionNum(1, 2);
myFunctionStr("Hao", "Zhao");
```

### 4.10.2. 创建过于复杂的函数类型

为了说明函数类型简单化的好处，让我们看一下这个函数的函数类型示例，该函数可以是基本的计算器运算，其中 a 和 b 是要运算的数字，op 确定要执行哪个操作。如果未提供 op，则该函数可能默认为一项操作，例如加法：

```typescript
type CalculationOperation = (
  a: number,
  b: number,
  op?: "add" | "subtract" | "multiply" | "divide"
) => number;

const complexCalculation: CalculationOperation = (a, b, op = "add") => {
  switch (op) {
    case "add":
      return a + b;

    case "subtract":
      return a - b;

    case "multiply":
      return a * b;

    case "divide":
      return a / b;

    default:
      throw new Error(`Unsupported operation: ${op}`);
  }
};

console.log(complexCalculation(4, 2, "subtract"));  // #A
console.log(complexCalculation(4, 2));              // #B
```

`#A Outputs: 2`
`#B Outputs: 6 (default is 'add')`

此 CalculationOperation 函数类型指定分配给它的函数 (complexCalculation) 应接受两个必需参数 a 和 b，这两个参数都应为 number 类型。此外，它还可以接受可选的第三个参数 op，它是一个字符串，只能是与值对应的四个特定操作之一：“add”、“subtract”、“multiply”或“divide”。这是通过使用联合类型（由 | 字符表示）来实现的，它允许 op 的值成为多种已定义类型之一。最后，函数类型定义还规定该类型的函数应返回数字类型的值。

更好的方法是使用更简单的函数类型，但使用四个不同的函数。这四个函数中的每一个都在 CalculationOperation 中键入。通过使用 CalculationOperation 类型，代码可确保所有这些函数都遵循正确的类型签名。如果这些函数中的任何一个函数实现不正确（例如，如果其中一个函数尝试返回字符串），TypeScript 编译器将引发错误。

```typescript
type CalculationOperation = (a: number, b: number) => number;

const add: CalculationOperation = (a, b) => a + b;
const subtract: CalculationOperation = (a, b) => a - b;
const multiply: CalculationOperation = (a, b) => a * b;
const divide: CalculationOperation = (a, b) => a / b;
```

通过正确使用函数类型，您可以利用 TypeScript 的类型系统来强制一致性并提高代码的可维护性。

### 9.10.3。混淆函数类型和函数签名

一个不太常见但仍然令人困惑的错误是将函数类型与函数签名混淆。简而言之，函数类型描述了函数的形状，而函数签名是函数的实际实现。考虑以下示例，其中我们错误地将函数类型定义与函数定义混淆：

```typescript
type MyFunction = (x: number, y: number) => number {        // #A
  return x + y;
};
```

`#A TypeScript error: ';' expected because we try to implement the function in a type alias.`

为了解决这个问题，我们必须将类型与函数定义本身分开（作为分配给 MyFunction 类型的变量的函数表达式）。这是正确的代码：

```typescript
type MyFunction = (x: number, y: number) => number;         // #A
const myFunction: MyFunction = (x, y) => x + y;             // #B
```

`#A Function type`
`#B Function implementation`

鉴于此，TypeScript 给了我们一个错误，但该错误是关于分号的一些内容，而且并不是立即显而易见的。阅读这个错误可以让你节省几分钟盯着代码的困惑。

### 4.10.4. 使用过于通用的函数类型

过于通用的函数类型可能会导致类型安全性的丧失，从而难以在编译时捕获错误。例如，以下函数类型过于通用：

```typescript
type GenericFunction = (...args: any[]) => any;
```

该函数类型接受任意数量任意类型的参数并返回任意类型的值。当然，正如前面所讨论的，它削弱了 TypeScript 的优势。最好使用更具体的函数类型来准确描述预期的输入和输出：

```typescript
type SpecificFunction = (a: number, b: number) => number;
```

我们已经介绍了应用函数类型及其最佳实践方面的大量内容。总结一下：类型是好的（而不是通用的任何类型或无类型），简单是好的（而不是过于复杂）。

## 4.11. 忽略函数的实用程序类型

TypeScript 提供了一组内置实用程序类型，可以使使用函数及其类型更轻松、更高效。忽略这些实用程序类型可能会导致不必要的代码重复，并错过利用 TypeScript 类型系统提高代码质量的机会。本节将讨论一些常见的函数实用程序类型，并提供如何有效使用它们的示例。

### 4.11.1. 忘记 t​​ypeof

在 TypeScript 中，typeof 运算符可用于提取变量（包括函数）的类型。当您将 typeof 与函数一起使用时，它会返回该函数的类型签名，包括其参数类型和返回类型。当您想要将现有函数的类型签名重用于另一个变量或在其他函数中定义参数或返回类型时，这尤其有用。

下面的示例说明了如何使用 typeof 来提取函数的类型：

```typescript
function exampleFunction(                               // #A
  a: number,
  b: string
): boolean {
  // ... some operations
  return true;
}

type ExampleFunctionType = typeof exampleFunction;      // #B
let myFunction: ExampleFunctionType;                    // #C

myFunction = (num: number, str: string): boolean => {   // #D
  // ... some operations
  return false;
};
```

`#A Defining a function (imagine it's outside your code and we can't change it)`
`#B Using 'typeof' to extract the function type`
`#C Now you can use the extracted type for other variables or parameters`
`#D Assigning a function to 'myFunction' that matches the 'exampleFunction' signature`

在此示例中： exampleFunction 是一个简单函数，它接受数字和字符串作为参数并返回布尔值。考虑它在您的代码之外，因此不可能更改其代码以使用与 myFunction 相同的类型。接下来，ExampleFunctionType 使用 typeof 来提取 exampleFunction 的类型签名。该类型包括exampleFunction的参数类型和返回类型。

然后，myFunction 被声明为ExampleFunctionType 类型，这意味着它应该是一个与exampleFunction 具有相同签名的函数。通过使用 typeof 提取和重用函数类型，您可以保持一致性并减少冗余，特别是在处理复杂函数或需要确保多个函数在代码库中共享相同签名时。

### 4.11.2. 未充分利用 ReturnType 来实现更好的类型推断

ReturnType 实用程序类型提取函数的返回类型，当您想要确保函数的返回类型与另一个函数的返回类型相同或定义派生类型时，这会很有用。

这是一个定义函数和函数类型的不太理想的示例。名为 sum 的函数采用两个参数 a 和 b，均为数字类型。当使用两个数字调用此函数时，会将这些数字相加并返回结果，该结果也是数字类型。

然后，名为 Calculation 的类型别名表示一个接受两个数字参数并返回一个数字的函数。此类型可用于对乘法等其他函数进行类型检查，以确保它们与获取两个数字并返回一个数字的模式相匹配。

```typescript
function sum(a: number, b: number): number {              // #A
  return a + b;
}

type Calculation = (a: number, b: number) => number;      // #B

let multiply: Calculation = (a: number, b: number) => {   // #C
  return a * b;
};
```

`#A Implementing the addition function sum`
`#B Defining type alias`
`#C Implementing a multiplication function with the defined type alias`

在上面的示例中，sum 的返回类型被手动内联定义为数字，并且在类型别名 Calculation 中再次指定相同的返回类型。另外，我们可以让 TypeScript 通过这个来推断乘法的类型（之前我们介绍了推断的工作原理以及它的优点和缺点）：

```typescript
let multiply: Calculation = (a, b) => {
  return a * b;
};
```

有趣的是，我们将重用函数 sum 的返回类型。通过使用ReturnType，自动推断sum的返回类型并用于计算，减少代码重复，提高可维护性。

```typescript
function sum(a: number, b: number) {
  return a + b;
}

type Calculation = (a: number, b: number) => ReturnType<typeof sum>;

let multiply: Calculation = (a: number, b: number) => {
  return a * b;
};
```

您可能认为这个示例很愚蠢，因为为什么不像我们对乘法那样直接使用计算求和，而是使用 ReturnType？这是因为像 sum 这样的函数可以在不同的模块或库（由其他开发人员编写）中定义，因此我们无权增强 sum 的代码。同时，我们希望返回类型匹配。在这种情况下 ReturnType 会派上用场。

或者，在_这个特定_示例中，您可以像这样替换整个类型：

```typescript
type Calculation = typeof sum;
```

然而，这与仅仅从 sum 中提取返回类型是一种非常不同的方法，因为它分配整个类型而不仅仅是返回类型。灵活性较差。这样我们就无法根据需要修改参数，但使用 ReturnType 方法时，计算的函数参数可以与求和的函数参数不同。

```typescript
type Calculation = (nums: number[]) => ReturnType<typeof sum>;

let multiply: Calculation = (nums) => {
  return nums.reduce((p, c) => p * c, 1);
};
```

下面是 ReturnType 的另一个更复杂的示例，它展示了函数 fetchData 和类型 FetchDataResult 的声明，后面是另一个函数 processData 的定义。

函数 fetchData 从给定的 URL 获取一些数据，类型 FetchDataResult 表示获取的数据的结果，函数 processData 使用提供的获取函数回调处理获取的数据。

fetchData 函数的返回类型与 processData 回调函数的返回类型完全相同：

```typescript
function fetchData(url: string): Promise<{ data: any }> {
  // Fetch data from the URL and return a Promise
}

type FetchDataResult = Promise<{ data: any }>;

function processData(fetchFn: (url: string) => FetchDataResult) {
  // Process the fetched data
}
```

fetchData 函数接受 string 类型的 url 参数，并返回一个 Promise，该 Promise 解析为具有 any 类型的 data 属性的对象。该函数负责从指定的 URL 获取数据。 FetchDataResult 类型被定义为一个 Promise，它解析为具有任何类型的数据属性的对象。该类型用于描述processData函数中fetchFn函数参数的预期返回类型。 processData 函数采用函数参数 fetchFn，该函数被定义为接受字符串类型的 url 参数并返回 FetchDataResult 的函数。该函数负责处理获取的数据。

因此，在上面的示例中，fetchData 的返回类型重复两次，这可能容易出错且难以维护。假设我们可以出于某种原因更新 fetchData 的代码。考虑到这一点，一个更好的例子是利用 ReturnType 来避免代码重复，当仅在一个地方而不是所有地方修改时，代码重复可能会导致错误：

```typescript
function fetchData(url: string): Promise<{ data: any }> {   // #A
  // Fetch data from the URL and return a Promise
}

type FetchDataResult = ReturnType<typeof fetchData>;       // #B

function processData(fetchFn: (url: string) => FetchDataResult) {
  // Process the fetched data
}
```

`#A Imagine this is outside of our code in some library over which we don't have control`
`#B Used the ReturnType utility type`

事实上，通过使用 ReturnType 实用程序类型，我们简化了代码并使其更易于维护。

### 4.11.3. 放弃参数以获得更清晰的参数类型

参数实用程序类型将函数参数的类型提取为元组，从而更容易创建与现有函数具有相同参数的类型。它有点类似于 ReturnType，仅适用于函数参数（也称为函数参数）。

假设您有一些默认的通用函数来向人们打招呼 standardGreet。然后，如果您想创建新的自定义函数，您可以定义一个类型别名 MyGreeting ，用于大声或友好地打招呼：

```typescript
function standardGreet(name: string, age: number) {
  console.log(`Hello, ${name}. You are ${age} years old.`);
}

type MyGreeting = (name: string, age: number) => void;

const greetPersonLoudly: MyGreeting = (name, age) => {
  standardGreet(name.toUpperCase(), age);
};

const greetPersonNicely: MyGreeting = (name, age) => {
  standardGreet(name, age - 10);
};

greetPersonLoudly("Deepak", 54); // Hello, DEEPAK. You are 54 years old.
greetPersonNicely("Deepak", 54); // Hello, Deepak. You are 44 years old.
```

上例中，在MyGreeting中再次手动指定standardGreet的参数类型。我们可以做得更好，对吗？当然！让我们利用参数从 standardGreet 中“提取”函数参数，而其余代码可以保持不变：

```typescript
type MyGreeting = (...args: Parameters<typeof standardGreet>) => void;
```

通过使用Parameters，可以自动推断standardGreet的参数类型并在MyGreeting中使用，使代码更简洁，更易于维护。接下来，我想演示一些参数的更多示例和用例。我们从这段代码开始：

```typescript
function standardGreet(name: string, age: number) {
  console.log(`Hello, ${name}. You are ${age} years old.`);
}

type MyGreeting = (...args: Parameters<typeof standardGreet>) => void;
```

下一个示例演示使用Parameters<typeof standardGreet>将特定参数分配给params1，然后使用扩展运算符调用standardGreet：

```typescript
const params1: Parameters<typeof standardGreet> = ['Pooja', 25];
greet(...params1);      // #A
```

`#A Output: Hello, Pooja. You are 25 years old.`

之后，下一个示例通过使用 as const 断言声明 params2 来展示元组类型的使用，以确保参数的文字类型：

```typescript
const params2: Parameters<typeof standardGreet> = ['Arjun', 30] as const;
greet(...params2);      // #A
```

`#A Output: Hello, Arjun. You are 30 years old.`

随后，下一个示例声明一个 MyReturnedGreeting 类型的变量greetPerson，它表示一个与standardGreet 具有相同参数的函数。参数或返回类型（前面介绍过）的强大之处在于我们可以混合和匹配它们（即，将它们与“父”类型不同的值组合起来）。然后使用特定参数和新的返回类型（字符串）调用greetPerson，从而产生预期的输出。

```typescript
type MyReturnedGreeting = (...args: Parameters<typeof standardGreet>) => string;

const greetPerson: MyReturnedGreeting = (name, age) =>
  console.log(`¡Saludos, ${name}! Tienes ${age} años.`);

greetPerson("Vikram", 35);   // #A
console.log("Jose", 42);     // #B
```

`#A Output: NO`
`#B Output: Saludos, Jose! Tienes 42 años.`

总之，TypeScript 的函数实用类型可以帮助您创建更高效​​、可维护且更具表现力的代码。通过利用 ReturnType 和Parameters 等实用程序类型，您可以减少代码重复并使代码库更能适应更改。在使用 TypeScript 中的函数时，请始终考虑使用实用程序类型，以充分利用该语言的类型系统。

## 4.12. 概括

- 始终指定函数的返回类型，以确保正确的类型检查并防止意外行为。

- 明智地使用可选参数和剩余参数，考虑它们对函数行为和可读性的影响。在函数签名调用中，始终将可选参数放在必需参数之后。并将其余参数放在最后。

- 始终指定函数的返回类型，以确保类型安全并为调用者提供明确的期望。

- 利用参数、ReturnType 和 ThisParameterType 等实用程序类型来增强类型安全性并提高函数中的代码质量。

- 使用箭头函数或显式绑定来维护所需的 this 上下文。始终设置其形状/类型。了解用于操作 this 上下文的 bind、call、apply 和 strictBindCallApply 之间的差异。

- 使用globalThis代替特定于环境的全局对象（窗口、全局等）以获得更好的可移植性。

- 利用参数、ReturnType 和 ThisParameterType 等实用程序类型来提高代码质量和正确性。

