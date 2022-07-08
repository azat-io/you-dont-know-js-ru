# Вы не знает JS: Типы и грамматика
# Глава 3: Стандартные встроенные объекты

Several times in Chapters 1 and 2, we alluded to various built-ins, usually called "natives," like `String` and `Number`. Let's examine those in detail now.
Несколько раз в первой и второй главах этой книги мы упоминали различные нативные объекты, такие как `String` и `Number`. Давай рассмотрим их детальнее.

Here's a list of the most commonly used natives:
Вот список часто используемых стандартных объектов:

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

As you can see, these natives are actually built-in functions.
Как вы можете заметить, на деле все они являются встроенными функциями.

If you're coming to JS from a language like Java, JavaScript's `String()` will look like the `String(..)` constructor you're used to for creating string values. So, you'll quickly observe that you can do things like:
Если вы пришли в JS из языков подобных Java, то, наверное, заметили, что выражение `String()` схоже с конструктором `String(..)`, используемым в Java для создания строк. Скоро станет понятно, что можно делать подобные вещи:

```js
var s = new String( "Hello World!" );

console.log( s.toString() ); // "Hello World!"
```

It *is* true that each of these natives can be used as a native constructor. But what's being constructed may be different than you think.
На самом деле каждый из этих встроенных объектов может быть использован в качестве конструктора. Но продукт его вызова может быть не таким, как вам кажется.

```js
var a = new String( "abc" );

typeof a; // "object" ... не "String"

a instanceof String; // true

Object.prototype.toString.call( a ); // "[object String]"
```

The result of the constructor form of value creation (`new String("abc")`) is an object wrapper around the primitive (`"abc"`) value.
Результатом создания значений с помощью вызова конструктора (`new String("abc")`) является объект-обертка над примитивным (`"abc"`) значением.

Importantly, `typeof` shows that these objects are not their own special *types*, but more appropriately they are subtypes of the `object` type.
Вызов `typeof` показывает, что эти объекты не имеют какой-то уникальный `тип`, точнее определить их подтипами типа `object`.

This object wrapper can further be observed with:
Эту обертку над значением можно наблюдать с помощью:

```js
console.log( a );
```

The output of that statement varies depending on your browser, as developer consoles are free to choose however they feel it's appropriate to serialize the object for developer inspection.
Результаты вывода могут отличаться в разных браузерах, так как консоли разработчика вправе выбирать как сериализировать объекты для инспектирования их разработчиками.

**Note:** At the time of writing, the latest Chrome prints something like this: `String {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}`. But older versions of Chrome used to just print this: `String {0: "a", 1: "b", 2: "c"}`. The latest Firefox currently prints `String ["a","b","c"]`, but used to print `"abc"` in italics, which was clickable to open the object inspector. Of course, these results are subject to rapid change and your experience may vary.
**Замечание:** В момент написания этой книги последняя версия Chrome печатает что-то вроде этого: `String {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}`. Но старые версии Chrome печатали просто строку: `String {0: "a", 1: "b", 2: "c"}`. Последний Firefox на данный момент выводит `String ["a", "b", "c"]`, но до этого печатал курсивом `"abc"`, на который можно было кликнуть и открыть окно инспектора. Конечно, все это быстро меняется, и ваш опыт может сильно разниться с приведенным в книге.

The point is, `new String("abc")` creates a string wrapper object around `"abc"`, not just the primitive `"abc"` value itself.
Цель создания обертки над строчкой `"abc"` с помощью `new String("abc")` - не только примитивное значение `"abc"`.

## Внутреннее свойство `[[Class]]`

Values that are `typeof` `"object"` (such as an array) are additionally tagged with an internal `[[Class]]` property (think of this more as an internal *class*ification rather than related to classes from traditional class-oriented coding). This property cannot be accessed directly, but can generally be revealed indirectly by borrowing the default `Object.prototype.toString(..)` method called against the value. For example:
Значения, для которых `typeof` возвращает `"object"` (как, например, массив), дополнительно помечаются внутренним свойством `[[Class]]` (думайте об этом больше как о внутренней *класс*ификации, нежели о классах из традиционного ООП). Это свойство не имеет прямого доступа, но его его можно обнаружить, одолжив стандартный метод `Object.prototype.toString()` и вызвав его с желаемым значением. К примеру:

```js
Object.prototype.toString.call( [1,2,3] );			// "[object Array]"

Object.prototype.toString.call( /regex-literal/i );	// "[object RegExp]"
```

So, for the array in this example, the internal `[[Class]]` value is `"Array"`, and for the regular expression, it's `"RegExp"`. In most cases, this internal `[[Class]]` value corresponds to the built-in native constructor (see below) that's related to the value, but that's not always the case.
Видно, что для массива в этом примере, внутренний `[[Class]]` имеет значение `"Array"`, а для регулярного выражения `"RegExp"`. В большинстве случаев, значение внутреннего свойства `[[Class]]` соответствует стандартному встроенному конструктору (см. ниже), связанного со значением, хотя это не всегда так.

