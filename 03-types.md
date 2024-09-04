

# 3。类型、别名和接口

本章涵盖

- 了解类型别名和接口之间的区别

- 实践类型拓宽

- 适当地排序类型属性和扩展接口

- 适当应用类型保护

- 理解只读属性修饰符

- 有效利用 keyof 和 Extract 实用程序类型

掌握 TypeScript 感觉有点像被邀请参加一个专属派对，每个人都在说一种你认为自己很熟悉的语言的方言，但略有不同。在本例中，语言是 JavaScript，方言是 TypeScript。现在，想象一下走进这个聚会，听到诸如“类型”、“类型别名”和“接口”之类的词被到处乱扔。乍一听起来好像每个人都在讨论一场不寻常的艺术展览！但是，一旦你掌握了它的窍门，这些术语就会变得像你最喜欢的妙语一样熟悉。

在本次 TypeScript 晚会上的一系列独特对话中，您会发现人们热情地讨论类型扩展、类型防护、类型别名和接口的优点和缺点。这些 TypeScript 爱好者对这些结构的热情可能会让政治专家感到羞愧。对他们来说，TypeScript 功能之间错综复杂的差异不仅仅是编程问题，更是一种生活方式。如果您曾经觉得没有正确定义类型的代码库就像一个没有笑点的笑话，那么，您就在一个很好的公司。说个笑话：为什么 TypeScript 界面要去治疗？ ---因为它有太多未解决的属性！

但别担心。本章将引导您穿过 TypeScript 派对上熙熙攘攘的人群，确保您知道何时成为派对的焦点，何时负责任地推动您的代码库回家。毕竟，在 TypeScript 中就像在喜剧中一样，时机就是一切。我们将深入研究这些美味的 TypeScript 美食，了解每一种美食何时发挥作用，以及如何使用它们而不引起胃病。在此过程中，我们将学习避免一些最常见的陷阱，例如类型加宽、只读、keyof、类型保护、类型映射、类型别名以及其他可能使您的代码库看起来像幼儿园孩子吃过的糕点的陷阱。当我们讨论甜点主题时，我忍不住又开了一个玩笑：一个 TypeScript 变量担心体重增加，因为在吃完这些甜点之后，它不想成为一个_Fat Arrow Function_！

因此，准备好开始对类型和接口的探索吧。到本章结束时，你应该能够区分这两者，就像在家庭聚会上区分你的伯莎阿姨和格特鲁德阿姨一样——一切都在细节中。请记住，如果编码很容易，那么每个人都会这样做。但如果每个人都这样做，我们会嘲笑谁不懂递归呢？让我们深入了解吧！

## 3.1。令人困惑的类型别名和接口

在 TypeScript 世界中，类型别名和接口可以被视为同一枚硬币的两面——或者更恰当地说，是喜剧二人组中的两个角色。一个可能更灵活，可以做各种疯狂和不可预测的事情（你好，类型），而另一种则更可靠和一致，提供可预测的结构并确保一切按计划进行（那就是你，接口）。但就像任何优秀的喜剧二人组一样，他们都有自己的优点和缺点，知道何时利用每一个是编写剧本（或者在本例中为代码）的关键，这样才能获得最大的笑声（或者至少是最少的 bug 数量）。

TypeScript 中的类型就像编码世界中的变色龙。他们可以适应和改变以适应各种情况。它们用途广泛，可以随时转变为数据所需的任何形式。然而，它们也有其局限性。想象一下，一只变色龙试图融入杰克逊·波洛克的画作中——它将会经历一段艰难的时期！因此，虽然类型很方便，但尝试将它们用于复杂或变化的结构可能会导致代码混乱，速度比您所说的“类型混乱”更快。

另一方面，我们有接口。如果类型别名是变色龙，那么界面更像是房屋或家具的蓝图。它们为您提供了一个具体的结构，一个需要遵循的详细计划，确保您的对象按照规格构建。然而，就像蓝图一样，如果您的施工偏离计划，您最终会遇到编译器错误，这些错误看起来比未完成的宜家家具组装更可怕。让我们面对现实吧，没有人喜欢在项目进行到一半时才发现他们缺少一两个“分号”！

请记住，TypeScript 会编译为 JavaScript，因此最终这两种构造都只是在编译器和编辑器中提供更强的类型安全性和自动完成功能的工具。

类型别名：TypeScript 中的 type 关键字用于定义自定义类型，其中可以包括现有类型、联合类型、交集类型等的_别名_。类型可以表示任何类型的值，包括基元、对象和函数。 type 创建别名来引用类型。以下示例定义了两种类型、对象变量和使用这些类型的类：

```typescript
type Coordinate = number | string;      //  #A
const latitude: Coordinate = "30°47′41.841″ N";
const longitude: Coordinate = -122.276582;

type Point = {                          //  #B
  x: number;
  y: number;
};

let point: Point = {
  x: 10,
  y: 20,
};

class Circle implements Point {         //  #C

  x: number = 0;
  y: number = 0;
  radius: number = 10;
}
```

`#A Type alias for the union type.`
`#B Type alias for the object type with x, y coordinates.`
`#C Type aliased is used to define the required properties of the class`

请注意，当我们实现一个类型时，我们可以添加更多属性，例如前面示例中的半径，同时我们必须提供我们实现的类型（Point）的所有属性。

接口：interface 关键字用于定义对象的契约，描述它们的形状和行为。接口可以由类实现，由其他接口扩展，并用于对对象进行类型检查。它们不能表示原始值或联合类型。

以下示例定义了一个 Shape 接口、一个 Shape 类型的对象，以及一个实现该接口的类：

```typescript
interface Shape {
  area(): number;
}

let shape100: Shape = {               //  #A
  area: () => {
    return 100;
  },
};

class Circle implements Shape {       //  #B
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  area(): number {
    return Math.PI * this.radius ** 2;
  }
}
```

`#A Object shape must be the same shape as the interface Shape.`
`#B Class Circle must have properties overlap with the Shape interface, i.e., method area().`

顺便说一句，在本书之外的一些 TypeScript 代码中，您可能会看到以 I 字母为后缀（结尾）的接口，如 ShapeI 中那样。这里的动机很明确——区分类或类型别名。然而，TS 专业人士不鼓励使用这种表示法，如以下 GitHub 讨论所示：<https://github.co/microsoft/TypeScript-Handbook/issues/121>。在我看来，这个符号是不必要的。

为了演示类型别名和接口之间的相似性，让我们看看如何重写使用类型别名和接口的示例。我们需要用大括号替换等号，用接口替换关键字类型，并且因为我们无法用接口定义联合类型，所以我们必须创建一个解决方法值属性，如下所示：

```typescript
interface Coordinate {       //  #A
  value: number | string;
}

const latitude: Coordinate = {
  value: "30°47′41.841″ N",
};

const longitude: Coordinate = {
  value: -122.276582,
};

interface Point {           //  #B
  x: number;
  y: number;
}

let point: Point = {
  x: 10,
  y: 20,
};

class Circle implements Point {
  x: number = 0;
  y: number = 0;
  radius: number = 10;
}
```

`#A Union type needs to become an object type with the value property.`
`#B We don't use an equal sign = when defining interfaces unlike with type aliases.`

虽然在前面的示例中类型别名和接口是可以互换的，但这里有一个很酷的技巧，接口可以做到（而类型别名不能）。接口可以“重新打开”。技术术语是声明合并。这是 TypeScript 表示不同 ECMAScript 版本中为内置类型（例如数组）引入的方法的方法。以下示例说明了如何“重新打开”接口：

```typescript
interface User {
  name: string;
}

interface User {
  age: number;          //  #A
}

let user: User = {
  name: "Petya",
  age: 18,              //  #B
};

type Point = {
  x: number;
};

type Point = {          //  #C
  y: number;
};
```

`#A This is perfectly fine; the User interface now has a name and an age.`
`#B Our object literal has the "added" property age.`
`#C This will raise a Duplicate identifier error.`
类型别名和接口之间的主要区别在于类型别名更灵活，因为它们可以表示原始类型、联合类型、交集类型等，而接口更适合对象类型检查以及类和对象字面量表达能力（实际上所有他们可以做到！）。正如我们所看到的，类型别名不能“重新打开”来添加新属性，而接口始终是可扩展的。

以下是何时使用类型别名与接口的简短列表：

- 使用对象形状和类契约的接口：接口是定义对象形状或类必须实现的契约的理想选择。它们提供了一种清晰简洁的方式来表达类和对象之间的关系。

- 使用类型来实现更复杂和灵活的结构：类型别名更加通用，可以表示复杂的结构，例如联合类型、交集类型和映射类型（对生成对象类型的类型进行操作，而不是对每个类型本身进行操作） se）、元组类型或文字类型以及函数类型（尽管它们也可以使用接口定义）。因此，当您的类型定义需要更大的灵活性和复杂性时，请使用类型。

- 接口可以从其他类型（接口和类型别名）“扩展”/“继承”，但类型别名不能（但它们可以使用交集 &）

