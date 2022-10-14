# Вы не знаете JS: Типы и грамматика
# 

3: Стандартные встроенные объекты

Several times in Chapters 1 and 2, we alluded to various built-ins, usually called "natives," like `String` and `Number`. Let's examine those in detail now.
Несколько раз в Главах 1 и 2 этой книги мы упоминали о различных встроенных объектах таких как `String` и `Number` (встроенные объекты еще называют "примитивы"). Давайте рассмотрим их детальнее.

Here's a list of the most commonly used natives:
Вот список часто используемых примитивов:

* `String()`
* `Number()`
* `Boolean()`
* `Array()`
* `Object()`
* `Function()`
* `RegExp()`
* `Date()`
* `Error()`
* `Symbol()` -- добавлено в ES6!

Как вы можете заметить, на деле все они являются встроенными функциями.

If you're coming to JS from a language like Java, JavaScript's `String()` will look like the `String(..)` constructor you're used to for creating string values. So, you'll quickly observe that you can do things like:
Если вы пришли в JS из языков подобных Java, то, наверное, заметили, что выражение `String()` похоже на конструктор `String(..)`, который используется для создания строковых значений. Скоро станет понятно, что можно делать такие вещи как:

```js
var s = new String( "Hello World!" );

console.log( s.toString() ); // "Hello World!"
```

It *is* true that each of these natives can be used as a native constructor. But what's being constructed may be different than you think.
На самом деле, каждый из этих встроенных объектов может быть использован в качестве конструктора. Но результат вызова конструктора может быть не таким, как вам кажется.

```js
var a = new String( "abc" );

typeof a; // "object" ... не "String"

a instanceof String; // true

Object.prototype.toString.call( a ); // "[object String]"
```

The result of the constructor form of value creation (`new String("abc")`) is an object wrapper around the primitive (`"abc"`) value.
Результатом создания значений с помощью вызова конструктора (`new String("abc")`) является объект-обёртка над примитивным (`"abc"`) значением.

Importantly, `typeof` shows that these objects are not their own special *types*, but more appropriately they are subtypes of the `object` type.
Вызов `typeof` показывает, что у этих объектов нет уникального `типв`, точнее определить их подтипами типа `object`.

This object wrapper can further be observed with:
Обёртку над значением можно посмотреть с помощью:

```js
console.log( a );
```

The output of that statement varies depending on your browser, as developer consoles are free to choose however they feel it's appropriate to serialize the object for developer inspection.
Результаты этого вывода отличаются в зависимости от браузера, т.к. консоли разработчика вправе выбирать как сериализировать объекты для инспектирования их разработчиками.

**Note:** At the time of writing, the latest Chrome prints something like this: `String {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}`. But older versions of Chrome used to just print this: `String {0: "a", 1: "b", 2: "c"}`. The latest Firefox currently prints `String ["a","b","c"]`, but used to print `"abc"` in italics, which was clickable to open the object inspector. Of course, these results are subject to rapid change and your experience may vary.
**Примечание:** В момент написания этой книги последняя версия Chrome печатает что-то вроде этого: `String {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}`. Но старые версии Chrome печатали просто строку: `String {0: "a", 1: "b", 2: "c"}`. Последний Firefox на данный момент выводит `String ["a", "b", "c"]`, но до этого печатал курсивом `"abc"`, на который можно было кликнуть и открыть окно инспектора. Конечно, всё это быстро меняется и ваш опыт может сильно разниться с приведенным в книге.

The point is, `new String("abc")` creates a string wrapper object around `"abc"`, not just the primitive `"abc"` value itself.
Главное, что вы должны понять - `new String("abc")`  создает объект-обертку вокруг `"abc"`, а не просто примитивное значение `"abc"`.

## Внутреннее свойство `[[Class]]`

Values that are `typeof` `"object"` (such as an array) are additionally tagged with an internal `[[Class]]` property (think of this more as an internal *class*ification rather than related to classes from traditional class-oriented coding). This property cannot be accessed directly, but can generally be revealed indirectly by borrowing the default `Object.prototype.toString(..)` method called against the value. For example:
Значения, для которых `typeof` возвращает `"object"` (например, массив), дополнительно помечаются внутренним свойством `[[Class]]` (думайте об этом как о внутренней *класс*ификации, а не о классах из традиционного ООП). Доступ к этому свойству не может быть получен напрямую, но "внутренний класс" можно выявить косвенно путем вызова стандартного метода `Object.prototype.toString()` для значения, у которого вы хотите посмотреть "внутренний класс". К примеру:

```js
Object.prototype.toString.call( [1,2,3] );			// "[object Array]"

Object.prototype.toString.call( /regex-literal/i );	// "[object RegExp]"
```

So, for the array in this example, the internal `[[Class]]` value is `"Array"`, and for the regular expression, it's `"RegExp"`. In most cases, this internal `[[Class]]` value corresponds to the built-in native constructor (see below) that's related to the value, but that's not always the case.
Из этого примера видно, что для массива внутренний `[[Class]]` имеет значение `"Array"`, а для регулярного выражения `"RegExp"`. В большинстве случаев значение внутреннего свойства `[[Class]]` соответствует стандартному встроенному конструктору (см. ниже), связанного со значением, но это не всегда так.

А что насчет примитивных значений? Рассмотрим, `null` и `undefined`:

```js
Object.prototype.toString.call( null );			// "[object Null]"
Object.prototype.toString.call( undefined );	// "[object Undefined]"
```

You'll note that there are no `Null()` or `Undefined()` native constructors, but nevertheless the `"Null"` and `"Undefined"` are the internal `[[Class]]` values exposed.
Вы вероятно заметите, что у `Null()` или `Undefined()` нет своих конструкторов. Тем не менее, `"Null"` и `"Undefined"` являются внутренними значениями `[[Class]]`.

But for the other simple primitives like `string`, `number`, and `boolean`, another behavior actually kicks in, which is usually called "boxing" (see "Boxing Wrappers" section next):
Но для других простых примитивов таких как `string`, `number` и `boolean` используется другое поведение, которое обычно называется "boxing" (см. следующий раздел "Boxing обёртки").

```js
Object.prototype.toString.call( "abc" );	// "[object String]"
Object.prototype.toString.call( 42 );		// "[object Number]"
Object.prototype.toString.call( true );		// "[object Boolean]"
```

In this snippet, each of the simple primitives are automatically boxed by their respective object wrappers, which is why `"String"`, `"Number"`, and `"Boolean"` are revealed as the respective internal `[[Class]]` values.
В этом примере каждый из простых примитивов автоматически помещается в соответствующие объектные оберти, поэтому `"String"`, `"Number"` и `"Boolean"` раскрываются как внутреннем значении `[[Class]]``.

**Note:** The behavior of `toString()` and `[[Class]]` as illustrated here has changed a bit from ES5 to ES6, but we cover those details in the *ES6 & Beyond* title of this series.
**Примечание:** Поведение `toString()` и `[[Class]]` немного изменилось при переходе с ES5 на ES6, но мы раскроем эти детали в оглавлении (предисловии) к книге *ES6 & Beyond* этой серии.

## Boxing Wrappers
## Упаковка

These object wrappers serve a very important purpose. Primitive values don't have properties or methods, so to access `.length` or `.toString()` you need an object wrapper around the value. Thankfully, JS will automatically *box* (aka wrap) the primitive value to fulfill such accesses.
У этих объектных обёрток существует очень важная цель. Примитивные значения не имеют собственных свойств или методов, поэтому для доступа к `.length` или `.toString()` вам нужна объектная обертка вокруг значения. К счастью, JS автоматически *оборачивает* примитивное значение для выполнения таких операций.

```js
var a = "abc";