What about primitive values? First, `null` and `undefined`:
А что насчет примитивных значений? Рассмотрим, `null` и `undefined`:

```js
Object.prototype.toString.call( null );			// "[object Null]"
Object.prototype.toString.call( undefined );	// "[object Undefined]"
```

You'll note that there are no `Null()` or `Undefined()` native constructors, but nevertheless the `"Null"` and `"Undefined"` are the internal `[[Class]]` values exposed.
Вы заметите, что нет собственных конструкторов Null() или Undefined(), но Null и Undefined, тем не менее, являются внутренними общедоступными значениями `[[Class]]`.

But for the other simple primitives like `string`, `number`, and `boolean`, another behavior actually kicks in, which is usually called "boxing" (see "Boxing Wrappers" section next):
Но для других простых примитивов, таких как `string`, `number` и `boolean`, на самом деле срабатывает другое поведение, которое обычно называется "упаковкой" (см. раздел "Обертки упаковки" далее):

```js
Object.prototype.toString.call( "abc" );	// "[object String]"
Object.prototype.toString.call( 42 );		// "[object Number]"
Object.prototype.toString.call( true );		// "[object Boolean]"
```

In this snippet, each of the simple primitives are automatically boxed by their respective object wrappers, which is why `"String"`, `"Number"`, and `"Boolean"` are revealed as the respective internal `[[Class]]` values.
В этом фрагменте каждый из простых примитивов автоматически упаковывается соответствующими объектными оболочками, поэтому `"String", `"Number" и `"Boolean"` раскрываются как соответствующие внутренние `[[Class]]` значения.

**Note:** The behavior of `toString()` and `[[Class]]` as illustrated here has changed a bit from ES5 to ES6, but we cover those details in the *ES6 & Beyond* title of this series.
**Примечание:** Поведение `toString()` и `[[Class]]`, как показано здесь, немного изменилось с ES5 на ES6, но мы рассмотрим эти детали в заголовке *ES6 и далее* этой серии. .

## Обертки

These object wrappers serve a very important purpose. Primitive values don't have properties or methods, so to access `.length` or `.toString()` you need an object wrapper around the value. Thankfully, JS will automatically *box* (aka wrap) the primitive value to fulfill such accesses.
Эти оболочки объектов служат очень важной цели. Примитивные значения не имеют свойств или методов, поэтому для доступа к `.length` или `.toString()` вам нужна оболочка объекта вокруг значения. К счастью, JS автоматически *упаковывает* (или обертывает) примитивное значение для выполнения таких обращений.

```js
var a = "abc";

a.length; // 3
a.toUpperCase(); // "ABC"
```

So, if you're going to be accessing these properties/methods on your string values regularly, like a `i < a.length` condition in a `for` loop for instance, it might seem to make sense to just have the object form of the value from the start, so the JS engine doesn't need to implicitly create it for you.
Итак, если вы собираетесь регулярно обращаться к этим свойствам/методам строковых значений, например, к условию `i < a.length` в цикле `for`, может показаться, что имеет смысл просто иметь объектную форму значения с самого начала, поэтому движку JS не нужно неявно создавать его для вас.

But it turns out that's a bad idea. Browsers long ago performance-optimized the common cases like `.length`, which means your program will *actually go slower* if you try to "preoptimize" by directly using the object form (which isn't on the optimized path).
Но оказывается, это плохая идея. Браузеры уже давно оптимизировали производительность в таких распространенных случаях, как `.length`, что означает, что ваша программа *фактически будет работать медленнее*, если вы попытаетесь "предварительно оптимизировать" непосредственно с помощью формы объекта (которая не есть путём к оптимизации).

In general, there's basically no reason to use the object form directly. It's better to just let the boxing happen implicitly where necessary. In other words, never do things like `new String("abc")`, `new Number(42)`, etc -- always prefer using the literal primitive values `"abc"` and `42`.
В общем, нет никакой причины использовать форму объекта напрямую. Лучше просто позволить боксу происходить неявно там, где это необходимо. Другими словами, никогда не делайте такие вещи, как `new String("abc")`, `new Number(42)` и т. д. Всегда предпочитайте использовать литеральные примитивные значения `"abc"` и `42`.

### Object Wrapper Gotchas
### Ошибки объектной оболочки

There are some gotchas with using the object wrappers directly that you should be aware of if you *do* choose to ever use them.
Есть некоторые подводные камни, связанные с непосредственным использованием объектов-оболочек, о которых вам следует знать, если вы *решите* когда-либо их использовать.

For example, consider `Boolean` wrapped values:
Например, рассмотрите `Boolean` обёртку:

```js
var a = new Boolean( false );

if (!a) {
	console.log( "Oops" ); // никогда не запустится
}
```

The problem is that you've created an object wrapper around the `false` value, but objects themselves are "truthy" (see Chapter 4), so using the object behaves oppositely to using the underlying `false` value itself, which is quite contrary to normal expectation.
Проблема в том, что вы создали объектную оболочку вокруг значения `false`, но сами объекты являются "истинными" (см. главу 4), поэтому использование объекта ведет себя противоположно использованию самого лежащего в основе значения `false`, что весьма вопреки обычному ожиданию.