- 接口支持声明合并，而类型别名则不支持。

- 类型别名可以定义不能表示为接口的类型，例如联合类型、元组类型和其他复杂或计算类型。

I> 旁注和强大提示：必要时组合类型和接口。在某些情况下，组合类型和接口来创建更强大和更具表现力的类型定义可能会很有好处。例如，您可以使用类型来表示多个接口的联合或使用类型扩展接口。这是一个示例，我们将定义一些接口，然后将它们与类型组合以创建可以表示多种不同形状的数据的联合类型。

让我们为不同种类的宠物定义一些接口：

```typescript
interface Dog {
  species: "dog";
  bark: () => void;
}

interface Cat {
  species: "cat";
  purr: () => void;
}

interface Bird {
  species: "bird";
  sing: () => void;
}
```

接下来，让我们使用类型来表示接口的联合：

```typescript
type Pet = Dog | Cat | Bird;
```

采用 Pet 类型的示例函数。在内部，我们可以根据宠物的种类使用类型缩小来与宠物互动

```typescript
function interactWithPet(pet: Pet) {
  console.log(`Interacting with a ${pet.species}.`);
  if (pet.species === "dog") {
    pet.bark();
  } else if (pet.species === "cat") {
    pet.purr();
  } else if (pet.species === "bird") {
    pet.sing();
  }
}
```

使用带有附加属性的交集类型的另一个示例：

```typescript
type PetWithID = Pet & { id: string };
```

创建满足 PetWithID 的对象：

```typescript
const myPet: PetWithID = {
  species: "dog",
  bark: () => console.log("Woof!"),
  id: "D123",
};
```

使用该函数很简单：

```typescript
interactWithPet(myPet);
console.log(`Pet ID is ${myPet.id}.`);
```

在这个例子中：

- 我们定义了三个接口：狗、猫和鸟，每个接口代表具有独特行为的不同种类的宠物。

- 然后我们创建一个宠物类型，可以是狗、猫或鸟。这是联合类型，允许我们定义一个可以保存多种数据形状的变量。

- 我们定义一个函数interactWithPet，它接受Pet类型并使用类型缩小来根据宠物的种类调用适当的方法。

- 我们使用附加属性 (ownerName) 扩展接口 (Pet) 以创建新接口 (PetWithOwner)。

- 我们还创建了一个交集类型 (PetWithID)，它将 Pet 类型与附加 id 属性相结合。这对于宠物需要具有唯一标识符的情况非常有用。

通过组合接口和类型，您可以创建复杂且灵活的类型定义，以适应 TypeScript 应用程序中的各种场景。

现在，您可能仍在问自己，“我应该使用哪个？键入别名或接口？”。如果您询问我的意见，并且作为向您证明心理捷径的意义，我建议从使用接口开始，直到或除非您需要类型可以提供的更大灵活性。通过这种方式，通过默认接口，您将获得类型安全性，并消除不断思考此处应该使用什么：类型或接口的额外认知负担。

接下来，让我们看看在 TypeScript 中使用类型和接口时要避免的最常见陷阱：

- 混合类型和接口方法：了解类型和接口之间的差异，并为您的用例选择合适的类型和接口。一旦选择了方法（例如，使用接口来声明对象的形状），请遵循它以确保给定项目中的一致性。

- 在接口中过度使用联合类型：虽然可以在接口中使用联合类型，但过度使用它们会使接口更难以理解和维护。考虑将复杂的联合类型重构为单独的接口或使用更复杂结构的类型。我们将在 _3.7 中介绍更多相关内容。类型过于复杂._

让我们抛开类型别名和接口，来讨论一个不同但重要的主题：类型与值。开发人员在使用 TypeScript 时可能会遇到的一个常见错误是“_仅指类型，但在此处用作值。_”当在需要值的上下文中使用类型或接口时，就会发生此错误。由于类型和接口仅用于编译时类型检查并且没有运行时表示，因此不能将它们视为值。要解决此错误，请确保您使用的上下文结构正确。如果需要运行时值，请考虑使用类、枚举或常量而不是类型或接口。理解 TypeScript 中类型和值之间的区别对于避免此错误并编写正确的、可维护的代码至关重要。

下面是一个代码示例，说明了上述错误：“仅指类型，但此处被用作值。”这将导致错误：“MyType 仅引用类型，但在这里被用作值。”：

```typescript
type MyType = {
  property: string;
};

const instance = new MyType(); //  #A
interface MyTypeI {            //  #B
  property: string;
}

const instance = new MyType(); // #C
```

`#A Incorrect usage: trying to use a type as a value`
`#B Same error with interfaces`
`#C Erro`


解决方案：使用类、枚举或常量而不是类型。

```typescript
class MyClass implements MyType {
  property: string;
  constructor(property: string) {
    this.property = property;
  }
}

const instance = new MyClass("Hello, TypeScript!"); //  #A
console.log(instance.property);                     //  #B

class MyClass implements MyTypeI {
  property: string;
  constructor(property: string) {
    this.property = property;
  }
}

const instance = new MyClass("Hello, TypeScript!"); //  #C
console.log(instance2.property);
```

`#A Correct usage: instantiating a class`
`#B Hello, TypeScript!`
`#C Correct usage: instantiating a class`

在前面的示例中，尝试将 MyType 实例化为类会导致错误。为了解决这个问题，我们定义一个与 MyType 结构相同的类 MyClass 并实例化 MyClass。这证明了理解 TypeScript 中类型和值之间的区别以及针对不同上下文使用正确结构的重要性。

总而言之，通过了解类型别名和接口之间的差异，开发人员可以为其用例选择正确的构造，并编写更清晰、更易于维护的 TypeScript 代码。类型别名：最适合定义并集、交集、元组或需要应用复杂类型转换时。接口：非常适合声明对象和类的形状，特别是当您预计通过声明合并扩展这些类型时。考虑每个构造的优点和缺点，并在必要时组合使用它们以创建强大且富有表现力的类型定义。

## 3.2。误解类型加宽

类型加宽是一个 TypeScript 概念，指的是根据使用类型的上下文自动扩展类型。这个过程（又名推理）在某些情况下可能会有所帮助，但如果不正确理解和管理，它也可能导致意外的类型问题。本节将讨论类型加宽的概念、其含义以及在 TypeScript 代码中有效使用它的最佳实践。

让我们首先了解类型扩展。当 TypeScript 根据值的用法或上下文为该值分配更广泛的类型时，就会发生类型加宽。当使用特定值初始化变量并且 TypeScript 扩展类型以包含其他潜在值时，通常会发生这种情况。

具有特定值但扩展类型的字符串变量示例：

```typescript
let message = "Hello, TypeScript!"; //  #A
```

`#A Widened type: string`

在此示例中，消息变量使用字符串值进行初始化。即使初始值是特定的字符串文字，TypeScript 也会自动将消息类型扩展为字符串。

类型加宽还可能导致意外的类型分配，因为 TypeScript 可能会过度加宽类型，从而导致潜在的类型不匹配。因此，使用类型扩展的最佳实践是：显式类型注释和 const。

- 使用显式类型注释：为了防止意外的类型加宽，您可以使用显式类型注释来指定变量或函数参数所需的确切类型。

```typescript
let message: "Hello, TypeScript!" = "Hello, TypeScript!"; //  #A
```

`#A Type: "Hello, TypeScript!"`

在此示例中，通过提供显式类型注释，我们阻止 TypeScript 将消息类型扩展为字符串，确保它保持特定的字符串文字类型。

- 对不可变值使用 const：当声明具有不可变值的变量时，使用 const 关键字而不是 let。这将防止基元（而不是对象）的类型扩展，因为 const 变量无法重新分配。

```typescript
const message = "Hello, TypeScript!"; //  #A
```

`#A Type: "Hello, TypeScript!"`

我们还可以在非常量变量上使用 as const。在以下示例中，重新分配 apiUrl 变量的值将显示错误：类型 '"https://malicious-website.com/api"' 无法分配给类型 '"https://azat.co/ API/v1"'。 apiUrl 被视为具有值 https://azat.co/api/v1 的文字类型，而不仅仅是类型字符串。

```typescript
let apiUrl = "https://azat.co/api/v1" as const;
apiUrl = "https://malicious-website.com/api";
```

这对于配置等很有用。我们还可以将 as const 与 const 一起使用，但 const 本身已经阻止了更改。

下面是另一个示例，说明了 TypeScript 类型扩展的实际效果：

```typescript
function displayText(text: string) {
  console.log(text);
}

const greeting = "Hello, TypeScript!";      //  #A
displayText(greeting);                      //  #B
const specificGreeting: "Hello, TypeScript!" = "Hello, TypeScript!"; //  #C
displayText(specificGreeting);              //  #D
```

`#A Widened type: string`
`#B No error, as the type of greetingis widened to string`
`#C Preventing type widening using explicit type annotation`
`#D Error: Argument of type '"Hello, TypeScript!"' is not assignable to parameter of type 'string'.`