a.length; // 3
a.toUpperCase(); // "ABC"
```

So, if you're going to be accessing these properties/methods on your string values regularly, like a `i < a.length` condition in a `for` loop for instance, it might seem to make sense to just have the object form of the value from the start, so the JS engine doesn't need to implicitly create it for you.
Так, если вы собираетесь регулярно обращаться к этим свойствам/методам для ваших строковых значений, например, к условию `i < a.length` в цикле `for`, может показаться, что есть смысл с самого начала иметь объектную форму значения, чтобы JS-движок не создавал неявно ее для вас.

But it turns out that's a bad idea. Browsers long ago performance-optimized the common cases like `.length`, which means your program will *actually go slower* if you try to "preoptimize" by directly using the object form (which isn't on the optimized path).
Но оказалось, что это плохая идея. Браузеры уже давно оптимизировали производительность для таких распространенных случаев как `.length`, что означает, что ваша программа будет *действительно работать медленнее*, если вы попытаетесь "предварительно оптимизировать", используя напрямую объектную форму.

In general, there's basically no reason to use the object form directly. It's better to just let the boxing happen implicitly where necessary. In other words, never do things like `new String("abc")`, `new Number(42)`, etc -- always prefer using the literal primitive values `"abc"` and `42`.
В целом, нет никаких причин использовать объектную форму напрямую. Лучше просто позволить оборачиванию неявно происходить там, где это необходимо. Другими словами, никогда не делайте таких вещей как `new String("abc")`, `new Number(42)` и т.д. - всегда старайтесь использовать литеральные примитивные значения `"abc"` и `42`.

### Object Wrapper Gotchas
### Проблемы с объектными обёртками
There are some gotchas with using the object wrappers directly that you should be aware of if you *do* choose to ever use them.
Есть несколько проблем с использованием объектных обёрток напрямую, о которых вам следует знать, если вы решите их использовать.

For example, consider `Boolean` wrapped values:
Рассмотрим значения в булевой обёртке `Boolean`:

```js
var a = new Boolean( false );

if (!a) {
	console.log( "Oops" ); // никогда не запустится
}
```

Проблема в том, что вы создали объектную оболочку вокруг значения `false`, но сами объекты являются "истинными" (см. главу 4), поэтому использование объекта ведет себя противоположно использованию самого лежащего в основе значения `false`, что весьма вопреки обычному ожиданию.

Проблема в том, что вы создали объектную обёртку вокруг значения `false`, но объекты по своей природе "truthy" (см. Главу 4), поэтому поведение обёрнутого значения отличается от поведения самого значения `false`, что противоречит нормальным ожиданиям.

If you want to manually box a primitive value, you can use the `Object(..)` function (no `new` keyword):
Для того чтобы вручную обернуть примитивное значение, ты можешь использовать `Object(..)` функцию (без ключевого слова `new`).

```js
var a = "abc";
var b = new String( a );
var c = Object( a );

typeof a; // "string"
typeof b; // "object"
typeof c; // "object"

b instanceof String; // true
c instanceof String; // true

Object.prototype.toString.call( b ); // "[object String]"
Object.prototype.toString.call( c ); // "[object String]"
```

Again, using the boxed object wrapper directly (like `b` and `c` above) is usually discouraged, but there may be some rare occasions you'll run into where they may be useful.
Обычно не рекомендуется использовать объектную обёртку напрямую (как `b` и `c` выше), но в редких случаях они могут быть полезны.

## Unboxing
## Распаковка

If you have an object wrapper and you want to get the underlying primitive value out, you can use the `valueOf()` method:
Если у вас есть объектная обертка и вы хотите получить примитивное значение, вы можете использовать метод `valueOf()`:

```js
var a = new String( "abc" );
var b = new Number( 42 );
var c = new Boolean( true );

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

Unboxing can also happen implicitly, when using an object wrapper value in a way that requires the primitive value. This process (coercion) will be covered in more detail in Chapter 4, but briefly:
Распаковка может произойти автоматически, когда обёрнутое значение используется в тех местах, где требуется примитивное значение. Более подробно этот процесс ("принуждение") будет рассмотрен в Главе 4, но если кратко:

```js
var a = new String( "abc" );
var b = a + ""; // `b` будет иметь распакованное примитивное значение "abc"

typeof a; // "object"
typeof b; // "string"
```

## Встроенные объекты как конструкторы

For `array`, `object`, `function`, and regular-expression values, it's almost universally preferred that you use the literal form for creating the values, but the literal form creates the same sort of object as the constructor form does (that is, there is no nonwrapped value).
Для регулярных выражений и значений типа `массив`, `объект`, `функция` почти везде предпочтительно использовать литеральную форму для создания значений, но литеральная форма создает такой же объект, как и форма конструктора (то есть, значение обернётся в обёртку).

Just as we've seen above with the other natives, these constructor forms should generally be avoided, unless you really know you need them, mostly because they introduce exceptions and gotchas that you probably don't really *want* to deal with.
Как мы уже видели выше, такие формы конструкторов следует избегать: в основном, потому что они вводят исключения и проблемы, с которыми скорее всего вы не хотите иметь дело.