If you want to manually box a primitive value, you can use the `Object(..)` function (no `new` keyword):
Если вы хотите вручную упаковать примитивное значение, вы можете использовать функцию `Object(..)` (без ключевого слова `new`):

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
Опять же, прямое использование упакованных объектов-оболочек (например, `b` и `c` выше) обычно не рекомендуется, но могут быть некоторые редкие случаи, когда вы столкнетесь с тем, что они могут быть полезны.

## Unboxing
## Распаковка

If you have an object wrapper and you want to get the underlying primitive value out, you can use the `valueOf()` method:
Если у вас есть оболочка объекта и вы хотите получить базовое примитивное значение, вы можете использовать метод `valueOf()`:

```js
var a = new String( "abc" );
var b = new Number( 42 );
var c = new Boolean( true );

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

Unboxing can also happen implicitly, when using an object wrapper value in a way that requires the primitive value. This process (coercion) will be covered in more detail in Chapter 4, but briefly:
Распаковка также может происходить неявно, при использовании значения оболочки объекта способом, требующим примитивного значения. Этот процесс (принуждение) будет рассмотрен более подробно в главе 4, но кратко:

```js
var a = new String( "abc" );
var b = a + ""; // `b` имеет распакованное примитивное значение "abc"

typeof a; // "object"
typeof b; // "string"
```

## Natives as Constructors
## Встроенные объекты как конструкторы

For `array`, `object`, `function`, and regular-expression values, it's almost universally preferred that you use the literal form for creating the values, but the literal form creates the same sort of object as the constructor form does (that is, there is no nonwrapped value).
Для `массива`, `объекта`, `функции` и значений регулярных выражений почти повсеместно предпочтительно использовать литеральную форму для создания значений, но литеральная форма создает тот же тип объекта, что и форма конструктора ( то есть нет неупакованного значения).

Just as we've seen above with the other natives, these constructor forms should generally be avoided, unless you really know you need them, mostly because they introduce exceptions and gotchas that you probably don't really *want* to deal with.
Как мы видели выше с другими стандартными объектами, этих форм-конструкторов обычно следует избегать, если вы действительно не уверены, что они вам нужны, в основном потому, что они вводят исключения и ловушки, с которыми вы, вероятно, не *хотите* иметь дело.

### `Array(..)`

```js
var a = new Array( 1, 2, 3 );
a; // [1, 2, 3]

var b = [1, 2, 3];
b; // [1, 2, 3]
```

**Note:** The `Array(..)` constructor does not require the `new` keyword in front of it. If you omit it, it will behave as if you have used it anyway. So `Array(1,2,3)` is the same outcome as `new Array(1,2,3)`.
**Примечание:** Конструктор `Array(..)` не требует ключевого слова `new` перед ним. Если вы его опустите, он будет вести себя так, как будто вы его все равно использовали. Таким образом, «Массив (1,2,3)» — это тот же результат, что и «новый массив (1,2,3)».

The `Array` constructor has a special form where if only one `number` argument is passed, instead of providing that value as *contents* of the array, it's taken as a length to "presize the array" (well, sorta).
Конструктор `Array` имеет специальную форму, в которой, если передается только один аргумент `number`, вместо того, чтобы предоставлять это значение как *содержимое* массива, оно берется как длина для «предварительного размера массива» (ну, вроде как).

This is a terrible idea. Firstly, you can trip over that form accidentally, as it's easy to forget.
Это ужасная идея. Во-первых, вы можете случайно споткнуться об эту форму, так как ее легко забыть.

But more importantly, there's no such thing as actually presizing the array. Instead, what you're creating is an otherwise empty array, but setting the `length` property of the array to the numeric value specified.
Но что еще более важно, нет такой вещи, как предварительное определение размера массива. Вместо этого вы создаете пустой массив, но устанавливаете для свойства `length` массива указанное числовое значение.

An array that has no explicit values in its slots, but has a `length` property that *implies* the slots exist, is a weird exotic type of data structure in JS with some very strange and confusing behavior. The capability to create such a value comes purely from old, deprecated, historical functionalities ("array-like objects" like the `arguments` object).
Массив, который не имеет явных значений в своих слотах, но имеет свойство `length`, которое *подразумевает*, что слоты существуют, является странным экзотическим типом структуры данных в JS с очень странным и запутанным поведением. Возможность создания такого значения исходит исключительно из старых, устаревших, исторических функций («массивоподобные объекты», такие как объект «аргументы»).

**Note:** An array with at least one "empty slot" in it is often called a "sparse array."
**Примечание.** Массив, в котором есть хотя бы один «пустой слот», часто называют «разреженным массивом».

It doesn't help matters that this is yet another example where browser developer consoles vary on how they represent such an object, which breeds more confusion.
Дело не в том, что это еще один пример, когда консоли разработчиков браузера различаются в зависимости от того, как они представляют такой объект, что порождает еще большую путаницу.

For example:
Например:

```js
var a = new Array( 3 );