在此示例中，我们定义了一个 displayText 函数，该函数采用字符串类型的文本参数。当我们在没有显式类型注释的情况下声明问候语变量时，TypeScript 会自动将其类型扩展为字符串，从而允许将其作为参数传递给 displayText 函数，而不会出现任何问题。

然后，我们使用显式类型注释“Hello, TypeScript!”声明 SpecificGreeting 变量。字符串文字，而 TypeScript 不会扩展类型。因此，将 SpecificGreeting 传递给 displayText 函数不会引发类型错误，因为“Hello, TypeScript!”字符串文字可分配给函数所需的更通用的字符串类型（文本参数）。

下面的示例说明了对 TypeScript 类型扩展的无意依赖：

```typescript
function getPetInfo(pet: { 
    species: string; 
    age: number }) {
  return `My ${pet.species} is ${pet.age} years old.`;
}

const specificDog = { species: "dog", age: 3 };         //  #A
const dogInfo = getPetInfo(specificDog);                //  #B
specificDog.species = "cat";                            //  #C
const updatedDogInfo = getPetInfo(specificDog);         //  #D
```

`#A Widened type: { species: string; age: number; }`
`#B No error, as the type of specificDog is widened to { species: string; age: number; }`
`#C Later in the code, a developer mistakenly updates the object`
`#D The specificDog object is no longer accurate, but the type system does not catch this error due to type widening; Returns: "My cat is 3 years old."`

在此示例中，我们定义了一个 getPetInfo 函数，该函数接受具有特定形状的宠物参数。当我们在没有显式类型注释的情况下声明 SpecificDog 变量时，TypeScript 会自动将其类型扩展为 {species:string;年龄：数字； }，允许它作为参数传递给 getPetInfo 函数，没有任何问题。

然而，在代码的后面，开发人员错误地将 SpecificDog.species 属性更新为“cat”。由于类型加宽，TypeScript 无法捕获此错误，并且 getPetInfo 函数返回不准确的结果。这表明无意中依赖类型加宽会导致代码更难以维护并且更容易出错。

为了防止此类问题，请考虑使用显式类型注释或创建类型别名（或接口）来表示预期的对象形状：

```typescript
type Dog = {
  species: "dog";
  age: number;
};

const specificDog: Dog = { 
  species: "dog", 
  age: 3 
};
specificDog.species = "cat";        //  #A
specificDog satisfies Dog;          //  #B
```

`#A Error: Type '"cat"' is not assignable to type '"dog"'`
`#B Yes, it satisfies. No errors here.`

注意：我们可以通过使用接口 Dog 甚至内联定义来实现相同的效果，如下所示：
```typescript
const specificDog: { 
  species: 'dog', age: number 
} = { 
  species: 'dog', 
  age: 3 
};
```

satisfies 关键字是 TypeScript 中的一个有用工具，用于确保类型安全性和兼容性，同时保持类型的完整性和原始结构。它在复杂的代码库中特别有用，因为在不牺牲类型灵活性的情况下，严格的类型一致性至关重要。

现在，如果我们从变量 SpecificDog 中删除显式类型注释，但将其保留在函数参数 pet 中，您认为会发生什么？如果我们有这样的代码，那就是这样的：

```typescript
type Dog = {
  species: "dog";
  age: number;
};

function getPetInfo(pet: Dog) {
  return `My ${pet.species} is ${pet.age} years old.`;
}

const specificDog = { species: "dog", age: 3 };
const dogInfo = getPetInfo(specificDog);      //  #A
specificDog satisfies Dog;                    //  #B
specificDog.species = "cat";                  //  #C
```

`#A Argument of type '{ species: string; age: number; }' is not assignable to parameter of type 'Dog'.`
`#B Type '{ species: string; age: number; }' does not satisfy the expected type 'Dog'`
`#C Okay, no TS errors which can lead to bugs.`

惊喜！通过将 SpecificDog 属性设为字符串，拓宽太过分了，这反过来又使我们的对象 SpecificDog 与 Dog 函数参数类型的 pet 不兼容。

通过使用显式类型注释（使用接口或类型别名），您可以避免忽视类型扩展并确保您的代码保持准确和可维护。另一方面，如果加宽过大，则可能会导致类型错误。总而言之，在 TypeScript 中处理类型扩展时要避免的最常见陷阱是：

- 忽略类型加宽：请注意代码中可能发生类型加宽的时间和地点，因为忽略它可能会导致意外行为或与类型相关的错误。

- 无意中依赖类型加宽：虽然类型加宽在某些情况下可能很有帮助，但无意中依赖它可能会使代码难以维护并且更容易出错。有意使用类型扩展，并在必要时使用显式类型注释。

- 享受类型扩展按预期工作的同时。这是对语言的一个很好的补充（当开发人员知道它是如何工作时）。

通过理解类型扩展的概念及其含义，开发人员可以编写更健壮且可维护的 TypeScript 代码。请注意类型加宽可能发生的时间和地点，并使用显式类型注释和 const 关键字来防止意外的加宽。这将产生更精确和可靠的类型系统，有助于在编译时捕获潜在的错误。

## 3.3。类型属性的排序不一致

接口和类中不一致的属性顺序可能会导致代码难以阅读和维护。确保属性顺序一致使代码更可预测且更易于理解。让我们看一些示例来说明一致的属性排序的好处。

在以下示例中，属性的排序与接口​​和类声明不一致：姓名、年龄、地址、职位：

```typescript
interface InconsistentPerson {
  age: number;
  name: string;
  address: string;
  jobTitle: string;
}

class InconsistentEmployee implements InconsistentPerson {
  address: string;
  age: number;
  name: string;
  jobTitle: string;

  constructor(
    name: string,
    age: number,
    address: string,
    jobTitle: string
  ) {
    this.name = name;
    this.age = age;
    this.address = address;
    this.jobTitle = jobTitle;
  }
}

const employee = new InconsistentEmployee(
  "Anastasia",
  30,
  "123 Main St.",
  "Software Engineer"
);

console.log(employee);
```

在此示例中，InconcientPerson 接口和 InconcientEmployee 类 (employee) 的对象的属性顺序不一致。这使得代码更难阅读，因为开发人员必须花费更多时间搜索他们需要的属性。在 new InconcientEmployee() 构造函数调用中很容易出错。

现在，让我们看一个具有一致属性排序的示例：

```typescript
interface ConsistentPerson {
  name: string;
  age: number;
  address: string;
  jobTitle: string;
}

class ConsistentEmployee implements ConsistentPerson {
  name: string;
  age: number;
  address: string;
  jobTitle: string;

  constructor(
    name: string,
    age: number,
    address: string,
    jobTitle: string
  ) {
    this.name = name;
    this.age = age;
    this.address = address;
    this.jobTitle = jobTitle;
  }
}

const employee = new ConsistentEmployee(
  "Pavel",
  25,
  "456 Main Ave.",
  "Product Manager"
);

console.log(employee);
```

或者，我们可以用一个对象（具有定义的自定义类型）替换多个构造函数参数。这也有助于不弄乱参数的顺序。但是使用单个对象而不是多个参数是一种全新的模式，其优点和缺点我们将留给其他人讨论。

通过一致地对接口和类中的属性进行排序，我们使代码更可预测且更易于阅读。这可以提高生产力和可维护性，因为开发人员可以快速找到并了解他们需要使用的属性。

总之，在 TypeScript 代码中保持一致的属性顺序对于可读性和可维护性至关重要。通过遵循可预测的模式，开发人员可以更好地理解和导航代码，从而获得更高效、更愉快的开发体验。

## 3.4。不必要地扩展接口

在 TypeScript 中，扩展接口可以帮助您创建更复杂和可重用的类型。然而，不必要的接口扩展可能会导致代码过于复杂并妨碍可维护性。在本章中，我们将讨论不必要的接口扩展可能引起的问题，并探索简化代码的方法。

考虑这个哺乳动物、狗和动物接口的例子：

```typescript
interface Animal {                 //  #A
  name: string;
  age: number;
}

interface Mammal extends Animal {} //  #B
interface Dog extends Mammal {}    //  #C

const myDog: Dog = {               //  #D
  name: "Buddy",
  age: 3,
};

console.log(myDog);                //  #E
```

`#A Base interface`
`#B Extended interface with no additional properties`
`#C Another extended interface with no additional properties`
`#D Usage of the extended interface`
`#E The Dog and Mammal interfaces add no additional value.`

基本接口已扩展为包括 Dog 和 Mammal 接口，但没有其他属性。这意味着新接口不会带来额外的价值。他们只是给代码添加了不必要的膨胀。我们可以通过删除空接口 Dog 和 Mammal 来简化前面的版本：

```typescript
interface SimplifiedAnimal {
  name: string;
  age: number;
}

const mySimplifiedDog: SimplifiedAnimal = {
  name: "Buddy",
  age: 3,
};

console.log(mySimplifiedDog);
```

SimplifiedAnimal 界面更加简洁，更容易理解。

这是另一个带有空接口管理器的示例：