### `Array(..)`

```js
var a = new Array( 1, 2, 3 );
a; // [1, 2, 3]

var b = [1, 2, 3];
b; // [1, 2, 3]
```

**Note:** The `Array(..)` constructor does not require the `new` keyword in front of it. If you omit it, it will behave as if you have used it anyway. So `Array(1,2,3)` is the same outcome as `new Array(1,2,3)`.
**Примечание:**
Конструктор `Array(...)` не требует наличия ключевого слова `new` перед ним. Если вы опустите его, поведение будет таким же, как и при его использовании. Поэтому `Array(1,2,3)` - это тот же результат, что и `new Array(1,2,3)`.

The `Array` constructor has a special form where if only one `number` argument is passed, instead of providing that value as *contents* of the array, it's taken as a length to "presize the array" (well, sorta).
Конструктор `Array` имеет специальную форму, в которой, если передан только один аргумент `number`, вместо предоставления значения как *содержимого* массива, оно берется в качестве длины для "предварительного вычисления размера массива" (как-то так).

This is a terrible idea. Firstly, you can trip over that form accidentally, as it's easy to forget.
Это ужасная идея. Во-первых, вы можете случайно напороться на эту особенность, так как ее легко забыть.

But more importantly, there's no such thing as actually presizing the array. Instead, what you're creating is an otherwise empty array, but setting the `length` property of the array to the numeric value specified.
Но что еще важнее — нет такого понятия как "предварительное изменение размера массива". Вместо этого создается пустой массив, а свойство `length` массива устанавливается на указанное числовое значение.

An array that has no explicit values in its slots, but has a `length` property that *implies* the slots exist, is a weird exotic type of data structure in JS with some very strange and confusing behavior. The capability to create such a value comes purely from old, deprecated, historical functionalities ("array-like objects" like the `arguments` object).
Массив, в котором не записаны явные значение в своих слотах, но имеющий свойство `length`, которое *подразумевает* существование слотов, является довольно экзотическим типом структуры данных в JS с очень странным и запутанным поведением. Возможность создания такого значения возникает вследствии старых, устаревших функций ("массивоподобные объекты", такие как объект `arguments`).

**Note:** An array with at least one "empty slot" in it is often called a "sparse array."
**Примечание:** Массив, в котором есть хотя бы один "пустой слот", часто называют "разреженным массивом".

Дело не в том, что это еще один пример, когда консоли разработчиков браузера различаются в зависимости от того, как они представляют такой объект, что порождает еще большую путаницу.

Например:

```js
var a = new Array( 3 );

a.length; // 3
a;
```
Сериализация `a` в Chrome (на момент написания): `[ undefined x 3 ]`. **Это действительно прискорбно.** Это означает, что в слотах этого массива есть три `неопределенных` значения, хотя на самом деле слоты не существуют (так называемые "пустые слоты" - тоже плохое название!)

Чтобы визуализировать разницу, попробуйте следующее:

```js
var a = new Array( 3 );
var b = [ undefined, undefined, undefined ];
var c = [];
c.length = 3;

a; // [empty × 3]
b; // [undefined, undefined, undefined]
c; // [empty × 3]
```

**Note:** As you can see with `c` in this example, empty slots in an array can happen after creation of the array. Changing the `length` of an array to go beyond its number of actually-defined slot values, you implicitly introduce empty slots. In fact, you could even call `delete b[1]` in the above snippet, and it would introduce an empty slot into the middle of `b`.
**Примечание:**

Для `b` (в настоящее время в Chrome) вы найдете `[ undefined, undefined, undefined ]` в качестве сериализации, в отличие от `[ undefined x 3 ]` для `a` и `c`. Озадачены? Да, как и все остальные.

Хуже того, на момент написания Firefox сообщает `[ , , , ]` для `a` и `c`. Вы поняли, почему это так запутанно? Посмотрите внимательно. Три запятые означают четыре слота, а не три слота, как мы ожидали.