a.length; // 3
a;
```

The serialization of `a` in Chrome is (at the time of writing): `[ undefined x 3 ]`. **This is really unfortunate.** It implies that there are three `undefined` values in the slots of this array, when in fact the slots do not exist (so-called "empty slots" -- also a bad name!)Сериализация `a` в Chrome (на момент написания): `[ undefined x 3 ]`. **Это действительно прискорбно.** Это означает, что в слотах этого массива есть три `неопределенных` значения, хотя на самом деле слоты не существуют (так называемые "пустые слоты" - тоже плохое название!)

To visualize the difference, try this:
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
**Примечание:** Как видно из `c` в этом примере, пустые слоты в массиве могут появиться после создания массива. Изменяя «длину» массива так, чтобы он превышал количество фактически определенных значений слотов, вы неявно вводите пустые слоты. На самом деле, вы могли бы даже вызвать `delete b[1]` в приведенном выше фрагменте, и это добавит пустой слот в середину `b`.

For `b` (in Chrome, currently), you'll find `[ undefined, undefined, undefined ]` as the serialization, as opposed to `[ undefined x 3 ]` for `a` and `c`. Confused? Yeah, so is everyone else.
Для `b` (в настоящее время в Chrome) вы найдете `[ undefined, undefined, undefined ]` в качестве сериализации, в отличие от `[ undefined x 3 ]` для `a` и `c`. Озадачены? Да, как и все остальные.

Worse than that, at the time of writing, Firefox reports `[ , , , ]` for `a` and `c`. Did you catch why that's so confusing? Look closely. Three commas implies four slots, not three slots like we'd expect.
Хуже того, на момент написания Firefox сообщает `[ , , , ]` для `a` и `c`. Вы поняли, почему это так запутанно? Посмотрите внимательно. Три запятые означают четыре слота, а не три слота, как мы ожидали.

**What!?** Firefox puts an extra `,` on the end of their serialization here because as of ES5, trailing commas in lists (array values, property lists, etc.) are allowed (and thus dropped and ignored). So if you were to type in a `[ , , , ]` value into your program or the console, you'd actually get the underlying value that's like `[ , , ]` (that is, an array with three empty slots). This choice, while confusing if reading the developer console, is defended as instead making copy-n-paste behavior accurate.
**Что!?** Firefox добавляет здесь дополнительный `,` в конце своей сериализации, потому что начиная с ES5 разрешены конечные запятые в списках (значения массива, списки свойств и т. д.) (и, таким образом, отбрасываются и игнорируются). Таким образом, если бы вы ввели значение `[ , , ]` в свою программу или консоль, вы фактически получили бы базовое значение, похожее на `[ , , ]` (то есть массив с тремя пустыми слотами). Этот выбор, хотя и сбивает с толку при чтении консоли разработчика, защищается тем, что вместо этого делает поведение копирования и вставки точным.

If you're shaking your head or rolling your eyes about now, you're not alone! Shrugs.
Если вы сейчас качаете головой или закатываете глаза, вы не одиноки! Пожимает плечами.

Unfortunately, it gets worse. More than just confusing console output, `a` and `b` from the above code snippet actually behave the same in some cases **but differently in others**:
К сожалению, это становится хуже. `a` и `b` из приведенного выше фрагмента кода больше, чем просто сбивающий с толку вывод консоли, на самом деле ведут себя одинаково в некоторых случаях **но по-разному в других**:

```js
a.join( "-" ); // "--"
b.join( "-" ); // "--"