```typescript
interface Person {                    //  #A
  name: string;
  age: number;
}

interface Employee extends Person {   //  #B
  title: string;
  department: string;
}

interface Manager extends Employee {} //  #C

const myManager: Manager = {          //  #D
  name: "Anastasia",
  age: 35,
  title: "Project Manager",
  department: "IT",
};

console.log(myManager);
```

`#A Base interface`
`#B Extended interface with unrelated properties`
`#C Another extended interface with no additional properties`
`#D Usage of the extended interface`

Manager 接口没有添加任何附加值，因为 Manager 接口的主体是空的。没有属性因此，我们可以简化我们的代码库。值得一提的是，有一条 ESLint 规则禁止空接口：no-empty-interface。

我们可以保留 Person 和 Employee（具有特定于员工的属性的更具体的人员）或只是简化为单个接口（除非 Person 在代码中的其他地方使用）：

```typescript
interface SimplifiedEmployee {
  name: string;
  age: number;
  title: string;
  department: string;
}

const mySimplifiedManager: SimplifiedEmployee = {
  name: "Anastasia",
  age: 35,
  title: "Project Manager",
  department: "IT",
};

console.log(mySimplifiedManager);
```

最终的 SimplifiedEmployee 界面比最初的代码更简洁、更容易理解。它没有空接口。当然，您可能会想，“嘿，我将来需要那个空接口”，这可能是真的。然而，现在代码变得更加复杂。同样的简单性原则不仅适用于空接口，还适用于可以组合或合并到其他接口中的接口。

通过查看我们的示例，您可能会认为扩展接口始终是一个坏主意，但事实并非如此。在 TypeScript 中扩展接口本质上并不是坏事；这是一个强大的功能，可以实现更灵活和可重用的代码。然而，它是否可取取决于上下文以及它的使用方式。例如，当我们有一组共享公共属性但也有自己独特属性的对象时，扩展接口特别好且有用。

想象一下，我们正在创建一个用户管理系统，需要处理不同类型的用户：管理员、会员和访客。所有用户共享共同的属性 id、姓名和电子邮件，但他们也有独特的属性和方法。我们定义一个基本接口 User，然后使用每个子接口的唯一属性对其进行扩展：

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}
```

对于管理员用户：

```typescript
interface Admin extends User {
  adminLevel: string;
  createPost(content: string): void;
}
```

对于会员用户：

```typescript
interface Member extends User {
  membershipType: string;
  renewMembership(): void;
}
```

对于访客用户：

```typescript
interface Guest extends User {
  expirationDate: Date;
}
```

现在，我们可以使用这些接口来定义适用于特定用户类型的函数或类。例如，我们可以创建一个以 Admin 作为参数的函数并调用它：

```typescript
function createAdmin(user: Admin) {      // #A  
  console.log(`Creating admin user: ${user.name}`);
}

const adminUser: Admin = {              // #B
  id: 1,
  name: "Alisa",
  email: "alisa@qq.com",
  adminLevel: "super",
  createPost: (content: string) => console.log(`Posting: ${content}`),
};

createAdmin(adminUser);                 // #C

adminUser.createPost(                   // #D
  "10 Reasons TypeScript is the 'Type' of Friend JavaScript Didn't Know It Needed!"
);          
```

`#A Logic specific to creating an admin user`
`#B Sample admin user`
`#C Creating admin user: Alisa`
`#D Works perfectly!`

在此用户类型示例中，扩展接口组织代码，使其可扩展且可维护，这在更大或更复杂的 TypeScript 应用程序中特别有益。

总之，避免在 TypeScript 代码中使用_不必要的_接口扩展非常重要。通过保持界面简洁和集中，您可以提高代码的可读性和可维护性。始终考虑扩展接口是否会增加代码的价值或复杂性，并尽可能选择简单性。

## 3.5。错过使用类型别名的机会

类型别名是 TypeScript 中的一项有用功能，允许您为类型创建新名称，使代码更具可读性和可维护性。本质上，类型别名是为任何特定类型指定的名称。忽略类型别名可能会导致代码重复、可读性降低并增加维护工作量。在本节中，我们将讨论类型别名的重要性并提供有关如何有效使用它们的指导。请注意，在本节中，当我谈论类型别名时，在许多情况下，接口可以替代类型别名，因为接口定义了类型。

在整个代码库中重复复杂的类型可能会导致重复并使代码更难以维护。类型别名通过为类型提供单一引用点来帮助您避免此问题。在下面的示例中，我们不使用类型别名并最终导致代码重复：

```typescript
function processText(text: string | null | undefined): string {
  // ...do something
  return text ?? "";
}

function displayText(text: string | null | undefined): void {
  console.log(text ?? "");
}

function customTrim(text: string | null | undefined): string {
  if (text === null || text === undefined) {
    return "";
  }

  let startIndex = 0;
  let endIndex = text.length - 1;

  while (startIndex < endIndex && text[startIndex] === " ") {
    startIndex++;
  }

  while (endIndex >= startIndex && text[endIndex] === " ") {
    endIndex--;
  }

  return text.substring(startIndex, endIndex + 1);
}
```

在上面的例子中，复杂类型 string |空 | undefined 在两个函数签名中重复出现。使用类型别名（NullableString）可以简化代码：

```typescript
type NullableString = string | null | undefined;

function processText(text: NullableString): string {
  return text ?? "";
}

function displayText(text: NullableString): void {
  console.log(text ?? "");
}

function customTrim(text: NullableString): void {
  // ...
}
```

使用类型别名可以通过为复杂类型或常用类型组合提供描述性名称来提高代码的可读性。例如，考虑以下没有类型别名的代码，该代码具有两个采用完全相同的参数维度的函数：

```typescript
function rectangleArea(dimensions: { width: number; height: number }): number {
  return dimensions.width * dimensions.height;
}

function rectanglePerimiter(dimensions: {
  width: number;
  height: number;
}): number {
  return (dimensions.width + dimensions.height) * 2;
}

console.log(rectangleArea({ width: 10, height: 4 }));       // 40
console.log(rectanglePerimiter({ width: 10, height: 4 }));  // 28
```

下一步，让我们添加类型别名 RectangleDimensions 以提高可读性（并避免代码重复），特别是当我们必须在许多地方一遍又一遍地使用 RectangleDimensions 而不仅仅是这两个函数时。下面概述了带有类型别名的情况：

```typescript
type RectangleDimensions = { width: number; height: number };

function rectangleArea(dimensions: RectangleDimensions): number {
  // ...
}

function rectanglePerimiter(dimensions: RectangleDimensions): number {
  // ...
}
```

在上面的示例中，使用 RectangleDimensions 的类型别名可以提高矩形区域和矩形Perimiter 函数签名的可读性。

类型别名还可以帮助封装与类型相关的逻辑，从而更轻松地更新和维护代码（以及提高可读性并避免代码重复）。

考虑带有联合类型别名 ApiResponse 的代码，该代码具有用于成功响应和失败（错误）响应的 API 响应结构：

```typescript
type ApiResponse<T> =
  | { data: T; status: number }
  | { status: number; error: string };
```

成功的响应将有 T 类型和状态的数据，但没有错误，而失败的响应将有错误和状态字段，但没有数据。

接下来，我们可以为成功和错误创建单独的类型别名。这将使我们能够在其他地方使用响应类型并使代码更具可读性。之后，我们仍然可以为 ApiResponse 类型创建联合类型：

```typescript
type SuccessResponse<T> = { data: T; status: number };
type ErrorResponse = { status: number; error: string };
type ApiResponse<T> = SuccessResponse<T> | ErrorResponse;
```

在上面的示例中，为 SuccessResponse 和 ErrorResponse 使用类型别名使得联合类型 ApiResponse 更易于理解和维护。 ApiResponse<T> 类型表示任何 API 响应。它是联合类型，因此 ApiResponse 可以是 SuccessResponse 或 ErrorResponse。 T 又是 SuccessResponse 中数据类型的占位符。如果您有一个返回用户的 API 端点，您可以使用这些类型，如下所示：

```typescript
type SuccessResponse<T> = { data: T; status: number};
type ErrorResponse = { status: number, error: string };
type ApiResponse<T> = SuccessResponse<T> | ErrorResponse;

type User = {
  id: string;
  name: string;
}

function getUser(id: User['id']): ApiResponse<User> {
  if (Math.random()<0.5) {         //  #A
    return {
      data: {
        id: '123',
        name: 'Petya'
      },
      status: 200
    }
  } else {
    return {
      status: 500,
      error: "500 server error"
    }
  }
}

console.log(getUser('123'))        //  #B
```

`#A Random function that either returns success or error`
`#B We invoke the getUser with user ID`

在本例中，getUser 是一个返回 ApiResponse<User> 的函数。这意味着返回的对象可以是 SuccessResponse<User> （数据是 User 对象，错误不存在），也可以是 ErrorResponse （数据不存在，错误是字符串）。