**Что!?** Firefox добавляет здесь дополнительный `,` в конце своей сериализации, потому что начиная с ES5 разрешены конечные запятые в списках (значения массива, списки свойств и т. д.) (и, таким образом, отбрасываются и игнорируются). Таким образом, если бы вы ввели значение `[ , , ]` в свою программу или консоль, вы фактически получили бы базовое значение, похожее на `[ , , ]` (то есть массив с тремя пустыми слотами). Этот выбор, хотя и сбивает с толку при чтении консоли разработчика, защищается тем, что вместо этого делает поведение копирования и вставки точным.

Если вы сейчас качаете головой или закатываете глаза, вы не одиноки! Пожимает плечами.

К сожалению, это становится хуже. `a` и `b` из приведенного выше фрагмента кода больше, чем просто сбивающий с толку вывод консоли, на самом деле ведут себя одинаково в некоторых случаях **но по-разному в других**:

```js
a.join( "-" ); // "--"
b.join( "-" ); // "--"

a.map(function(v,i){ return i; }); // [ undefined x 3 ]
b.map(function(v,i){ return i; }); // [ 0, 1, 2 ]
```

**Фу.**

Вызов `a.map(..)` *терпит неудачу*, потому что слоты на самом деле не существуют, поэтому `map(..)` нечего перебирать. `join(..)` работает по-другому. По сути, мы можем думать о его реализации примерно так:

```js
function fakeJoin(arr,connector) {
	var str = "";
	for (var i = 0; i < arr.length; i++) {
		if (i > 0) {
			str += connector;
		}
		if (arr[i] !== undefined) {
			str += arr[i];
		}
	}
	return str;
}

var a = new Array( 3 );
fakeJoin( a, "-" ); // "--"
```

Как видите, `join(..)` работает, просто *предполагая*, что слоты существуют, и зацикливается до значения `length`. Что бы ни делала `map(..)` внутри, она (очевидно) не делает такого предположения, поэтому результат от странного массива "пустых слотов" является неожиданным и, вероятно, приведет к сбою.

Итак, если вы хотите *на самом деле* создать массив фактических «неопределенных» значений (а не просто «пустых слотов»), как вы можете это сделать (кроме как вручную)?

```js
var a = Array.apply( null, { length: 3 } );
a; // [ undefined, undefined, undefined ]
```

Озадачены? Ага. Вот примерно как это работает.

`apply(..)` - это утилита, доступная для всех функций, которая вызывает функцию, с которой она используется, но особым образом.

Первый аргумент — это привязка объекта `this` (описанная в заголовке *this & Object Prototypes* этой серии), которая нам здесь не нужна, поэтому мы устанавливаем для нее значение `null`. Предполагается, что второй аргумент должен быть массивом (или чем-то *подобным* массиву, также известному как "array-like object"). Содержимое этого «массива» «распространяется» как аргументы рассматриваемой функции.

Таким образом, `Array.apply(..)` вызывает функцию `Array(..)` и распределяет значения (значения объекта `{length: 3 }`) в качестве аргументов.

Внутри `apply(..)` мы можем представить себе еще один цикл `for` (что-то вроде `join(..)` выше), который идет от `0` до `length` (`3` в нашем случае).

Для каждого индекса он извлекает этот ключ из объекта. Таким образом, если бы параметр объекта массива был назван `arr` внутри функции `apply(..)`, доступ к свойству был бы: `arr[0]`, `arr[1]` и `arr[2]. ]`. Конечно, ни одно из этих свойств не существует в значении объекта `{ length: 3 }`, поэтому все три доступа к этим свойствам вернут значение `undefined`.

Другими словами, это заканчивается вызовом `Array(..)` в основном следующим образом: `Array(undefined,undefined,undefined)`, так мы получаем массив, заполненный `undefined` значениями, а не этими (сумасшедшими) пустыми слотами.

Хотя `Array.apply( null, {length: 3 } )` - это странный и многословный способ создания массива, заполненного `неопределенными` значениями, он **значительно** лучше и надежнее, чем то, что вы получаете с ножным ружьем `Array(3)` пустые слоты.

Итог: **никогда, ни при каких обстоятельствах** вы не должны намеренно создавать и использовать эти экзотические массивы пустых слотов. Просто не делай этого. Они сумасшедшие.

### `Object(..)`, `Function(..)`, and `RegExp(..)`

Конструкторы `Object(..)`/`Function(..)`/`RegExp(..)` также обычно необязательны (и поэтому их обычно следует избегать, если они специально не требуются):