a.map(function(v,i){ return i; }); // [ undefined x 3 ]
b.map(function(v,i){ return i; }); // [ 0, 1, 2 ]
```

**Ugh.**
**Фу.**

The `a.map(..)` call *fails* because the slots don't actually exist, so `map(..)` has nothing to iterate over. `join(..)` works differently. Basically, we can think of it implemented sort of like this:
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

As you can see, `join(..)` works by just *assuming* the slots exist and looping up to the `length` value. Whatever `map(..)` does internally, it (apparently) doesn't make such an assumption, so the result from the strange "empty slots" array is unexpected and likely to cause failure.
Как видите, `join(..)` работает, просто *предполагая*, что слоты существуют, и зацикливается до значения `length`. Что бы ни делала `map(..)` внутри, она (очевидно) не делает такого предположения, поэтому результат от странного массива "пустых слотов" является неожиданным и, вероятно, приведет к сбою.

So, if you wanted to *actually* create an array of actual `undefined` values (not just "empty slots"), how could you do it (besides manually)?
Итак, если вы хотите *на самом деле* создать массив фактических «неопределенных» значений (а не просто «пустых слотов»), как вы можете это сделать (кроме как вручную)?

```js
var a = Array.apply( null, { length: 3 } );
a; // [ undefined, undefined, undefined ]
```

Confused? Yeah. Here's roughly how it works.
Озадачены? Ага. Вот примерно как это работает.

`apply(..)` is a utility available to all functions, which calls the function it's used with but in a special way.
`apply(..)` - это утилита, доступная для всех функций, которая вызывает функцию, с которой она используется, но особым образом.

The first argument is a `this` object binding (covered in the *this & Object Prototypes* title of this series), which we don't care about here, so we set it to `null`. The second argument is supposed to be an array (or something *like* an array -- aka an "array-like object"). The contents of this "array" are "spread" out as arguments to the function in question.
Первый аргумент — это привязка объекта `this` (описанная в заголовке *this & Object Prototypes* этой серии), которая нам здесь не нужна, поэтому мы устанавливаем для нее значение `null`. Предполагается, что второй аргумент должен быть массивом (или чем-то *подобным* массиву, также известному как "array-like object"). Содержимое этого «массива» «распространяется» как аргументы рассматриваемой функции.

So, `Array.apply(..)` is calling the `Array(..)` function and spreading out the values (of the `{ length: 3 }` object value) as its arguments.
Таким образом, `Array.apply(..)` вызывает функцию `Array(..)` и распределяет значения (значения объекта `{length: 3 }`) в качестве аргументов.

Inside of `apply(..)`, we can envision there's another `for` loop (kinda like `join(..)` from above) that goes from `0` up to, but not including, `length` (`3` in our case).
Внутри `apply(..)` мы можем представить себе еще один цикл `for` (что-то вроде `join(..)` выше), который идет от `0` до `length` (`3` в нашем случае).

For each index, it retrieves that key from the object. So if the array-object parameter was named `arr` internally inside of the `apply(..)` function, the property access would effectively be `arr[0]`, `arr[1]`, and `arr[2]`. Of course, none of those properties exist on the `{ length: 3 }` object value, so all three of those property accesses would return the value `undefined`.
Для каждого индекса он извлекает этот ключ из объекта. Таким образом, если бы параметр объекта массива был назван `arr` внутри функции `apply(..)`, доступ к свойству был бы: `arr[0]`, `arr[1]` и `arr[2]. ]`. Конечно, ни одно из этих свойств не существует в значении объекта `{ length: 3 }`, поэтому все три доступа к этим свойствам вернут значение `undefined`.

In other words, it ends up calling `Array(..)` basically like this: `Array(undefined,undefined,undefined)`, which is how we end up with an array filled with `undefined` values, and not just those (crazy) empty slots.
Другими словами, это заканчивается вызовом `Array(..)` в основном следующим образом: `Array(undefined,undefined,undefined)`, так мы получаем массив, заполненный `undefined` значениями, а не этими (сумасшедшими) пустыми слотами.

While `Array.apply( null, { length: 3 } )` is a strange and verbose way to create an array filled with `undefined` values, it's **vastly** better and more reliable than what you get with the footgun'ish `Array(3)` empty slots.
Хотя `Array.apply( null, {length: 3 } )` - это странный и многословный способ создания массива, заполненного `неопределенными` значениями, он **значительно** лучше и надежнее, чем то, что вы получаете с ножным ружьем `Array(3)` пустые слоты.

Bottom line: **never ever, under any circumstances**, should you intentionally create and use these exotic empty-slot arrays. Just don't do it. They're nuts.
Итог: **никогда, ни при каких обстоятельствах** вы не должны намеренно создавать и использовать эти экзотические массивы пустых слотов. Просто не делай этого. Они сумасшедшие.

### `Object(..)`, `Function(..)`, and `RegExp(..)`

The `Object(..)`/`Function(..)`/`RegExp(..)` constructors are also generally optional (and thus should usually be avoided unless specifically called for):
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

There's practically no reason to ever use the `new Object()` constructor form, especially since it forces you to add properties one-by-one instead of many at once in the object literal form.
Практически нет причин когда-либо использовать форму конструктора `new Object()`, тем более, что она вынуждает вас добавлять свойства одно за другим, а не многие сразу в литеральной форме объекта.

The `Function` constructor is helpful only in the rarest of cases, where you need to dynamically define a function's parameters and/or its function body. **Do not just treat `Function(..)` as an alternate form of `eval(..)`.** You will almost never need to dynamically define a function in this way.
Конструктор `Function` полезен только в самых редких случаях, когда вам нужно динамически определить параметры функции и/или ее тело функции. **Не рассматривайте `Function(..)` просто как альтернативную форму `eval(..)`.** Вам почти никогда не потребуется динамически определять функцию таким образом.

Regular expressions defined in the literal form (`/^a*b+/g`) are strongly preferred, not just for ease of syntax but for performance reasons -- the JS engine precompiles and caches them before code execution. Unlike the other constructor forms we've seen so far, `RegExp(..)` has some reasonable utility: to dynamically define the pattern for a regular expression.
Регулярные выражения, определенные в литеральной форме (`/^a*b+/g`), настоятельно предпочтительнее не только из-за простоты синтаксиса, но и из соображений производительности - механизм JS предварительно компилирует и кэширует их перед выполнением кода. В отличие от других форм конструктора, которые мы видели до сих пор, `RegExp(..)` имеет разумную полезность: для динамического определения шаблона для регулярного выражения.

```js
var name = "Kyle";
var namePattern = new RegExp( "\\b(?:" + name + ")+\\b", "ig" );