请记住，别名仅用作替代名称，不会创建相同类型的唯一或不同的“版本”。当使用类型别名时，它的功能就像您编写了它所代表的原始类型一样。当然，所有类型信息都将在编译过程中被删除，因此 JavaScript 代码不会有任何 SuccessResponse 或 ErrorResponse 的概念。

总之，类型别名是 TypeScript 中提高代码可读性和可维护性的重要工具。避免忽略类型别名而倾向于复制复杂类型或使用描述性较少的类型组合。通过有效地使用类型别名，您可以创建更清晰、更易于维护的 TypeScript 代码。

## 3.6。避免类型保护

类型保护是 TypeScript 中的一项强大功能，它允许您缩小特定代码块中变量的类型范围。不使用类型保护可能会导致代码安全性降低，并且更容易出错。在本章中，我们将讨论类型保护的重要性并展示如何有效使用它们的示例。

接下来，我们有一个两个接口的示例，它们的并集以及一个采用并集参数来提供形状面积的函数。在此示例中，我们不使用类型保护，也不使用断言，也不检查标记联合形状上的标记。 （稍后将详细介绍类型保护和标签。）这会导致我们出现如下错误：

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape, shapeType: string): number {
  if (shapeType === "circle") {
    return Math.PI * shape.radius ** 2;   //  #A
  } else {
    return shape.sideLength ** 2;         //  #B
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle, "circle")); // 78.53981633974483
```

`#A Error: Property 'radius' does not exist on type 'Shape'. Property 'radius' does not exist on type 'Square'.`
`#B Error: Property 'sideLength' does not exist on type 'Shape'. Property 'sideLength' does not exist on type 'Circle'.`

在前面的示例中，我们有一个 Circle 和一个 Square 接口，它们都属于 Shape 类型。 getArea 函数计算形状的面积，但它不使用类型保护、标记或断言。换句话说，TypeScript 很混乱。

要修复这些错误，解决方案之一是使用类型断言（形状为圆形和形状为方形）来访问每个形状的特定属性。 TypeScript 中的类型断言是一种告诉编译器“相信我，我知道我在做什么”的方法。下面是我们如何使用类型断言修复错误：

```typescript
function getArea(shape: Shape, shapeType: string): number {
  if (shapeType === "circle") {
    return Math.PI * (shape as Circle).radius ** 2;
  } else {
    return (shape as Square).sideLength ** 2;
  }
}
```

如果我们可以使用用户定义的类型保护（isCircle）而不是依赖断言（as）怎么办？这是一个更改后的代码示例，其中我们引入了返回布尔值的 isCircle。我们还可以利用 Circle 和 Square 类型的 type 属性：

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function isCircle(shape: Shape): shape is Circle {  //  #A
  return shape.type === "circle";
}

function getArea(shape: Shape): number {
  if (isCircle(shape)) {
    return Math.PI * shape.radius ** 2;
  } else {
    return shape.sideLength ** 2;
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle));
```

`#A Defining a type guard (isCircle) to narrow the type of the shape`

在此示例中，我们引入了一个名为 isCircle 的类型保护函数，它缩小了 if 块内形状的类型。这使得代码更安全、更高效，因为我们不再需要使用类型断言来访问每个形状的特定属性。 TypeScript 可以根据 if/else 结构判断出它正在处理什么类型，圆形还是方形。然而，在这段代码中很容易引入错误。假设有人无意中更改了 isCircle 的主体，错误地将其与方形类型进行比较。然后我们不会看到任何 TypeScript 错误，但会出现一个运行时错误，导致错误的结果：

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function isCircle(shape: Shape): shape is Circle {
  return shape.type === "square";                 //  #A
}

function getArea(shape: Shape): number {
  if (isCircle(shape)) {
    return Math.PI * shape.radius ** 2;           //  #A
  } else {
    return shape.sideLength ** 2;                 //  #B
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle));                   //  #C
```

`#A Wrong code that is easy to miss`
`#B No TS error`
`#C Run-time error: NaN`

有趣的是，如果我们完全删除 isCircle 并在函数 getArea 中进行类型保护检查，那么 TypeScript 就足够聪明，可以捕获 === square 检查为 true 和尝试访问 radius 属性之间的不一致：

```typescript
function getArea(shape: Shape): number {
  if (shape.type === "square") {
    return Math.PI * shape.radius ** 2;           //  #A
  } else {
    return shape.sideLength ** 2;                 //  #B
  }
}
```

`#A Correctly shows the error: Property 'radius' does not exist on type 'Square'.`
`#B Also correctly shows the error; Property 'sideLength' does not exist on type 'Circle'.`

就类型保护而言，当我们提供用户定义的类型保护（例如，单独的函数 isCircle）时，我们就取消了 TypeScript 内置的控制流分析。除非必要（例如，代码重用），否则最好不要这样做。

让我们继续前面有错误的代码，因为逻辑是相反的（输入 square 返回圆的面积）。要修复它，我们必须返回正确的 if check shape.type === Circle 或 switch area 语句。这是采用理想方法（直接使用类型保护）的代码，没有 TS 错误，并且通过显示有问题的区域更难引入运行时错误：

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape): number {
  if (shape.type === "circle") {
    return Math.PI * shape.radius ** 2;
  } else {
    return shape.sideLength ** 2;
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle));         // ~78.54

const mySquare: Square = { type: "square", sideLength: 5 };
console.log(getArea(mySquare));         // 25
```

在给定的示例中，我们在对象上使用类型属性，因为 typeof 运算符不适合区分对象联合成员。这是因为 typeof 运算符无法区分类和构造函数等对象类型。它将始终返回对象或函数。它仅对检查基本类型（数字、字符串、布尔值）有用，对对象没有用。

随后，我们将看到一个直接在函数中对类型保护中的原语使用 typeof 的示例。下面是一个新示例，其中基本类型 number 和 string 被随机传递给describeType 函数：

```typescript
type PrimitiveType = string | number;

function describeType(value: PrimitiveType): string {
  if (typeof value === "number") {
    return `The number is ${value.toFixed(2)}`;         //  #A
  } else {
    return `The string is "${value.toUpperCase()}"`;    //  #B
  }
}

const numOrStr: PrimitiveType = Math.random() > 0.5 ? 42 : "hello";
console.log(describeType(numOrStr)); //  #C

const numOrStr2: PrimitiveType = Math.random() > 0.5 ? 42 : "hello";
console.log(describeType(numOrStr2));
```

`#A TypeScript knows that 'value' is a number within this block, so no TS errors`
`#B TypeScript knows that 'value' is a string within this block, so no TS errors`
`#C Output could randomly be either number 42 or a string hello`

在此示例中，我们使用类型保护函数 isNumber 来使用 typeof 运算符检查该值是否为数字。然后，describeType 函数使用此类型保护来区分数字值和字符串值，并提供相应的描述。

尽管它看起来不起眼，但实际上在表面之下正在发生大量的活动。与 TypeScript 通过静态类型检查运行时值的方式类似，它还对 JavaScript 的运行时控制流结构执行类型分析，例如 if/else 语句、条件三元组、循环和真实性检查，所有这些都会影响类型。

在 if 语句中，TypeScript 将表达式 typeof value === "number" 标识为一种称为类型保护的特定代码形式。 TypeScript 通过跟踪程序可能采用的潜在执行路径来分析给定位置的值的最具体类型。它类似于有一个起点，然后是可能结果的分支。 TypeScript 检查这些独特的检查（称为类型保护）和分配以创建结果。这种将类型（字符串或数字）精炼为比最初声明的（字符串 | 数字）更精确的过程称为缩小。

总之，在 TypeScript 代码中使用类型保护对于编写更安全、更高效和更具可读性的代码至关重要。通过缩小特定上下文中变量的类型，您可以访问该类型的属性和方法，而无需类型断言或手动类型检查。

## 3.7。类型过于复杂

过于复杂的类型可能是 TypeScript 项目中的常见陷阱。虽然复杂的类型有时是必要的，但过度复杂化可能会导致混乱、可读性降低和维护成本增加。在本章中，我们将讨论与过于复杂的类型相关的问题，并提供如何简化它们的建议。

### 3.7.1。嵌套类型

当您有深度嵌套的类型时，理解它们的结构可能会很困难，这可能会导致错误并增加使用它们时的认知负担。以下示例具有由接口定义的类型（也可以是类型别名），具有三层嵌套属性：

```typescript
interface NestedType {
  firstLevel: {
    secondLevel: {
      thirdLevel: {
        value: string;
      };
    };
  };
}
```

为了简化这种深度嵌套的类型，请考虑将它们分解为更小、更易于管理的类型（如果您愿意，可以使用接口或类型别名）：

```typescript
interface ThirdLevel {
  value: string;
}

interface SecondLevel {
  thirdLevel: ThirdLevel;
}

interface FirstLevel {
  secondLevel: SecondLevel;
}

interface SimplifiedNestedType {
  firstLevel: FirstLevel;
}
```

### 3.7.2。复杂的并集和交集类型

并集和交集类型是 TypeScript 中强大的功能，但过度使用它们可能会导致类型复杂且难以理解。这是一个具有很多联合的疯狂复杂类型的示例：