```js
var c = new Object();
c.foo = "bar";
c; // { foo: "bar" }

var d = { foo: "bar" };
d; // { foo: "bar" }

var e = new Function( "a", "return a * 2;" );
var f = function(a) { return a * 2; };
function g(a) { return a * 2; }

var h = new RegExp( "^a*b+", "g" );
var i = /^a*b+/g;
```

Практически нет причин когда-либо использовать форму конструктора `new Object()`, тем более, что она вынуждает вас добавлять свойства одно за другим, а не многие сразу в литеральной форме объекта.

Конструктор `Function` полезен только в самых редких случаях, когда вам нужно динамически определить параметры функции и/или ее тело функции. **Не рассматривайте `Function(..)` просто как альтернативную форму `eval(..)`.** Вам почти никогда не потребуется динамически определять функцию таким образом.

Регулярные выражения, определенные в литеральной форме (`/^a*b+/g`), настоятельно предпочтительнее не только из-за простоты синтаксиса, но и из соображений производительности - механизм JS предварительно компилирует и кэширует их перед выполнением кода. В отличие от других форм конструктора, которые мы видели до сих пор, `RegExp(..)` имеет разумную полезность: для динамического определения шаблона для регулярного выражения.

```js
var name = "Kyle";
var namePattern = new RegExp( "\\b(?:" + name + ")+\\b", "ig" );

var matches = someText.match( namePattern );
```

Такой сценарий время от времени возникает в программах JS, поэтому вам нужно использовать форму `new RegExp("pattern","flags")`.

### `Date(..)` and `Error(..)`

Собственные конструкторы `Date(..)` и `Error(..)` намного полезнее, чем другие нативные конструкторы, потому что ни для одного из них нет литеральной формы.

Чтобы создать значение объекта даты, вы должны использовать `new Date()`. Конструктор `Date(..)` принимает необязательные аргументы для указания используемой даты/времени, но если они опущены, предполагается текущая дата/время.

На сегодняшний день наиболее распространенной причиной создания объекта даты является получение текущего значения метки времени (целое число со знаком в миллисекундах с 1 января 1970 года). Вы можете сделать это, вызвав `getTime()` для экземпляра объекта даты.

Но еще более простой способ — просто вызвать статическую вспомогательную функцию, определенную в ES5: `Date.now()`. А полифилить это для pre-ES5 довольно просто:

```js
if (!Date.now) {
	Date.now = function(){
		return (new Date()).getTime();
	};
}
```

**Note:** If you call `Date()` without `new`, you'll get back a string representation of the date/time at that moment. The exact form of this representation is not specified in the language spec, though browsers tend to agree on something close to: `"Fri Jul 18 2014 00:31:02 GMT-0500 (CDT)"`.
**Примечание:** Если вы вызываете `Date()` без `new`, вы получите строковое представление даты/времени в этот момент. Точная форма этого представления не указана в спецификации языка, хотя браузеры, как правило, соглашаются на что-то близкое к: «Пт, 18 июля 2014 г., 00:31:02 GMT-0500 (CDT)».

Конструктор `Error(..)` (очень похожий на `Array()` выше) ведет себя одинаково с ключевым словом `new`, присутствующим или опущенным.

Основная причина, по которой вы хотите создать объект ошибки, заключается в том, что он захватывает текущий контекст стека выполнения в объект (в большинстве JS-движков после создания он раскрывается как свойство `.stack`, доступное только для чтения). Этот контекст стека включает в себя стек вызовов функций и номер строки, в которой был создан объект ошибки, что значительно упрощает отладку этой ошибки.

Обычно вы используете такой объект ошибки с оператором `throw`:

```js
function foo(x) {
	if (!x) {
		throw new Error( "x wasn't provided" );
	}
	// ..
}
```

Экземпляры объекта ошибки обычно имеют как минимум свойство `message`, а иногда и другие свойства (которые следует рассматривать как доступные только для чтения), например `type`. Однако, помимо проверки вышеупомянутого свойства `stack`, обычно лучше просто вызвать `toString()` для объекта ошибки (либо явно, либо неявно с помощью приведения – см. главу 4), чтобы получить сообщение об ошибке в понятном формате. сообщение.