var matches = someText.match( namePattern );
```

This kind of scenario legitimately occurs in JS programs from time to time, so you'd need to use the `new RegExp("pattern","flags")` form.
Такой сценарий время от времени возникает в программах JS, поэтому вам нужно использовать форму `new RegExp("pattern","flags")`.

### `Date(..)` and `Error(..)`

The `Date(..)` and `Error(..)` native constructors are much more useful than the other natives, because there is no literal form for either.
Собственные конструкторы `Date(..)` и `Error(..)` намного полезнее, чем другие нативные конструкторы, потому что ни для одного из них нет литеральной формы.

To create a date object value, you must use `new Date()`. The `Date(..)` constructor accepts optional arguments to specify the date/time to use, but if omitted, the current date/time is assumed.
Чтобы создать значение объекта даты, вы должны использовать `new Date()`. Конструктор `Date(..)` принимает необязательные аргументы для указания используемой даты/времени, но если они опущены, предполагается текущая дата/время.

By far the most common reason you construct a date object is to get the current timestamp value (a signed integer number of milliseconds since Jan 1, 1970). You can do this by calling `getTime()` on a date object instance.
На сегодняшний день наиболее распространенной причиной создания объекта даты является получение текущего значения метки времени (целое число со знаком в миллисекундах с 1 января 1970 года). Вы можете сделать это, вызвав `getTime()` для экземпляра объекта даты.

But an even easier way is to just call the static helper function defined as of ES5: `Date.now()`. And to polyfill that for pre-ES5 is pretty easy:
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

The `Error(..)` constructor (much like `Array()` above) behaves the same with the `new` keyword present or omitted.
Конструктор `Error(..)` (очень похожий на `Array()` выше) ведет себя одинаково с ключевым словом `new`, присутствующим или опущенным.

The main reason you'd want to create an error object is that it captures the current execution stack context into the object (in most JS engines, revealed as a read-only `.stack` property once constructed). This stack context includes the function call-stack and the line-number where the error object was created, which makes debugging that error much easier.
Основная причина, по которой вы хотите создать объект ошибки, заключается в том, что он захватывает текущий контекст стека выполнения в объект (в большинстве JS-движков после создания он раскрывается как свойство `.stack`, доступное только для чтения). Этот контекст стека включает в себя стек вызовов функций и номер строки, в которой был создан объект ошибки, что значительно упрощает отладку этой ошибки.

You would typically use such an error object with the `throw` operator:
Обычно вы используете такой объект ошибки с оператором `throw`:

```js
function foo(x) {
	if (!x) {
		throw new Error( "x wasn't provided" );
	}
	// ..
}
```

Error object instances generally have at least a `message` property, and sometimes other properties (which you should treat as read-only), like `type`. However, other than inspecting the above-mentioned `stack` property, it's usually best to just call `toString()` on the error object (either explicitly, or implicitly through coercion -- see Chapter 4) to get a friendly-formatted error message.
Экземпляры объекта ошибки обычно имеют как минимум свойство `message`, а иногда и другие свойства (которые следует рассматривать как доступные только для чтения), например `type`. Однако, помимо проверки вышеупомянутого свойства `stack`, обычно лучше просто вызвать `toString()` для объекта ошибки (либо явно, либо неявно с помощью приведения – см. главу 4), чтобы получить сообщение об ошибке в понятном формате. сообщение.

**Tip:** Technically, in addition to the general `Error(..)` native, there are several other specific-error-type natives: `EvalError(..)`, `RangeError(..)`, `ReferenceError(..)`, `SyntaxError(..)`, `TypeError(..)`, and `URIError(..)`. But it's very rare to manually use these specific error natives. They are automatically used if your program actually suffers from a real exception (such as referencing an undeclared variable and getting a `ReferenceError` error).
**Совет:** Технически, в дополнение к общему нативному коду Error(..)`, есть несколько других нативных типов для конкретных ошибок: `EvalError(..)`, `RangeError(..)`, ` ReferenceError(..)`, `SyntaxError(..)`, `TypeError(..)` и `URIError(..)`. Но очень редко можно вручную использовать эти конкретные ошибки. Они автоматически используются, если ваша программа действительно страдает от реального исключения (например, ссылка на необъявленную переменную и получение ошибки `ReferenceError`).

### `Symbol(..)`

New as of ES6, an additional primitive value type has been added, called "Symbol". Symbols are special "unique" (not strictly guaranteed!) values that can be used as properties on objects with little fear of any collision. They're primarily designed for special built-in behaviors of ES6 constructs, but you can also define your own symbols.
В ES6 добавлен дополнительный примитивный тип значения, который называется «Символ». Символы — это специальные «уникальные» (не строго гарантированные!) значения, которые можно использовать в качестве свойств объектов, практически не опасаясь каких-либо столкновений. В первую очередь они предназначены для специального встроенного поведения конструкций ES6, но вы также можете определить свои собственные символы.