类型 ComplexType = 字符串 |数量 |布尔 |空 |未定义 |数组<字符串>；

要简化复杂的并集和交集类型，请考虑使用命名类型或接口来提高可读性。我们可以将复杂类型重构为更具可读性的类型。新类型可以在项目的其他地方重用的额外好处是：

```typescript
type PrimitiveType = string | number | boolean;
type NullableType = null | undefined;
type SimplifiedComplexType = PrimitiveType | NullableType | Array<string>;
```

以下是使用上述类型的有效分配的示例：

```typescript
let a: SimplifiedComplexType;
a = "hello";        // string
a = 42;             // number
a = true;           // boolean
a = null;           // null
a = undefined;      // undefined
a = ["apple", "banana", "cherry"]; // Array<string>
```

或者假设我们有一个处理通知、个人资料信息和应用程序首选项的用户设置的应用程序。以下是原始的复杂且潜在冲突的类型：

```typescript
type NotificationSettings = {
  email: boolean;
  push: boolean;
  frequency: "daily" | "weekly" | "monthly";
};

type ProfileSettings = {
  displayName: string;
  biography: string;
  email: string;                          // #A
};

type AppPreferences = {
  theme: "light" | "dark";
  language: string;
  advancedMode: boolean;
};

type UserSettings = NotificationSettings 
  & ProfileSettings 
  & AppPreferences;                       // #B
```

`#A Potential conflict with NotificationSettings`
`#B Unnecessarily complex intersection type`

在此示例中，UserSettings 类型变得过于复杂，甚至与NotificationSettings 和ProfileSettings 中存在的电子邮件属性存在潜在冲突。以下是如何重构 UserSettings 类型以使其更加清晰：

```typescript
type NotificationSettings = {   
  notifications: {
    email: boolean;           // #A
    push: boolean;
    frequency: "daily" | "weekly" | "monthly";
  };
};

type ProfileSettings = {
  profile: {
    displayName: string;
    biography: string;
    contactEmail: string;     // #B
  };
};

type AppPreferences = {
  preferences: {
    theme: "light" | "dark";
    language: string;
    advancedMode: boolean;
  };
};

type UserSettings = NotificationSettings // #C
  & ProfileSettings 
  & AppPreferences;
```

`#A Email just for notifications`
`#B Renamed to avoid confusion with notification email settings`
`#C Composed type for user setting`


通过这种方法，我们可以避免冲突并拥有良好的嵌套属性。

### 3.7.3。过度使用映射和条件类型

映射类型和条件类型提供了很大的灵活性，但过度使用它们可能会创建过于复杂的类型，难以阅读和维护。

```typescript
type Overcomplicated<T extends { [key: string]: any }> = {
  [K in keyof T]: T[K] extends object ? Overcomplicated<T[K]> : T[K];
};
```

提供的 TypeScript 代码定义了一个泛型类型 Overcomplicated。该类型递归地映射对象类型 T 的属性，并将其自身应用于作为对象类型的任何属性。这是其背后的机制：

- 泛型类型 T：Overcomplicated 类型是泛型类型。它需要一个类型参数 T，该参数被限制为对象类型（即 { [key: string]: any }）。这意味着 T 必须是具有任何类型的字符串键和值的对象。

- 映射类型：在 Overcomplicated 类型中，有一个映射类型（[K in keyof T]）。这部分代码迭代类型 T 中的所有键 (K)。

- 条件类型：对于每个键 K，检查相应属性的类型：

- 如果 T[K]（T 中键 K 处的属性类型）是一个对象（T[K] 扩展对象），则 Overcomplicated 会递归地应用于该属性。这意味着如果属性是一个对象，则该对象将再次使用 Overcomplicated 类型，从而允许以相同的方式递归处理嵌套对象。

- 如果 T[K] 不是对象（意味着它是字符串、数字等原始类型），则它保留其原始类型 (T[K])。

本质上，这种类型保持原始类型不变，但如果属性是一个对象，它会递归地将相同的过程应用于该对象，有效地创建一个深度嵌套的类型结构，该结构镜像原始类型，但应用了相同的过于复杂的逻辑所有对象级别。

当您需要应用某些类型转换或递归检查嵌套对象结构的所有属性时，此类型可能很有用，尤其是在复杂的 TypeScript 应用程序中。

为了简化 Overcomplicated 类型，我们可以修改它，以便它不会递归地将自身应用于嵌套对象属性。相反，我们可以按原样保留每个属性的类型，无论它是原始类型还是对象。虽然不完全等同，但这将使类型映射更加简单且复杂性降低。因此，更简单的版本如下：

```typescript
type Simplified<T extends { [key: string]: any }> = {
  [K in keyof T]: T[K];
};
```

让我们看看这个简化版本是如何运作的：

- 通用类型 T：它仍然采用通用类型 T，它是具有任何类型的字符串键和值的对象。

- 映射类型：它使用 [K in keyof T] 映射 T 的每个属性。

- 属性类型：它不是应用条件类型来确定属性是否是对象，而是直接将类型 T[K] 分配给每个属性。这意味着结果类型中的每个属性都将具有与原始类型 T 中相同的类型，无论是原始类型、对象还是任何其他类型。

这种方法通过删除递归方面并统一处理所有属性（无论它们是对象还是基元）来简化类型定义。它有效地创建了一种反映原始类型结构的类型，而没有任何额外的复杂性。

总之，在类型的复杂性和简单性之间取得平衡至关重要。过于复杂的类型会降低可读性并增加维护成本，因此请注意类型的结构和复杂性。将复杂类型分解为更小、更易于管理的部分，并使用命名类型或接口来提高可读性。

## 3.8。忽略只读修饰符

TypeScript 提供了 readonly 修饰符，可用于将属性标记为只读，这意味着它们只能在初始化期间赋值，之后无法修改。在适当的时候忽略使用只读属性可能会导致意想不到的副作用，并使代码更难以推理。本节将讨论使用只读属性的好处，提供其用法示例，并分享将它们合并到 TypeScript 代码中的最佳实践。

readonly 修饰符可以应用于接口、类型和类中的属性。将属性标记为只读会向其他开发人员发出信号，表明其值在初始化后不应修改。但不仅是只读信号，它还由 TypeScript 强制执行！

在此示例中，我们有一个接口只读属性来说明语法（类似于 public 等其他修饰符）：

```typescript
interface ReadonlyPoint {
  readonly x: number;
  readonly y: number;
}

const point: ReadonlyPoint = {
  x: 10,
  y: 10,
};

point.x = 12; //  #A
```

`#A Cannot assign to 'x' because it is a read-only property.`

我们不仅限于接口，我们可以在类定义中使用修饰符，甚至可以与其他修饰符（如 private 和 public）结合使用。

```typescript
class ImmutablePerson {
  public readonly name: string;
  private readonly age: number;

  constructor(name: string, age: number) {
    this.name = name;        //  #A
    this.age = age;
  }
}

const person = new ImmutablePerson('Ivan', 18)
console.log(person.name)    //  #B
person.name = 'Dima'        //  #C
console.log(person.age)     //  #D

person.age = 17             //  #E
```

`#A Assignment in the constructor is okay for read-only properties`
`#B Reading public read-only is okay`
`#C Error: Cannot assign to 'name' because it is a read-only property.`
`#D Error: Property 'age' is private and only accessible within class 'ImmutablePerson'`
`#E Both errors: private and read-only`

是的，readonly 也可以与类型别名一起使用！下面是一个 TypeScript 代码示例，通过具有只读属性的只读类型别名和使用此类型作为其属性中心的类来说明 readonly 修饰符的用法：

```typescript
type ReadonlyPoint = {    //  #A
  readonly x: number;
  readonly y: number;
};

const point: ReadonlyPoint = { //  #B
  x: 10, 
  y: 20 
}; 

point.x = 15;   //  #C

class Shape {   //  #D
  constructor(public readonly center: ReadonlyPoint) {
    //...
  }

  distanceTo(point: ReadonlyPoint): number {
    const dx = point.x - this.center.x;
    const dy = point.y - this.center.y;
    return Math.sqrt(dx * dx + dy * dy);
  }
}

const shape = new Shape(point);         //  #E
shape.center = { x: 0, y: 0 };          //  #F
shape.center.x = 0;

const anotherPoint: ReadonlyPoint = {   // #G
  x: 30, 
  y: 40 
}; 
const distance = shape.distanceTo(anotherPoint); // #H
```

`#A Type alias with read-only properties`
`#B Creating a ReadonlyPoint object`
`#C Error: Cannot assign to 'x' because it is a read-only property`
`#D Class that uses the ReadonlyPoint type for its property`
`#E Creating a Shape instance with the ReadonlyPoint object`
`#F Error: Cannot assign to 'center' because it is a read-only property.`
`#G Error: Cannot assign to 'x' because it is a read-only property.`
`#H Calculating the distance to another point`