**Совет:** Технически, в дополнение к общему нативному коду Error(..)`, есть несколько других нативных типов для конкретных ошибок: `EvalError(..)`, `RangeError(..)`, ` ReferenceError(..)`, `SyntaxError(..)`, `TypeError(..)` и `URIError(..)`. Но очень редко можно вручную использовать эти конкретные ошибки. Они автоматически используются, если ваша программа действительно страдает от реального исключения (например, ссылка на необъявленную переменную и получение ошибки `ReferenceError`).

### `Symbol(..)`

В ES6 добавлен дополнительный примитивный тип значения, который называется «Символ». Символы — это специальные «уникальные» (не строго гарантированные!) значения, которые можно использовать в качестве свойств объектов, практически не опасаясь каких-либо столкновений. В первую очередь они предназначены для специального встроенного поведения конструкций ES6, но вы также можете определить свои собственные символы.

Символы можно использовать в качестве имен свойств, но вы не можете увидеть или получить доступ к фактическому значению символа из своей программы или из консоли разработчика. Если вы оцениваете символ в консоли разработчика, то, что показано, выглядит, например, как `Symbol(Symbol.create)`.

В ES6 есть несколько предопределенных символов, доступ к которым осуществляется как к статическим свойствам функционального объекта `Symbol`, например, `Symbol.create`, `Symbol.iterator` и т.д. Чтобы использовать их, сделайте что-то вроде:

```js
obj[Symbol.iterator] = function(){ /*..*/ };
```

Чтобы определить свои собственные символы, используйте родной `Symbol(..)`. Собственный "конструктор" `Symbol(..)` уникален тем, что вам не разрешено использовать с ним `new`, так как это вызовет ошибку.

```js
var mysym = Symbol( "my own symbol" );
mysym;				// Symbol(my own symbol)
mysym.toString();	// "Symbol(my own symbol)"
typeof mysym; 		// "symbol"

var a = { };
a[mysym] = "foobar";

Object.getOwnPropertySymbols( a );
// [ Symbol(my own symbol) ]
```

Хотя символы на самом деле не являются приватными (`Object.getOwnPropertySymbols(..)` отражает объект и раскрывает символы довольно публично), их использование для приватных или специальных свойств, вероятно, является их основным вариантом использования. Для большинства разработчиков они могут заменить имена свойств с префиксами подчеркивания `_`, которые почти всегда по соглашению означают: «Эй, это частное/специальное/внутреннее свойство, так что не трогайте его!»

**Note:** `Symbol`s are *not* `object`s, they are simple scalar primitives.

**Примечание:** "Символы" *не* "объекты", это простые скалярные примитивы.

### Прототипы встроенных объектов

Каждый из встроенных нативных конструкторов имеет свой собственный объект `.prototype` -- `Array.prototype`, `String.prototype` и т.д.

Эти объекты содержат поведение, уникальное для их конкретного подтипа объекта.

Например, все строковые объекты, а также расширенные (через упаковку) примитивы `string`, имеют доступ к поведению по умолчанию как методы, определенные в объекте `String.prototype`.

**Note:** By documentation convention, `String.prototype.XYZ` is shortened to `String#XYZ`, and likewise for all the other `.prototype`s.
**Примечание:** По соглашению, принятому в документации, String.prototype.XYZ сокращается до String#XYZ, и аналогично для всех остальных `.prototype`.

* `String#indexOf(..)`: найти позицию в строке другой подстроки
* `String#charAt(..)`:  получить доступ к символу в позиции в строке
* `String#substr(..)`, `String#substring(..)`, and `String#slice(..)`: извлечь часть строки как новую строку
* `String#toUpperCase()` and `String#toLowerCase()`: создать новую строку, которая преобразуется в верхний или нижний регистр
* `String#trim()`: создать новую строку, лишенную всех конечных или начальных пробелов

Ни один из методов не изменяет строку *на месте*. Модификации (такие как преобразование регистра или обрезка) создают новое значение из существующего значения.

Благодаря делегированию прототипам (см. заголовок *this & Object Prototypes* в этой серии) любое строковое значение может получить доступ к этим методам:

```js
var a = " abc ";

a.indexOf( "c" ); // 3
a.toUpperCase(); // " ABC "
a.trim(); // "abc"
```