Symbols can be used as property names, but you cannot see or access the actual value of a symbol from your program, nor from the developer console. If you evaluate a symbol in the developer console, what's shown looks like `Symbol(Symbol.create)`, for example.
Символы можно использовать в качестве имен свойств, но вы не можете увидеть или получить доступ к фактическому значению символа из своей программы или из консоли разработчика. Если вы оцениваете символ в консоли разработчика, то, что показано, выглядит, например, как `Symbol(Symbol.create)`.

There are several predefined symbols in ES6, accessed as static properties of the `Symbol` function object, like `Symbol.create`, `Symbol.iterator`, etc. To use them, do something like:
В ES6 есть несколько предопределенных символов, доступ к которым осуществляется как к статическим свойствам функционального объекта `Symbol`, например, `Symbol.create`, `Symbol.iterator` и т.д. Чтобы использовать их, сделайте что-то вроде:

```js
obj[Symbol.iterator] = function(){ /*..*/ };
```

To define your own custom symbols, use the `Symbol(..)` native. The `Symbol(..)` native "constructor" is unique in that you're not allowed to use `new` with it, as doing so will throw an error.
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

While symbols are not actually private (`Object.getOwnPropertySymbols(..)` reflects on the object and reveals the symbols quite publicly), using them for private or special properties is likely their primary use-case. For most developers, they may take the place of property names with `_` underscore prefixes, which are almost always by convention signals to say, "hey, this is a private/special/internal property, so leave it alone!"
Хотя символы на самом деле не являются приватными (`Object.getOwnPropertySymbols(..)` отражает объект и раскрывает символы довольно публично), их использование для приватных или специальных свойств, вероятно, является их основным вариантом использования. Для большинства разработчиков они могут заменить имена свойств с префиксами подчеркивания `_`, которые почти всегда по соглашению означают: «Эй, это частное/специальное/внутреннее свойство, так что не трогайте его!»

**Note:** `Symbol`s are *not* `object`s, they are simple scalar primitives.
**Примечание:** "Символы" *не* "объекты", это простые скалярные примитивы.

### Native Prototypes
### Прототипы встроенных объектов

Each of the built-in native constructors has its own `.prototype` object -- `Array.prototype`, `String.prototype`, etc.
Каждый из встроенных нативных конструкторов имеет свой собственный объект `.prototype` -- `Array.prototype`, `String.prototype` и т.д.

These objects contain behavior unique to their particular object subtype.
Эти объекты содержат поведение, уникальное для их конкретного подтипа объекта.

For example, all string objects, and by extension (via boxing) `string` primitives, have access to default behavior as methods defined on the `String.prototype` object.
Например, все строковые объекты, а также расширенные (через упаковку) примитивы `string`, имеют доступ к поведению по умолчанию как методы, определенные в объекте `String.prototype`.

**Note:** By documentation convention, `String.prototype.XYZ` is shortened to `String#XYZ`, and likewise for all the other `.prototype`s.
**Примечание:** По соглашению, принятому в документации, String.prototype.XYZ сокращается до String#XYZ, и аналогично для всех остальных `.prototype`.

* `String#indexOf(..)`: find the position in the string of another substring найти позицию в строке другой подстроки
* `String#charAt(..)`: access the character at a position in the string получить доступ к символу в позиции в строке
* `String#substr(..)`, `String#substring(..)`, and `String#slice(..)`: extract a portion of the string as a new string извлечь часть строки как новую строку
* `String#toUpperCase()` and `String#toLowerCase()`: create a new string that's converted to either uppercase or lowercase создать новую строку, которая преобразуется в верхний или нижний регистр
* `String#trim()`: create a new string that's stripped of any trailing or leading whitespace создать новую строку, лишенную всех конечных или начальных пробелов

None of the methods modify the string *in place*. Modifications (like case conversion or trimming) create a new value from the existing value.
Ни один из методов не изменяет строку *на месте*. Модификации (такие как преобразование регистра или обрезка) создают новое значение из существующего значения.

By virtue of prototype delegation (see the *this & Object Prototypes* title in this series), any string value can access these methods:
Благодаря делегированию прототипам (см. заголовок *this & Object Prototypes* в этой серии) любое строковое значение может получить доступ к этим методам:

```js
var a = " abc ";

a.indexOf( "c" ); // 3
a.toUpperCase(); // " ABC "
a.trim(); // "abc"
```

The other constructor prototypes contain behaviors appropriate to their types, such as `Number#toFixed(..)` (stringifying a number with a fixed number of decimal digits) and `Array#concat(..)` (merging arrays). All functions have access to `apply(..)`, `call(..)`, and `bind(..)` because `Function.prototype` defines them.
Другие прототипы конструкторов содержат поведение, соответствующее их типам, например `Number#toFixed(..)` (строка числа с фиксированным количеством десятичных цифр) и `Array#concat(..)` (объединение массивов). Все функции имеют доступ к `apply(..)`, `call(..)` и `bind(..)`, поскольку они определены в `Function.prototype`.