在此示例中，我们定义了具有只读属性 x 和 y 的 ReadonlyPoint 类型。 Shape 类使用 ReadonlyPoint 类型作为其 center 属性，该属性也标记为只读。 distanceTo 方法计算形状中心与另一个点之间的距离。当我们尝试修改点对象的 x 属性时，TypeScript 会引发错误，因为它是只读属性。

如果您想查看 readonly 如何帮助防止错误的更实际示例，这里有一个示例，其中我们有一个类，然后 // 使用特定设置初始化配置。稍后在代码中，尝试修改配置将导致编译时错误：

```typescript
class AppConfig {
  readonly databaseUrl: string;
  readonly maxConnections: number;

  constructor(databaseUrl: string, maxConnections: number) {
    this.databaseUrl = databaseUrl;
    this.maxConnections = maxConnections;
  }
}

const config = new AppConfig("https://db.example.com", 10);
config.databaseUrl = "https://db.changedurl.com";             //  #A
```

`#A This line will cause an error`

请注意小写的 readonly 和大写的 Readonly 之间的区别。在 TypeScript 中，这些代表不同的概念。虽然 readonly 指的是本节中讨论的修饰符关键字，但 Readonly 是一种内置实用程序类型，用于将对象类型中的所有属性标记为只读。

使用只读（readonly）属性的好处有很多：

- 不变性：只读属性促进了不可变数据结构的使用，这可以使代码更容易推理，并减少由意外副作用引起的错误的可能性。

- 代码清晰：将属性标记为只读可以清楚地向其他开发人员传达该属性不应被修改的信息，从而使代码的意图更加明确。

- 封装：只读属性通过防止对对象内部状态的外部修改来帮助强制执行正确的封装。

要正确利用只读，请记住最佳实践：

- 对不可变数据使用只读类属性：每当您有初始化后不应更改的数据时，请考虑使用只读属性。这对于表示配置数据、常量或值对象的对象特别有用。

- 将只读应用于接口和类型：定义接口或类型时，如果不应修改属性，请考虑将属性标记为只读。这使得契约更加明确，并有助于确保实现代码遵循所需的行为。

- 使用只读数组以及任何对象和类对象类型时要小心：将数组属性标记为只读时，请注意，它只会防止更改数组引用，而不会更改数组的内容。要创建真正不可变的数组，请考虑使用 ReadonlyArray<T> 或数组类型的 readonly 修饰符。

下面是创建只读数组的示例：

```typescript
interface Data {
  readonly numbers: ReadonlyArray<number>;
}

// Alternatively

interface Data {
  readonly numbers: readonly number[];
}

const data: Data = {
  numbers: [1, 2, 3],
};

data.numbers = [];      //  #A
data.numbers[0] = 0;    //  #B
```

`#A readonly numbers gives an error: Cannot assign to 'numbers' because it is a read-only property.`
`#B ReadonlyArray and readonly T[] give an error: Index signature in type 'readonly number[]' only permits reading.`

然而，我们应该强调 readonly T[] 和 ReadonlyArray<T> 并不“深”。例如，此代码允许更改作为 arry 元素的对象内部的值 val：

```typescript
interface Item {
  val: number;
}

interface Data {
  readonly item: readonly Item[];
}

const objects: Data = {
  item: [{ val: 0 }, { val: 1 }],
};

objects.item[0].val = 1;        // #A
```

`#A Ok, no errors`

为了解决这个问题，我们还需要向 val 添加 readonly ：

```typescript
interface Item {
  readonly val: number;
}
```

然后我们会得到一个错误：无法分配给“val”，因为它是只读属性。

顺便说一下，这里我们也可以同样使用类型别名，如下所示：

```typescript
type Data = {
  readonly numbers: ReadonlyArray<number>;
};
```

```typescript
type Data = {
  readonly numbers: readonly number[];
};
```

您可能还记得浅冻结标准 JavaScript 方法 Object.freeze()，它也可用于强制不变性。然而，冻结和只读在开发过程的不同阶段以不同的方式工作。了解冻结和只读之间的差异是根据应用程序上下文选择正确方法的关键。让我们讨论一下它们在这些级别上的行为有何不同：运行时、深度、上下文、错误处理和意图。

运行时与编译时：

- Object.freeze()：这是一个在运行时工作的 JavaScript 方法。它使对象在创建后不可变。如果您尝试在运行时修改冻结对象，该操作将默默失败，或者在严格模式下抛出错误。

- readonly：这是一个 TypeScript 功能，可在编译时强制执行不变性。它可以防止类或接口的属性在初始分配后被重新分配。编译后生成的 JavaScript 中不存在 TypeScript 的 readonly；这纯粹是一个编译时约束。

不变性的深度：

- Object.freeze()：它是浅层的，这意味着它仅适用于对象的直接属性。嵌套对象不会被冻结并且可以修改。

- readonly：此关键字仅适用于它所附加的属性。 TypeScript 没有内置的深度只读功能。然而，它强制执行的不变性是类型系统的一部分，并且在整个 TypeScript 代码中都受到尊重。

使用上下文：

- Object.freeze()：在 JavaScript 和 TypeScript 中使用。当您需要在运行时（也许在一些初始化逻辑之后）使对象不可变时，它非常有用。

- 只读：仅在 TypeScript 中使用。非常适合在初始设置后不应更改的类属性或接口字段，在优先考虑类型安全的大型应用程序中特别有用。

错误处理：

- Object.freeze()：如果尝试修改冻结对象，则会在运行时发生错误（在严格模式下）或静默失败（在非严格模式下）。

- readonly：错误在 TypeScript 编译过程中抛出，而不是在运行时抛出。

意图和清晰度：

- Object.freeze()：通常当对象需要在程序执行的某个时刻变得不可变时使用。

- readonly：向其他开发人员明确传达属性在设置初始值后不应修改，从而增强代码的可读性和可维护性。

因此，Object.freeze() 提供运行时不变性并且是 JavaScript 的一部分，而 TypeScript 中的 readonly 是一种编译时功能，用于防止属性重新分配。它们之间的选择取决于您是否需要运行时强制或编译时类型安全。

总结本节和这个错误，这是在 TypeScript 中进行只读时需要避免的两个最常见的陷阱：

- 忘记使用只读属性：在适当的时候未能使用只读属性可能会导致意想不到的副作用，并使代码更难以推理。请注意对不变性的需求，并在需要和/或首选不变性时考虑使用只读属性。

- 通过别名修改只读属性：将只读属性传递给函数或将它们分配给变量时要小心，因为它们仍然可以通过别名进行修改。为了防止这种情况，请考虑使用 Object.freeze() 或深度冻结库来实现深度不变性。

通过在 TypeScript 代码中使用只读属性，您可以提高不变性、提高代码清晰度并强制封装。请注意何时使用只读属性，并考虑将它们适当地应用于接口、类型和类。这将导致代码更易于维护和健壮，从而减少出现意外副作用的可能性。

## 3.9。放弃实用程序类型的密钥

TypeScript 提供了多种强大的实用程序类型，可以增强代码的可读性和可维护性，其中之一就是实用程序类型 keyof。在适当的时候忽略使用此实用程序类型可能会导致代码复杂性增加并错失类型安全的机会。在本节中，我们将讨论使用 keyof 的好处，并提供其有效用法的示例。后续章节将介绍其他几种实用程序类型，例如 6.6 中的 Pick 和 Partial。

keyof 实用程序类型用于创建给定类型或接口的属性键的并集。当使用对象键、强制类型安全以及防止拼写错误或不正确的属性访问时，它特别有用。下面是它的详细功能：

```typescript
interface Person {
  name: string;
  age: number;
  hasPet: boolean;
}

type PersonKeys = keyof Person;     //  #A
```

`#A PersonKeys is "name" | "age" | "hasPet"`

注意：在此示例中，我们不能将 PersonKeys 设为接口。

接下来，请观察下图，该图提高了类型安全性。我们定义一个接口 Person，然后在函数签名中将其与 keyof 一起使用，以强制在 getProperty 中仅使用该接口 Person 的属性（键）。如果不是，那么我们会收到错误“Argument of type ... is not allocate”：

```typescript
interface Person {
  name: string;
  age: number;
  hasPet: boolean;
}

function getProperty(person: Person, key: keyof Person) {
  return person[key];
}

const person: Person = {
  name: "Sergiu",
  age: 40,
  hasPet: true
};

console.log(getProperty(person, "name"));        //  #A
console.log(getProperty(person, "address"));      //  #B
```

`#A Sergiu --- Correct usage`
`#B undefined --- Error: Argument of type '"address"' is not assignable to parameter of type 'keyof Person'`

在上面的示例中，使用 keyof Person 作为 key 参数可以强制执行类型安全，并确保只有有效的属性键才能传递给 getProperty 函数。换句话说，TypeScript 会警告我们不允许使用非 Person 类型的字段（例如地址）。

值得一提的是，我们可以将 getProperty 函数修改为通用函数，它适用于任何类型，而不仅仅是 Person：

```typescript
function getProperty<T, K extends keyof T>(person: T, key: K) {
  return person[key];
}
```