Другие прототипы конструкторов содержат поведение, соответствующее их типам, например `Number#toFixed(..)` (строка числа с фиксированным количеством десятичных цифр) и `Array#concat(..)` (объединение массивов). Все функции имеют доступ к `apply(..)`, `call(..)` и `bind(..)`, поскольку они определены в `Function.prototype`.

Но некоторые нативные прототипы не являются *просто* простыми объектами:

```js
typeof Function.prototype;			// "function"
Function.prototype();				// Пустая функция!

RegExp.prototype.toString();		// "/(?:)/" -- пустая регулярка
"abc".match( RegExp.prototype );	// [""]
```

Особенно плохая идея, вы даже можете изменить эти собственные прототипы (а не просто добавить свойства, с которыми вы, вероятно, знакомы):

```js
Array.isArray( Array.prototype );	// true
Array.prototype.push( 1, 2, 3 );	// 3
Array.prototype;					// [1,2,3]

// не оставляйте так, иначе ждите странностей!
// сбрасываем `Array.prototype` на пустой
Array.prototype.length = 0;
```

Как видите, Function.prototype — это функция, RegExp.prototype — регулярное выражение, а Array.prototype — массив. Интересно и круто, да?

#### Прототипы по умолчанию

`Function.prototype` – пустая функция, `RegExp.prototype` – "пустое" (например, несоответствующее) регулярное выражение, а `Array.prototype` – пустой массив, сделайте для них все приятные значения "по умолчанию" для присвоения к переменным, если эти переменные еще не имели значения надлежащего типа.

Например:

```js
function isThisCool(vals,fn,rx) {
	vals = vals || Array.prototype;
	fn = fn || Function.prototype;
	rx = rx || RegExp.prototype;

	return rx.test(
		vals.map( fn ).join( "" )
	);
}

isThisCool();		// true

isThisCool(
	["a","b","c"],
	function(v){ return v.toUpperCase(); },
	/D/
);					// false
```

**Note:** As of ES6, we don't need to use the `vals = vals || ..` default value syntax trick (see Chapter 4) anymore, because default values can be set for parameters via native syntax in the function declaration (see Chapter 5).
**Примечание:** Начиная с ES6, нам не нужно использовать `vals = vals || ..` трюк с синтаксисом значения по умолчанию (см. главу 4) больше не используется, потому что значения по умолчанию могут быть установлены для параметров с помощью собственного синтаксиса в объявлении функции (см. главу 5).

Одним из незначительных побочных преимуществ этого подхода является то, что `.prototype`
уже созданы и встроены, поэтому создаются *только один раз*. Напротив, использование самих значений `[]`, `function(){}` и `/(?:)/` для этих значений по умолчанию (вероятно, в зависимости от реализации движка) будет воссоздавать эти значения (и, возможно, выполнять сборку мусора). их позже) для *каждого вызова* `isThisCool(..)`. Это может быть расточительно по памяти/ЦП.

Кроме того, будьте очень осторожны, чтобы не использовать `Array.prototype` в качестве значения по умолчанию, **которое впоследствии будет изменено**. В этом примере vals используется только для чтения, но если бы вы вместо этого вносили изменения в vals на месте, вы фактически модифицировали бы сам Array.prototype, что привело бы к подводным камням, упомянутым ранее!

**Note:** While we're pointing out these native prototypes and some usefulness, be cautious of relying on them and even more wary of modifying them in anyway. See Appendix A "Native Prototypes" for more discussion.
**Примечание.** Хотя мы указываем на эти нативные прототипы и на некоторую их полезность, будьте осторожны, полагаясь на них, и еще более осторожны, не изменяя их каким-либо образом. См. Приложение A «Родные прототипы» для более подробного обсуждения.

## Обзор

JavaScript предоставляет объектные оболочки вокруг примитивных значений, известных как стандартные объекты (String, Number, Boolean и т. д.). Эти оболочки объектов предоставляют значениям доступ к поведению, подходящему для каждого подтипа объекта (`String#trim()` и `Array#concat(..)`).

Если у вас есть простое скалярное примитивное значение, такое как `"abc"`, и вы обращаетесь к его свойству `length` или какому-то методу `String.prototype`, JS автоматически "упаковывает" значение (оборачивает его в соответствующую оболочку объекта), чтобы доступы к свойствам/методам могли быть выполнены.