But, some of the native prototypes aren't *just* plain objects:
Но некоторые нативные прототипы не являются *просто* простыми объектами:

```js
typeof Function.prototype;			// "function"
Function.prototype();				// Пустая функция!

RegExp.prototype.toString();		// "/(?:)/" -- пустая регулярка
"abc".match( RegExp.prototype );	// [""]
```

A particularly bad idea, you can even modify these native prototypes (not just adding properties as you're probably familiar with):
Особенно плохая идея, вы даже можете изменить эти собственные прототипы (а не просто добавить свойства, с которыми вы, вероятно, знакомы):

```js
Array.isArray( Array.prototype );	// true
Array.prototype.push( 1, 2, 3 );	// 3
Array.prototype;					// [1,2,3]

// не оставляйте так, иначе ждите странностей!
// сбрасываем `Array.prototype` на пустой
Array.prototype.length = 0;
```

As you can see, `Function.prototype` is a function, `RegExp.prototype` is a regular expression, and `Array.prototype` is an array. Interesting and cool, huh?
Как видите, Function.prototype — это функция, RegExp.prototype — регулярное выражение, а Array.prototype — массив. Интересно и круто, да?

#### Prototypes As Defaults
#### Прототипы по умолчанию

`Function.prototype` being an empty function, `RegExp.prototype` being an "empty" (e.g., non-matching) regex, and `Array.prototype` being an empty array, make them all nice "default" values to assign to variables if those variables wouldn't already have had a value of the proper type.
`Function.prototype` – пустая функция, `RegExp.prototype` – "пустое" (например, несоответствующее) регулярное выражение, а `Array.prototype` – пустой массив, сделайте для них все приятные значения "по умолчанию" для присвоения к переменным, если эти переменные еще не имели значения надлежащего типа.

For example:
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

One minor side-benefit of this approach is that the `.prototype`s are already created and built-in, thus created *only once*. By contrast, using `[]`, `function(){}`, and `/(?:)/` values themselves for those defaults would (likely, depending on engine implementations) be recreating those values (and probably garbage-collecting them later) for *each call* of `isThisCool(..)`. That could be memory/CPU wasteful.
Одним из незначительных побочных преимуществ этого подхода является то, что `.prototype`
уже созданы и встроены, поэтому создаются *только один раз*. Напротив, использование самих значений `[]`, `function(){}` и `/(?:)/` для этих значений по умолчанию (вероятно, в зависимости от реализации движка) будет воссоздавать эти значения (и, возможно, выполнять сборку мусора). их позже) для *каждого вызова* `isThisCool(..)`. Это может быть расточительно по памяти/ЦП.

Also, be very careful not to use `Array.prototype` as a default value **that will subsequently be modified**. In this example, `vals` is used read-only, but if you were to instead make in-place changes to `vals`, you would actually be modifying `Array.prototype` itself, which would lead to the gotchas mentioned earlier!
Кроме того, будьте очень осторожны, чтобы не использовать `Array.prototype` в качестве значения по умолчанию, **которое впоследствии будет изменено**. В этом примере vals используется только для чтения, но если бы вы вместо этого вносили изменения в vals на месте, вы фактически модифицировали бы сам Array.prototype, что привело бы к подводным камням, упомянутым ранее!

**Note:** While we're pointing out these native prototypes and some usefulness, be cautious of relying on them and even more wary of modifying them in anyway. See Appendix A "Native Prototypes" for more discussion.
**Примечание.** Хотя мы указываем на эти нативные прототипы и на некоторую их полезность, будьте осторожны, полагаясь на них, и еще более осторожны, не изменяя их каким-либо образом. См. Приложение A «Родные прототипы» для более подробного обсуждения.

## Review
## Обзор

JavaScript provides object wrappers around primitive values, known as natives (`String`, `Number`, `Boolean`, etc). These object wrappers give the values access to behaviors appropriate for each object subtype (`String#trim()` and `Array#concat(..)`).
JavaScript предоставляет объектные оболочки вокруг примитивных значений, известных как стандартные объекты (String, Number, Boolean и т. д.). Эти оболочки объектов предоставляют значениям доступ к поведению, подходящему для каждого подтипа объекта (`String#trim()` и `Array#concat(..)`).

If you have a simple scalar primitive value like `"abc"` and you access its `length` property or some `String.prototype` method, JS automatically "boxes" the value (wraps it in its respective object wrapper) so that the property/method accesses can be fulfilled.
Если у вас есть простое скалярное примитивное значение, такое как `"abc"`, и вы обращаетесь к его свойству `length` или какому-то методу `String.prototype`, JS автоматически "упаковывает" значение (оборачивает его в соответствующую оболочку объекта), чтобы доступы к свойствам/методам могли быть выполнены.