我们将在第 6 章中详细介绍泛型，当然还有泛型的错误。

## 3.10。使用对象类型时未充分利用实用程序类型 Extract 和 Partial

在使用 TypeScript 中的类型时，有两个实用程序非常有用：Extract 和 Partial。让我们深入研究一下它们。


### 3.10.1。忽略提取以缩小类型

在 TypeScript 中，Extract 类型是一种条件类型，它允许您通过从联合中提取可分配给另一个类型的所有成员来构造类型。本质上，它从 Type 中“提取”所有可分配给 Union 的联合成员：

```typescript
Extract<Type, Union>;
```

当过滤类型或使用重叠类型时，或者在使用您希望从更广泛的集合中缩小类型范围的库时，这可能很有用。作为一个简短的示例，假设您有一个所有可用颜色的联合类型，并且您想要创建一个仅代表该联合的某些成员的 PrimaryColors 类型：

```typescript
type AvailableColors = "red" | "green" | "blue" | "yellow";
type PrimaryColors = Extract<AvailableColors, "red" | "blue">;

let primary: PrimaryColors;
primary = "red"; // This is fine
primary = "blue"; // This is also fine
primary = "green"; // Error: Type '"green"' is not assignable to type 'PrimaryColors'
```

接下来，假设我们有一个 User 界面，其中包含所有用户信息，包括一些不应自由共享的非常私密的信息。接下来，我们可以使用 Extract 创建类型 SimpleUser 并使用此类型强制仅使用选定的属性（键）以避免泄漏私人用户信息。

```typescript
interface User {
  id: number;
  name: string;
  role: string;
  address: string;
  age: number;
  email: string;
  createdAt: string;
  updatedAt: string;
  dob: Date;
  phone: string;
}

type SimpleUser = Extract<keyof User, "id" | "name" | "role">; //  #A
const simpleUserProperties = ["id", "name", "role"];

function simplify(user: User): SimpleUser {                   //  #B
  
  return Object.keys(user)
    .reduce((obj: any, curr: string) => {
      if (simpleUserProperties.includes(curr))
        obj[curr] = user[curr as keyof User];
      return obj;
  }, {}) as SimpleUser;
}

const fakeUser: User = {
  id: 12345,
  name: "John Doe",
  role: "Admin",
  address: "1234 Elm Street, Anytown, USA",
  age: 35,
  email: "johndoe@example.com",
  createdAt: "2023-01-01T00:00:00.000Z",
  updatedAt: "2023-01-15T00:00:00.000Z",
  dob: new Date("1988-04-23"),
  phone: "555-1234",
};

const simpleUser: SimpleUser = simplify(fakeUser);
console.log(simpleUser);                                    //  #C
```

`#A The new type that has only select keys with Extract`
`#B Function that takes the full User object and returns a simplified object`
`#C { "id": 12345, "name": "John Doe", "role": "Admin" }`

有趣的是，我们还可以将 keyof 的结果传递给 Extract。让我们在下一个例子中看看。想象一下，我们需要创建一个函数来检查两个用户对象之间的属性。我们将定义两个接口，然后使用 Extract 创建类型 SharedProperties 以强制仅使用两个接口的属性（键）。否则，当我们尝试使用其中一个界面中不存在的电子邮件时，我会收到像示例中那样的错误（但两个界面中都存在 id，所以没关系）。

```typescript
interface User {
  id: number;
  name: string;
  role: string;
}

interface Admin {
  id: number;
  name: string;
  role: string;
  permissions: string;
}

type SharedProperties = Extract<keyof User, keyof Admin>;

function compareUsers(
  user: User,
  admin: Admin,
  key: SharedProperties
): boolean {
  return user[key] === admin[key];
}

const user: User = {
  id: 10,
  name: "Zhang",
  role: "user",
};

const admin: Admin = {
  id: 1,
  name: "Zhang",
  role: "admin",
  permissions: "read, write",
};

console.log(compareUsers(user, admin, "id"));          //  #A
console.log(compareUsers(user, admin, "name"));        //  #B
console.log(compareUsers(user, admin, "permissions")); //  #C
```

`#A false --- Correct usage, no errors`
`#B false --- Error: Argument of type '"permissions"' is not assignable to parameter of type 'SharedProperties'`
`#C true--- Correct usage, no errors`

在上面的示例中，使用 Extract 允许我们创建一个 SharedProperties 类型，其中仅包含 User 和 Admin 共有的属性。这确保了compareUsers函数只能接受共享属性键作为其第三个参数。

应该注意的是，在前面的示例中，我们可以将 Extract 替换为类型 (&) 的交集：

```typescript
type SharedProperties = keyof User & keyof Admin;
```

这是因为当提供字符串（键）的联合时，它们的功能相似，即它们作为联合成员的两个圆之间的维恩图重叠区域工作。但是，在其他情况下，尤其是在处理对象类型时，交集和提取之间存在差异。例如，两个对象类型（UserBasicInfo 和 UserPermissions）的交集将生成一个具有每个对象类型的所有属性的类型 (CombinedUserProfile)：

```typescript
type UserBasicInfo = {
  id: number;                  //  #A
  name: string;                //  #A
  email: string;               //  #A
};

type UserPermissions = {
  canEdit: boolean;            //  #B
  canDelete: boolean;          //  #B
  accessLevel: number;         //  #B
};

type CombinedUserProfile = UserBasicInfo & UserPermissions; //  #C
const userProfile: CombinedUserProfile = {                  //  #D
  id: 123,
  //...
  canEdit: true,
};
```

`#A UserBasicInfo represents basic information about a user`
`#B UserPermissions represents permissions assigned to a user`
`#C CombinedUserProfile represents a user's profile along with their permissions`
`#D userProfile has all properties: id, name, email, canEdit, canDelete, accessLevel`

相反，使用对象类型提取永远不会，因为 UserBasicInfo 不可分配给 UserPermissions：

```typescript
type ExtractionType = Extract<UserBasicInfo, UserPermissions>;
```

### 3.10.2。避免将属性标记为可选的 Partial

在 TypeScript 中，Partial 是一种内置实用程序类型，它允许您创建一种类型，使另一种类型的所有属性都可选。当您想要创建一个最初不一定具有所有属性值但可能稍后添加它们的对象时，这非常有用。或者，当您仅针对某些属性而不是全部属性向数据存储（例如数据库）发送更新时。

下面是一个示例，说明如何使用 Partial 自动神奇地创建一个新类型，该类型将具有原始类型的属性，但所有这些属性都变为可选：

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<User>;       //  #A
let user: PartialUser = {};             //  #B

user.id = 1;                            //  #C
user.name = "Aisha";

console.log(user);                      //  #D
```

`#A You can define a 'PartialUser' type that has all the same properties as 'User', but all are optional`
`#B Empty object assignment is valid because all properties are optional`
`#C You can add properties one by one and still it'll work`
`#D Output: { id: 1, name: 'Aisha }`

在此示例中，PartialUser 是一种与 User 具有相同属性的类型，但所有这些属性都是可选的。这意味着您可以创建一个不带任何属性的 PartialUser 对象，然后将它们一一添加。

当使用更新对象的函数时，这非常有用，您只想指定应更新的属性。例如：

```typescript
function updateUser(user: User, updates: Partial<User>): User {
  return { ...user, ...updates };
}

let user: User = { id: 1, name: "Alice", email: "alice@example.com" };
let updatedUser = updateUser(user, { email: "newalice@example.com" });

console.log(updatedUser);               //  #A
```

`#A { id: 1, name: 'Alice', email: 'newalice@example.com' }`

在此示例中， updateUser 是一个函数，它接受 User 和 Partial<User> 并返回应用了更新的新 User。这允许您更新用户的电子邮件，而无需指定 id 和 name 属性。

总之，利用 Extract 和 Partial 等实用程序类型可以帮助您编写更干净、更安全且更易于维护的 TypeScript 代码。请务必在适当的时候利用此实用程序类型来增强代码的可读性和类型安全性。

## 3.11。概括

- 使用接口来定义对象形状。接口可以扩展和重新打开，而类型别名则不能。

- 对复杂类型、交集、并集、元组等使用类型别名。

- 通过删除空的接口并在有意义时合并其他接口来简化接口。在适当的情况下考虑使用交叉类型或定义全新的接口。

- 在对象文字和接口中保持一致的属性顺序。跨类、类型和接口一致地命名属性，以提高可读性。

- 使用类型安全防护代替类型断言 (as)。尽可能实施类型防护，以提供更清晰、更安全的代码。

- 需要时，使用显式注释来防止类型加宽并确保变量始终具有预期的类型，因为 TypeScript 在某些情况下会自动加宽类型，这可能会导致不需要的行为。

- 在有意义的情况下利用只读来防止属性突变，即确保属性一旦初始化就无法更改。它有助于防止属性的意外突变并增强不变性。

- 利用 keyof 和 extract 强制检查属性（键）名称。 keyof 可用于获取类型键的并集，而 Extract 可以从并集中提取特定类型。

