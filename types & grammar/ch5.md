# Вы не знаете JS: Типы и грамматика
# Глава 5: Грамматика

Последняя важная тема, которую мы хотим затронуть, - это то, как работает синтаксис языка JavaScript (он же его грамматика). Вы можете думать, что знаете, как писать JS, но в различных частях грамматики языка ужасно много нюансов, которые приводят к путанице и неправильному пониманию, поэтому мы хотим углубиться в эти части и прояснить некоторые вещи.

**Примечание:** Термин "грамматика" может быть немного менее знаком читателям, чем термин "синтаксис". Во многих отношениях это схожие термины, описывающие *правила* того, как работает язык. У них есть небольшие различия, но они в основном не имеют значения для нашего здесь обсуждения. Грамматика для JavaScript - это структурированный способ описания того, как синтаксис (операторы, ключевые слова и т.д.) компонуется в правильно и корректно разработанные программы. Иными словами, обсуждение синтаксиса без грамматики упустило бы многие важные детали. Поэтому, в этой главе наше внимание акцентируется на том, что наиболее точно описывается как "грамматика", хотя разработчики непосредственно оперируют с синтаксисом языка.

## Инструкции и выражения

Разработчики довольно часто предполагают, что термины "инструкция" и "выражение" примерно эквивалентны. Но здесь нам нужно разделить их, потому что в наших JS программах есть некоторые очень важные различия.

Чтобы провести различие, давайте позаимствуем терминологию, с которой вы, возможно, более знакомы: английский язык.

"Предложение" - это одно законченное высказывание из слов, выражающее мысль. Оно состоит из одной или нескольких "фраз", каждая из которых может быть связана знаками препинания или союзами ("и", "или" и т.д.). Сама фраза может состоять из более мелких фраз. Некоторые фразы являются неполными и сами по себе мало чего значат, в то время как другие фразы могут быть самостоятельными. Эти правила в совокупности называются *грамматикой* английского языка.

Так же обстоит дело с грамматикой JavaScript. Инструкции - это предложения, выражения - это фразы, а операторы - это союзы/знаки препинания.

Каждое выражение в JS может быть вычислено вплоть до одного конкретного значения. Например:

```js
var a = 3 * 6;
var b = a;
b;
```

В этом фрагменте `3 * 6` является выражением (вычисляется до значения `18`). `a` во второй строке также является выражением, как и `b` в третьей строке. Оба выражения `a` и `b` вычисляются в соответствии со значениями, хранящимися в этих переменных в данный момент, которые равны `18`.

Более того, каждая из трех строк представляет собой инструкцию, содержащую выражения. `var a = 3 * 6` и `var b = a` называются "операторами объявления", потому что каждый из них объявляет переменную (и необязательно присваивает ей значение). Присваивания `a = 3 * 6` и `b = a` (откидывая `var`) называются выражениями присваивания.

Третья строка содержит только выражение `b`, и это также инструкция сама по себе (хотя и не очень интересная!). Обычно это называется "оператор выражения".

### Значение завершения инструкции

Это весьма малоизвестный факт, что все инструкции имеют значения завершения (даже если это просто `undefined`).

Как бы вы отнеслись к тому, чтобы взглянуть на значение завершения?

Наиболее очевидный путь - ввести инструкцию в консоль разработчика вашего браузера, потому что, когда вы ее выполняете, консоль по умолчанию сообщает значение завершения самой последней инструкции, которую она выполнила.

Давайте рассмотрим `var b = a`. Каково значение завершения этой инструкции?

Выражение присваивания `b = a` возвращает значение, которое было присвоено (`18` см. выше), но сама инструкция `var` возвращает `undefined`. Почему? Потому что так инструкция `var` определена в спецификации. Если вы введете `var a = 42;` в свою консоль, вы увидите сообщение `undefined` вместо `42`.

**Примечание:** Технически всё немного сложнее. В спецификации ES5, раздел 12.2 "Инструкция переменной", алгоритм `VariableDeclaration` действительно *возвращает* значение (`string`, содержащая имя объявленной переменной - странно, да!?), но это значение обычно поглощается (кроме цикла `for..in`) алгоритмом `VariableStatement`, который выдает пустое (известное как `undefined`) значение завершения.

На самом деле, если вы много экспериментировали с кодом в своей консоли (или в JS среде REPL -- read /evaluate /print / loop), вы, вероятно, видели сообщение `undefined` после множества разных инструкций и, возможно, никогда не понимали, что бы это было. Проще говоря, консоль просто сообщает значение завершения инструкции.

Но то, что консоль выводит как значение завершения, - это не то, что мы можем использовать внутри нашей программы. Итак, как мы можем зафиксировать значение завершения?

Это гораздо более сложная задача. Прежде чем мы объясним *как*, давайте рассмотрим *почему* вам хотелось бы это сделать?

Нам нужно рассмотреть другие типы значений завершения инструкции. Например, любой обыденный блок `{ .. }` имеет значение завершения, равное значению завершения его последней инструкции/выражения.

Рассмотрим:

```js
var b;

if (true) {
	b = 4 + 38;
}
```

Если бы вы ввели это в своей REPL-консоли, вы, вероятно, увидели бы сообщение `42`, поскольку `42` - это значение завершения блока `if`, который принял значение завершения своей последней инструкции выражения присваивания `b = 4 + 38`.

Другими словами, значение завершения блока похоже на *неявный return* значения последней инструкции в блоке.

**Примечание:** Это концептуально известно в таких языках, как CoffeeScript, которые имеют неявные `return` значения из `function`, которые совпадают со значением последней инструкции в функции.

Но есть очевидная проблема. Такой код не работает:

```js
var a, b;

a = if (true) {
	b = 4 + 38;
};
```

Мы не можем зафиксировать значение завершения инструкции и присвоить его другой переменной каким-либо простым синтаксическим/грамматическим способом (по крайней мере, пока!).

Итак, что мы можем сделать?

**Предупреждение**: только для демонстрационных целей -- пожалуйста, не делайте нижеприведенное в вашем реальном коде!

Мы можем использовать сильно оклеветанную функцию `eval(..)` (иногда произносится как "evil" - "злой"), чтобы зафиксировать это значение завершения.

```js
var a, b;

a = eval( "if (true) { b = 4 + 38; }" );

a;	// 42
```

Дааааааааа. Это ужасно некрасиво. Но это работает! И это иллюстрирует тот факт, что значения завершения инструкции - это реальная вещь, которая может быть зафиксирована не только в нашей консоли, но и в наших программах.

Есть предложение для ES7 под названием "выражение исполнения" (do-выражение). Вот как это может сработать:

```js
var a, b;

a = do {
	if (true) {
		b = 4 + 38;
	}
};

a;	// 42
```

Выражение `do { .. }` выполняет блок (с одним или несколькими операторами в нем), и значение завершения последней инструкции внутри блока становится значением завершения *`do`-выражения*, которое, как показано, затем может быть присвоено `a`.

Общая идея состоит в том, чтобы иметь возможность обрабатывать инструкции как выражения -- они могут отображаться внутри других инструкций -- без необходимости оборачивать их в функциональные выражения и делать явный `return ..`.

На данный момент значения завершения инструкций - это не больше, чем пустяки. Но они, возможно, будут приобретать все большее значение по мере развития JS, и, надеюсь, выражения `do { .. }` уменьшат соблазн использовать такие вещи, как `eval (..)`.

**Предупреждение:** Повторяю мое предыдущее увещевание: избегайте `eval (..)`. Серьезно. За дополнительными деталями обращайтесь к книге этой же серии *Область Видимости и Замыкания*.

### Побочные эффекты выражений

Большинство выражений не имеют побочных эффектов. Например:

```js
var a = 2;
var b = a + 3;
```

Выражение `a + 3` не имело *собственного* побочного эффекта, как, например, изменение `a`. У него был результат, равный `5`, и этот итог был присвоен `b` в инструкции `b = a + 3`.

Наиболее распространенным примером выражения с (возможными) побочными эффектами является выражение вызова функции:

```js
function foo() {
	a = a + 1;
}

var a = 1;
foo();		// результат: `undefined`, сторонний эффект: изменённое `a`
```

Однако есть и другие побочные эффекты выражений. Например:

```js
var a = 42;
var b = a++;
```

Выражение `a++` выполняет два отдельных действия. *Сначала* оно возвращает текущее значение `a`, которое равно `42` (которое следом присваивается `b`). Но *затем* оно изменяет само значение `a`, увеличивая его на единицу.

```js
var a = 42;
var b = a++;

a;	// 43
b;	// 42
```

Многие разработчики ошибочно полагают, что `b` равно `43` так же, как и `a`. Неразбериха возникает из-за того, что не полностью учитывается, *когда* происходит побочный эффект оператора `++`.

Операторы инкремента `++` и оператор декремента `--` являются унарными операторами (см. Главу 4), которые могут использоваться либо в постфиксной ("после") позиции, либо в префиксной ("до") позиции.

```js
var a = 42;

a++;	// 42
a;		// 43

++a;	// 44
a;		// 44
```

Когда `++` используется в префиксной позиции, как в `++a`, его побочный эффект (увеличение `a` на единицу) происходит *до* возврата значения из выражения, а не *после*, как в случае `a++`.

**Примечание:** Считаете ли вы, что `++a++` - это корректный синтаксис? Если вы попробуете, то получите ошибку `ReferenceError`, но почему? Потому что операторы с побочным эффектом **требуют ссылки на переменную**, на которую нацелены их побочные эффекты. Для `++a++` сначала вычисляется часть `a++` (из-за приоритета оператора - см. ниже), которая возвращает значение `a` _до_ инкремента. Но затем он пытается вычислить `++42`, что (если вы попытаетесь это сделать) даст ту же ошибку `ReferenceError`, поскольку `++` не может иметь побочного эффекта непосредственно для значения `42`.

Иногда ошибочно полагают, что вы можете инкапсулировать побочный эффект *после* `a++`, заключив его в пару скобок `( )`, например:

```js
var a = 42;
var b = (a++);

a;	// 43
b;	// 42
```

К сожалению, `( )` сами по себе не определяют новое обёрнутое выражение, которое вычисляется следом *за побочным эффектом* выражения `a++`, как мы могли бы надеяться. Фактически, даже если бы это произошло, `a++` сначала возвращает `42`, и, если у вас нет другого выражения, которое повторно вычисляет `a` после побочного эффекта `++`, вы не получите `43` из этого выражения, поэтому `b` не будет присвоен `43`.

Однако есть вариант: `,` - инструкция серии операторов запятой. Этот оператор позволяет вам объединить несколько автономных выражений в одну инструкцию:

```js
var a = 42, b;
b = ( a++, a );

a;	// 43
b;	// 43
```

**Примечание:** Здесь требуется `( .. )` вокруг `a++, a`. Причина в приоритете операторов, о котором мы поговорим позже в этой главе.

Выражение `a++, a` означает, что второе выражение инструкции `a` вычисляется следом *за побочным эффектом* первого выражения инструкции `a++`, что означает, что оно возвращает значение `43` для выполнения присваивания `b`.

Другим примером оператора с побочным действием, является `delete`. Как мы показали в Главе 2, `delete` используется для удаления свойства из `object` или ячейки из `array`. Но обычно он просто вызывается как отдельный оператор:

```js
var obj = {
	a: 42
};

obj.a;			// 42
delete obj.a;	// true
obj.a;			// undefined
```

Результирующее значение оператора `delete` равно `true`, если запрошенная операция является допустимой, или `false` в противном случае. Но побочный эффект оператора заключается в том, что он удаляет свойство объекта (или ячейку массива).

**Примечание:** Что мы подразумеваем под допустимым? Несуществующие свойства или свойства, которые существуют и конфигурируемы (см. Главу 3 из книги этой серии *This и Прототипы Объектов*), вернут `true` из оператора `delete`. В противном случае результат будет `false` или ошибка.

Последним примером оператора с побочным эффектом, который может быть одновременно и очевидным, и неочевидным, является оператор присваивания `=`.

Взгляните:

```js
var a;

a = 42;		// 42
a;			// 42
```

Может показаться, что `=` в инструкции `a = 42` не является оператором с побочным действием. Но, если мы посмотрим на результирующее значение инструкции `a = 42`, то увидим - это только что присвоенное значение (`42`), поэтому присвоение в `a` значения по сути является побочным эффектом.

**Совет:** Те же рассуждения о побочных эффектах применимы к составным операторам присваивания, таким как `+=`, `-=`, и т.д. Например, `a = b += 2` сначала обрабатывается как `b += 2` (то есть `b = b + 2`), а результат *этого* `=` затем присваивается `a`.

Такое поведение, при котором выражение присваивания (или инструкция) завершается присвоенным значением, в первую очередь полезно для цепочек присвоений, таких как:

```js
var a, b, c;

a = b = c = 42;
```

Здесь `c = 42` вычисляется как `42` (с побочным эффектом присваивания `42` в `c`), затем `b = 42` вычисляется как `42` (с побочным эффектом присваивания `42` в `b`), и, наконец, вычисляется `a = 42` (с побочным эффектом присваивания `42` в `a`).

**Предупреждение:** Распространенная ошибка, которую разработчики допускают при выполнении цепочки присваиваний выглядит так: `var a = b = 42`. Хотя это смотрится как одно и то же, но это не так. Если эта инструкция выполняется без наличия отдельной инструкции `var b` (где-то в области видимости), которая официально декларирует переменную `b`, то `var a = b = 42` не объявляет переменную `b` напрямую. В зависимости от `strict` режима это приведёт либо к ошибке, либо к случайной глобальной переменной (см. книгу этой серии *Область Видимости и Замыкания*).

Другой пример для рассмотрения:

```js
function vowels(str) {
	var matches;

	if (str) {
		// выбрать все гласные
		matches = str.match( /[aeiou]/g );

		if (matches) {
			return matches;
		}
	}
}

vowels( "Hello World" ); // ["e","o","o"]
```

Это работает, и многие разработчики предпочитают именно такой вариант. Но мы можем упростить функцию, объединив два оператора `if` в один, если применим идиому, в которой побочный эффект присваивания будет работать на пользу:

```js
function vowels(str) {
	var matches;

	// выбрать все гласные
	if (str && (matches = str.match( /[aeiou]/g ))) {
		return matches;
	}
}

vowels( "Hello World" ); // ["e","o","o"]
```

**Примечание:** `( .. )` вокруг `matches = str.match..` обязательны. Причина - приоритет операторов, который мы рассмотрим позже в разделе "Приоритет операторов" этой главы.

Я предпочитаю этот более короткий стиль, так как он, по-моему, проясняет, что два условных выражения на самом деле связаны, а не разделены. Но, как и в случае с большинством стилистических вариантов в JS, это лишь мнение, какой вариант *лучше*.

### Контекстуальные правила

В правилах грамматики JavaScript есть довольно много мест, где один и тот же синтаксис означает разные вещи в зависимости от того, где / как он используется. Такого рода вещи сами по себе могут вызвать немалую путаницу.

Здесь мы не перечислим исчерпывающе все подобные случаи, а просто назовем несколько самых распространенных.

#### `{ .. }` Фигурные скобки

Есть два основных случая (их будет больше по мере развития JS!), когда в вашем коде появляется пара фигурных скобок `{ .. }`. Давайте взглянем на каждый из них.

##### Объектные литералы

Во-первых, как литерал `object`:

```js
// предположим, что определена функция `bar()`

var a = {
	foo: bar()
};
```

Откуда мы знаем, что это литерал `object`? Потому что пара `{ .. }` - это значение, которое присваивается `a`.

**Примечание:** Эта ссылка `a` называется "L-значение" (значение левой стороны), поскольку она является целью присваивания. Пара `{ .. }` является "R-значением" (значение правой стороны), поскольку она используется *просто* как значение (в данном случае как источник присваивания).

##### Метки

Что произойдет, если мы удалим `var a =` из приведенного выше фрагмента?

```js
// предположим, что определена функция `bar()`

{
	foo: bar()
}
```

Многие разработчики предполагают, что пара `{ .. }` - это просто отдельный литерал `object`, который ничему не присваивается. Но на самом деле все совсем по-другому.

Здесь `{ .. }` - это просто обычный блок кода. В JavaScript не очень идиоматично (тем более в других языках!) иметь такой автономный блок `{ .. }`, но это вполне допустимая грамматика JS. Это может быть особенно полезно в сочетании с объявлениями переменных через `let` с областью видимости блока (см. книгу этой серии *Область Видимости и Замыкания*).

Блок кода `{ .. }` здесь в значительной степени функционально идентичен блоку кода, сопутствующему некой инструкции, такой как циклы `for`/`while`, условного ветвления `if` и т.д.

Но, если это обычный блок кода, тогда, что это за странный синтаксис `foo: bar()` и насколько это корректно?

Этот обескураживающе малоизвестный инструмент в JavaScript называется "инструкцией с меткой". Здесь `foo` - это метка для инструкции `bar()` (у которой опущена завершающая `;` - см. далее в этой главе "Автоматические точки с запятой"). Но в чем смысл инструкции с меткой?

Если бы в JavaScript был оператор `goto`, вы теоретически могли бы написать `goto foo` и заставить перейти сюда, продолжить выполнять код с этого места. Обычно `goto` считаются ужасными идиомами программирования, поскольку они значительно затрудняют понимание кода (он же "спагетти-код"), поэтому *очень хорошо*, что в JavaScript нет привычного `goto`.

Однако JS *действительно* поддерживает ограниченную, специальную форму `goto`: переходы по метке. Обе инструкции `continue` и `break` могут опционально принимать указанную метку, и в этом случае поток программы "прыгает" подобно `goto`. Рассмотрим:

```js
// `foo` labeled-loop
foo: for (var i=0; i<4; i++) {
	for (var j=0; j<4; j++) {
		// всякий раз, когда встречается, продолжить внешний цикл
		if (j == i) {
			// перейти к следующей итерации
			// цикла, помеченного `foo`
			continue foo;
		}

		// пропускать нечетные числа
		if ((j * i) % 2 == 1) {
			// обычный (безметочный) `continue` внутреннего цикла
			continue;
		}

		console.log( i, j );
	}
}
// 1 0
// 2 0
// 2 1
// 3 0
// 3 2
```

**Примечание:** `continue foo` не означает "перейти к позиции с меткой 'foo', чтобы продолжить", а "продолжить цикл с меткой "foo" со следующей итерации". Итак, это *на самом деле* не произвольное `goto`.

Как вы видите, мы пропустили итерацию с нечетным произведением `3 1`, а переход с меткой также пропустил итерации `1 1` и `2 2`.

Возможно, немного более полезная форма перехода с меткой - это `break __` из внутреннего цикла, чтобы выбраться из внешнего цикла. Эту логику довольно громоздко реализовывать с помощью `break` без метки:

```js
// `foo` labeled-loop
foo: for (var i=0; i<4; i++) {
	for (var j=0; j<4; j++) {
		if ((i * j) >= 3) {
			console.log( "останавливаемся!", i, j );
			// прервать цикл помеченный меткой `foo`
			break foo;
		}

		console.log( i, j );
	}
}
// 0 0
// 0 1
// 0 2
// 0 3
// 1 0
// 1 1
// 1 2
// останавливаемся! 1 3
```

**Примечание:** `break foo` не означает "перейти к позиции с меткой 'foo', чтобы продолжить", а "выйти из цикла/блока с надписью 'foo' и продолжить *после* него". Не совсем `goto` в традиционном понимании, да?

Безметочная альтернатива `break` вышеописанному, вероятно, потребовала бы задействовать одну или несколько функций, доступ к переменной из общей области видимости и т.д. Скорее всего, это было бы более запутанным, чем `break` с меткой, поэтому использование `break` с меткой здесь, вероятно, является лучшим вариантом.

Метка может применяться к блоку без цикла, но только `break` может сослаться на такую метку. Вы можете сделать `break ___` из любого блока с меткой, но вы не можете использовать `continue ___` или использовать `break` без метки из блока.

```js
function foo() {
	// блок с меткой `bar`
	bar: {
		console.log( "Привет" );
		break bar;
		console.log( "никогда не выводится" );
	}
	console.log( "Мир" );
}

foo();
// Hello
// World
```

Циклы/блоки с метками крайне редки, и к ним часто относятся неодобрительно. Если это возможно, то лучше всего избегать их; например, используя вызовы функций вместо переходов из цикла. Но, возможно, есть редкие случаи, когда они могут быть полезны. Если вы собираетесь использовать переход по метке, обязательно задокументируйте то, что вы делаете, с подробными объяснениями!

Очень распространено мнение, что JSON является корректным подмножеством JS, поэтому строка JSON (например, `{"a":42}` - обратите внимание на кавычки вокруг имени свойства, как того требует JSON!) считается допустимым кодом JavaScript. **Неправда!** Попробуйте ввести `{"a":42}` в вашу консоль JS, и вы получите сообщение об ошибке.

Инструкция с меткой не может быть заключена в кавычки, поэтому `"a"` не является допустимой меткой, и, следовательно, `:` не может идти сразу после нее.

Итак, JSON действительно является подмножеством синтаксиса JS, но JSON сам по себе не является допустимой грамматикой JS.

Одно из чрезвычайно распространенных заблуждений из этой области заключается в том, что, если бы вы загрузили файл JS через тег `<script src = ..>`, в котором содержится только содержимое JSON (например, результат вызова API), данные были бы прочитаны как действительный JavaScript, но просто были бы недоступны для программы. Обычно заявляют, что JSON-P (практика оборачивания JSON данных в вызов функции, например, `foo({"a":42})`) решает проблему их недоступности, отправляя значение в одну из функций вашей программы.

**Неправда!** Полностью допустимое значение JSON `{"a":42}` само по себе выдало бы ошибку JS, поскольку оно было бы интерпретировано как блок инструкций с недопустимой меткой. Но `foo({"a":42})` является допустимым JS, потому что в нем `{"a":42}` является литеральным значением `object`, передаваемым в `foo (..)`. Итак, правильно сказать, **JSON-P превращает JSON в допустимую JS грамматику**!

##### Блоки

Другая часто цитируемая JS нелепица (связанная с приведением - см. Главу 4) - это:

```js
[] + {}; // "[object Object]"
{} + []; // 0
```

Казалось бы, это говорит, что оператор `+` дает разные результаты в зависимости от того, является ли первый операнд `[]` или `{}`. Но на самом деле это не имеет к оператору никакого отношения!

В первой строке `{}` появляется в выражении оператора `+` и поэтому интерпретируется как фактическое значение (пустой `object`). В главе 4 объясняется, что `[]` преобразуется в `""` и, следовательно, `{}` также преобразуется в значение `string`: `"[object Object]"`.

Но во второй строке `{}` интерпретируется как отдельный пустой блок `{}` (который ничего не делает). Блокам не нужны точки с запятой, так что отсутствие точки с запятой здесь не является проблемой. Наконец, `+ []` - это выражение, которое *явно приводит* `[]` в `number` (см. Главу 4), которое равно `0`.

##### Деструктуризация объекта

Начиная с ES6, есть еще одна ситуация, когда вы увидите пару `{ .. }`, - это "деструктурирующее присваивание" (за дополнительной информацией обратитесь к книге этой серии *ES6 и не только*), в частности, к деструктурированию `object`. Рассмотрим:

```js
function getData() {
	// ..
	return {
		a: 42,
		b: "foo"
	};
}

var { a, b } = getData();

console.log( a, b ); // 42 "foo"
```

Как вы, наверное, можете сказать, `var { a , b } = ..` является формой деструктурирующего присваивания в ES6, что приблизительно эквивалентно:

```js
var res = getData();
var a = res.a;
var b = res.b;
```

**Примечание:** в ES6 `{ a, b }` на самом деле является сокращенной формой деструктурирования `{ a: a, b: b }`, поэтому оба будут работать, но предполагается, что более краткая `{ a, b }` будет предпочтительным вариантом.

Деструктурирование объекта с помощью пары `{ .. }` также может использоваться для именованных аргументов функции, что является сахаром для такого же рода неявного присваивания свойств объекта:

```js
function foo({ a, b, c }) {
	// нет необходимости в:
	// var a = obj.a, b = obj.b, c = obj.c
	console.log( a, b, c );
}

foo( {
	c: [1,2,3],
	a: 42,
	b: "foo"
} );	// 42 "foo" [1, 2, 3]
```

Итак, контекст, в котором мы используем пару `{ .. }`, полностью определяет, что она означает. Это иллюстрирует разницу между синтаксисом и грамматикой. Очень важно понимать эти нюансы, чтобы избежать неожиданных интерпретаций JS движком.

#### `else if` и необязательные блоки

Это распространенное заблуждение, что в JavaScript есть условие `else if`, потому что вы можете сделать так:

```js
if (a) {
	// ..
}
else if (b) {
	// ..
}
else {
	// ..
}
```

Но здесь есть скрытая особенность грамматики JS: здесь нет `else if`. Но инструкциям `if` и `else` разрешается опускать `{ }` вокруг прикрепленного к ним блока, если они содержат только одну инструкцию. Несомненно, ранее вы видели это много раз:

```js
if (a) doSomething( a );
```

Многие руководства по JS стилю будут настаивать на том, чтобы вы всегда использовали `{ }` вокруг блока с единственной инструкцией, например:

```js
if (a) { doSomething( a ); }
```

Однако точно такое же грамматическое правило применяется к условию `else`, поэтому форма `else if`, которую вы, вероятно, всегда писали, *на самом деле* интерпретируется так:

```js
if (a) {
	// ..
}
else {
	if (b) {
		// ..
	}
	else {
		// ..
	}
}
```

`if (b) { .. } else { .. }` - это единственная инструкция, которая следует за `else`, поэтому вы можете либо вставить обрамляющие `{ }`, либо нет. Другими словами, когда вы используете `else if`, вы технически нарушаете общее правило из руководства по стилю и просто определяете свое `else` с помощью одного оператора `if`.

Конечно, идиома `else if` чрезвычайно распространена и приводит к уменьшению отступа на один уровень, поэтому она так привлекательна. Какой бы способ вы ни выбрали, просто пишите явно в своем собственном руководстве по стилю/правилах и не предполагайте, что такие вещи, как `else if`, являются прямыми грамматическими правилами.

## Operator Precedence

As we covered in Chapter 4, JavaScript's version of `&&` and `||` are interesting in that they select and return one of their operands, rather than just resulting in `true` or `false`. That's easy to reason about if there are only two operands and one operator.

```js
var a = 42;
var b = "foo";

a && b;	// "foo"
a || b;	// 42
```

But what about when there's two operators involved, and three operands?

```js
var a = 42;
var b = "foo";
var c = [1,2,3];

a && b || c; // ???
a || b && c; // ???
```

To understand what those expressions result in, we're going to need to understand what rules govern how the operators are processed when there's more than one present in an expression.

These rules are called "operator precedence."

I bet most readers feel they have a decent grasp on operator precedence. But as with everything else we've covered in this book series, we're going to poke and prod at that understanding to see just how solid it really is, and hopefully learn a few new things along the way.

Recall the example from above:

```js
var a = 42, b;
b = ( a++, a );

a;	// 43
b;	// 43
```

But what would happen if we remove the `( )`?

```js
var a = 42, b;
b = a++, a;

a;	// 43
b;	// 42
```

Wait! Why did that change the value assigned to `b`?

Because the `,` operator has a lower precedence than the `=` operator. So, `b = a++, a` is interpreted as `(b = a++), a`. Because (as we explained earlier) `a++` has *after side effects*, the assigned value to `b` is the value `42` before the `++` changes `a`.

This is just a simple matter of needing to understand operator precedence. If you're going to use `,` as a statement-series operator, it's important to know that it actually has the lowest precedence. Every other operator will more tightly bind than `,` will.

Now, recall this example from above:

```js
if (str && (matches = str.match( /[aeiou]/g ))) {
	// ..
}
```

We said the `( )` around the assignment is required, but why? Because `&&` has higher precedence than `=`, so without the `( )` to force the binding, the expression would instead be treated as `(str && matches) = str.match..`. But this would be an error, because the result of `(str && matches)` isn't going to be a variable, but instead a value (in this case `undefined`), and so it can't be the left-hand side of an `=` assignment!

OK, so you probably think you've got this operator precedence thing down.

Let's move on to a more complex example (which we'll carry throughout the next several sections of this chapter) to *really* test your understanding:

```js
var a = 42;
var b = "foo";
var c = false;

var d = a && b || c ? c || b ? a : c && b : a;

d;		// ??
```

OK, evil, I admit it. No one would write a string of expressions like that, right? *Probably* not, but we're going to use it to examine various issues around chaining multiple operators together, which *is* a very common task.

The result above is `42`. But that's not nearly as interesting as how we can figure out that answer without just plugging it into a JS program to let JavaScript sort it out.

Let's dig in.

The first question -- it may not have even occurred to you to ask -- is, does the first part (`a && b || c`) behave like `(a && b) || c` or like `a && (b || c)`? Do you know for certain? Can you even convince yourself they are actually different?

```js
(false && true) || true;	// true
false && (true || true);	// false
```

So, there's proof they're different. But still, how does `false && true || true` behave? The answer:

```js
false && true || true;		// true
(false && true) || true;	// true
```

So we have our answer. The `&&` operator is evaluated first and the `||` operator is evaluated second.

But is that just because of left-to-right processing? Let's reverse the order of operators:

```js
true || false && false;		// true

(true || false) && false;	// false -- nope
true || (false && false);	// true -- winner, winner!
```

Now we've proved that `&&` is evaluated first and then `||`, and in this case that was actually counter to generally expected left-to-right processing.

So what caused the behavior? **Operator precedence**.

Every language defines its own operator precedence list. It's dismaying, though, just how uncommon it is that JS developers have read JS's list.

If you knew it well, the above examples wouldn't have tripped you up in the slightest, because you'd already know that `&&` is more precedent than `||`. But I bet a fair amount of readers had to think about it a little bit.

**Note:** Unfortunately, the JS spec doesn't really have its operator precedence list in a convenient, single location. You have to parse through and understand all the grammar rules. So we'll try to lay out the more common and useful bits here in a more convenient format. For a complete list of operator precedence, see "Operator Precedence" on the MDN site (* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence).

### Short Circuited

In Chapter 4, we mentioned in a side note the "short circuiting" nature of operators like `&&` and `||`. Let's revisit that in more detail now.

For both `&&` and `||` operators, the right-hand operand will **not be evaluated** if the left-hand operand is sufficient to determine the outcome of the operation. Hence, the name "short circuited" (in that if possible, it will take an early shortcut out).

For example, with `a && b`, `b` is not evaluated if `a` is falsy, because the result of the `&&` operand is already certain, so there's no point in bothering to check `b`. Likewise, with `a || b`, if `a` is truthy, the result of the operand is already certain, so there's no reason to check `b`.

This short circuiting can be very helpful and is commonly used:

```js
function doSomething(opts) {
	if (opts && opts.cool) {
		// ..
	}
}
```

The `opts` part of the `opts && opts.cool` test acts as sort of a guard, because if `opts` is unset (or is not an `object`), the expression `opts.cool` would throw an error. The `opts` test failing plus the short circuiting means that `opts.cool` won't even be evaluated, thus no error!

Similarly, you can use `||` short circuiting:

```js
function doSomething(opts) {
	if (opts.cache || primeCache()) {
		// ..
	}
}
```

Here, we're checking for `opts.cache` first, and if it's present, we don't call the `primeCache()` function, thus avoiding potentially unnecessary work.

### Tighter Binding

But let's turn our attention back to that earlier complex statement example with all the chained operators, specifically the `? :` ternary operator parts. Does the `? :` operator have more or less precedence than the `&&` and `||` operators?

```js
a && b || c ? c || b ? a : c && b : a
```

Is that more like this:

```js
a && b || (c ? c || (b ? a : c) && b : a)
```

or this?

```js
(a && b || c) ? (c || b) ? a : (c && b) : a
```

The answer is the second one. But why?

Because `&&` is more precedent than `||`, and `||` is more precedent than `? :`.

So, the expression `(a && b || c)` is evaluated *first* before the `? :` it participates in. Another way this is commonly explained is that `&&` and `||` "bind more tightly" than `? :`. If the reverse was true, then `c ? c...` would bind more tightly, and it would behave (as the first choice) like `a && b || (c ? c..)`.

### Associativity

So, the `&&` and `||` operators bind first, then the `? :` operator. But what about multiple operators of the same precedence? Do they always process left-to-right or right-to-left?

In general, operators are either left-associative or right-associative, referring to whether **grouping happens from the left or from the right**.

It's important to note that associativity is *not* the same thing as left-to-right or right-to-left processing.

But why does it matter whether processing is left-to-right or right-to-left? Because expressions can have side effects, like for instance with function calls:

```js
var a = foo() && bar();
```

Here, `foo()` is evaluated first, and then possibly `bar()` depending on the result of the `foo()` expression. That definitely could result in different program behavior than if `bar()` was called before `foo()`.

But this behavior is *just* left-to-right processing (the default behavior in JavaScript!) -- it has nothing to do with the associativity of `&&`. In that example, since there's only one `&&` and thus no relevant grouping here, associativity doesn't even come into play.

But with an expression like `a && b && c`, grouping *will* happen implicitly, meaning that either `a && b` or `b && c` will be evaluated first.

Technically, `a && b && c` will be handled as `(a && b) && c`, because `&&` is left-associative (so is `||`, by the way). However, the right-associative alternative `a && (b && c)` behaves observably the same way. For the same values, the same expressions are evaluated in the same order.

**Note:** If hypothetically `&&` was right-associative, it would be processed the same as if you manually used `( )` to create grouping like `a && (b && c)`. But that still **doesn't mean** that `c` would be processed before `b`. Right-associativity does **not** mean right-to-left evaluation, it means right-to-left **grouping**. Either way, regardless of the grouping/associativity, the strict ordering of evaluation will be `a`, then `b`, then `c` (aka left-to-right).

So it doesn't really matter that much that `&&` and `||` are left-associative, other than to be accurate in how we discuss their definitions.

But that's not always the case. Some operators would behave very differently depending on left-associativity vs. right-associativity.

Consider the `? :` ("ternary" or "conditional") operator:

```js
a ? b : c ? d : e;
```

`? :` is right-associative, so which grouping represents how it will be processed?

* `a ? b : (c ? d : e)`
* `(a ? b : c) ? d : e`

The answer is `a ? b : (c ? d : e)`. Unlike with `&&` and `||` above, the right-associativity here actually matters, as `(a ? b : c) ? d : e` *will* behave differently for some (but not all!) combinations of values.

One such example:

```js
true ? false : true ? true : true;		// false

true ? false : (true ? true : true);	// false
(true ? false : true) ? true : true;	// true
```

Even more nuanced differences lurk with other value combinations, even if the end result is the same. Consider:

```js
true ? false : true ? true : false;		// false

true ? false : (true ? true : false);	// false
(true ? false : true) ? true : false;	// false
```

From that scenario, the same end result implies that the grouping is moot. However:

```js
var a = true, b = false, c = true, d = true, e = false;

a ? b : (c ? d : e); // false, evaluates only `a` and `b`
(a ? b : c) ? d : e; // false, evaluates `a`, `b` AND `e`
```

So, we've clearly proved that `? :` is right-associative, and that it actually matters with respect to how the operator behaves if chained with itself.

Another example of right-associativity (grouping) is the `=` operator. Recall the chained assignment example from earlier in the chapter:

```js
var a, b, c;

a = b = c = 42;
```

We asserted earlier that `a = b = c = 42` is processed by first evaluating the `c = 42` assignment, then `b = ..`, and finally `a = ..`. Why? Because of the right-associativity, which actually treats the statement like this: `a = (b = (c = 42))`.

Remember our running complex assignment expression example from earlier in the chapter?

```js
var a = 42;
var b = "foo";
var c = false;

var d = a && b || c ? c || b ? a : c && b : a;

d;		// 42
```

Armed with our knowledge of precedence and associativity, we should now be able to break down the code into its grouping behavior like this:

```js
((a && b) || c) ? ((c || b) ? a : (c && b)) : a
```

Or, to present it indented if that's easier to understand:

```js
(
  (a && b)
    ||
  c
)
  ?
(
  (c || b)
    ?
  a
    :
  (c && b)
)
  :
a
```

Let's solve it now:

1. `(a && b)` is `"foo"`.
2. `"foo" || c` is `"foo"`.
3. For the first `?` test, `"foo"` is truthy.
4. `(c || b)` is `"foo"`.
5. For the second `?` test, `"foo"` is truthy.
6. `a` is `42`.

That's it, we're done! The answer is `42`, just as we saw earlier. That actually wasn't so hard, was it?

### Disambiguation

You should now have a much better grasp on operator precedence (and associativity) and feel much more comfortable understanding how code with multiple chained operators will behave.

But an important question remains: should we all write code understanding and perfectly relying on all the rules of operator precedence/associativity? Should we only use `( )` manual grouping when it's necessary to force a different processing binding/order?

Or, on the other hand, should we recognize that even though such rules *are in fact* learnable, there's enough gotchas to warrant ignoring automatic precedence/associativity? If so, should we thus always use `( )` manual grouping and remove all reliance on these automatic behaviors?

This debate is highly subjective, and heavily symmetrical to the debate in Chapter 4 over *implicit* coercion. Most developers feel the same way about both debates: either they accept both behaviors and code expecting them, or they discard both behaviors and stick to manual/explicit idioms.

Of course, I cannot answer this question definitively for the reader here anymore than I could in Chapter 4. But I've presented you the pros and cons, and hopefully encouraged enough deeper understanding that you can make informed rather than hype-driven decisions.

In my opinion, there's an important middle ground. We should mix both operator precedence/associativity *and* `( )` manual grouping into our programs -- I argue the same way in Chapter 4 for healthy/safe usage of *implicit* coercion, but certainly don't endorse it exclusively without bounds.

For example, `if (a && b && c) ..` is perfectly OK to me, and I wouldn't do `if ((a && b) && c) ..` just to explicitly call out the associativity, because I think it's overly verbose.

On the other hand, if I needed to chain two `? :` conditional operators together, I'd certainly use `( )` manual grouping to make it absolutely clear what my intended logic is.

Thus, my advice here is similar to that of Chapter 4: **use operator precedence/associativity where it leads to shorter and cleaner code, but use `( )` manual grouping in places where it helps create clarity and reduce confusion.**

## Automatic Semicolons

ASI (Automatic Semicolon Insertion) is when JavaScript assumes a `;` in certain places in your JS program even if you didn't put one there.

Why would it do that? Because if you omit even a single required `;` your program would fail. Not very forgiving. ASI allows JS to be tolerant of certain places where `;` aren't commonly thought  to be necessary.

It's important to note that ASI will only take effect in the presence of a newline (aka line break). Semicolons are not inserted in the middle of a line.

Basically, if the JS parser parses a line where a parser error would occur (a missing expected `;`), and it can reasonably insert one, it does so. What's reasonable for insertion? Only if there's nothing but whitespace and/or comments between the end of some statement and that line's newline/line break.

Consider:

```js
var a = 42, b
c;
```

Should JS treat the `c` on the next line as part of the `var` statement? It certainly would if a `,` had come anywhere (even another line) between `b` and `c`. But since there isn't one, JS assumes instead that there's an implied `;` (at the newline) after `b`. Thus, `c;` is left as a standalone expression statement.

Similarly:

```js
var a = 42, b = "foo";

a
b	// "foo"
```

That's still a valid program without error, because expression statements also accept ASI.

There's certain places where ASI is helpful, like for instance:

```js
var a = 42;

do {
	// ..
} while (a)	// <-- ; expected here!
a;
```

The grammar requires a `;` after a `do..while` loop, but not after `while` or `for` loops. But most developers don't remember that! So, ASI helpfully steps in and inserts one.

As we said earlier in the chapter, statement blocks do not require `;` termination, so ASI isn't necessary:

```js
var a = 42;

while (a) {
	// ..
} // <-- no ; expected here
a;
```

The other major case where ASI kicks in is with the `break`, `continue`, `return`, and (ES6) `yield` keywords:

```js
function foo(a) {
	if (!a) return
	a *= 2;
	// ..
}
```

The `return` statement doesn't carry across the newline to the `a *= 2` expression, as ASI assumes the `;` terminating the `return` statement. Of course, `return` statements *can* easily break across multiple lines, just not when there's nothing after `return` but the newline/line break.

```js
function foo(a) {
	return (
		a * 2 + 3 / 12
	);
}
```

Identical reasoning applies to `break`, `continue`, and `yield`.

### Error Correction

One of the most hotly contested *religious wars* in the JS community (besides tabs vs. spaces) is whether to rely heavily/exclusively on ASI or not.

Most, but not all, semicolons are optional, but the two `;`s in the `for ( .. ) ..` loop header are required.

On the pro side of this debate, many developers believe that ASI is a useful mechanism that allows them to write more terse (and more "beautiful") code by omitting all but the strictly required `;`s (which are very few). It is often asserted that ASI makes many `;`s optional, so a correctly written program *without them* is no different than a correctly written program *with them*.

On the con side of the debate, many other developers will assert that there are *too many* places that can be accidental gotchas, especially for newer, less experienced developers, where unintended `;`s being magically inserted change the meaning. Similarly, some developers will argue that if they omit a semicolon, it's a flat-out mistake, and they want their tools (linters, etc.) to catch it before the JS engine *corrects* the mistake under the covers.

Let me just share my perspective. A strict reading of the spec implies that ASI is an "error correction" routine. What kind of error, you may ask? Specifically, a **parser error**. In other words, in an attempt to have the parser fail less, ASI lets it be more tolerant.

But tolerant of what? In my view, the only way a **parser error** occurs is if it's given an incorrect/errored program to parse. So, while ASI is strictly correcting parser errors, the only way it can get such errors is if there were first program authoring errors -- omitting semicolons where the grammar rules require them.

So, to put it more bluntly, when I hear someone claim that they want to omit "optional semicolons," my brain translates that claim to "I want to write the most parser-broken program I can that will still work."

I find that to be a ludicrous position to take and the arguments of saving keystrokes and having more "beautiful code" to be weak at best.

Furthermore, I don't agree that this is the same thing as the spaces vs tabs debate -- that it's purely cosmetic -- but rather I believe it's a fundamental question of writing code that adheres to grammar requirements vs. code that relies on grammar exceptions to just barely skate through.

Another way of looking at it is that relying on ASI is essentially considering newlines to be significant "whitespace." Other languages like Python have true significant whitespace. But is it really appropriate to think of JavaScript as having significant newlines as it stands today?

My take: **use semicolons wherever you know they are "required," and limit your assumptions about ASI to a minimum.**

But don't just take my word for it. Back in 2012, creator of JavaScript Brendan Eich said (http://brendaneich.com/2012/04/the-infernal-semicolon/) the following:

> The moral of this story: ASI is (formally speaking) a syntactic error correction procedure. If you start to code as if it were a universal significant-newline rule, you will get into trouble.
> ..
> I wish I had made newlines more significant in JS back in those ten days in May, 1995.
> ..
> Be careful not to use ASI as if it gave JS significant newlines.

## Errors

Not only does JavaScript have different *subtypes* of errors (`TypeError`, `ReferenceError`, `SyntaxError`, etc.), but also the grammar defines certain errors to be enforced at compile time, as compared to all other errors that happen during runtime.

In particular, there have long been a number of specific conditions that should be caught and reported as "early errors" (during compilation). Any straight-up syntax error is an early error (e.g., `a = ,`), but also the grammar defines things that are syntactically valid but disallowed nonetheless.

Since execution of your code has not begun yet, these errors are not catchable with `try..catch`; they will just fail the parsing/compilation of your program.

**Tip:** There's no requirement in the spec about exactly how browsers (and developer tools) should report errors. So you may see variations across browsers in the following error examples, in what specific subtype of error is reported or what the included error message text will be.

One simple example is with syntax inside a regular expression literal. There's nothing wrong with the JS syntax here, but the invalid regex will throw an early error:

```js
var a = /+foo/;		// Error!
```

The target of an assignment must be an identifier (or an ES6 destructuring expression that produces one or more identifiers), so a value like `42` in that position is illegal and can be reported right away:

```js
var a;
42 = a;		// Error!
```

ES5's `strict` mode defines even more early errors. For example, in `strict` mode, function parameter names cannot be duplicated:

```js
function foo(a,b,a) { }					// just fine

function bar(a,b,a) { "use strict"; }	// Error!
```

Another `strict` mode early error is an object literal having more than one property of the same name:

```js
(function(){
	"use strict";

	var a = {
		b: 42,
		b: 43
	};			// Error!
})();
```

**Note:** Semantically speaking, such errors aren't technically *syntax* errors but more *grammar* errors -- the above snippets are syntactically valid. But since there is no `GrammarError` type, some browsers use `SyntaxError` instead.

### Using Variables Too Early

ES6 defines a (frankly confusingly named) new concept called the TDZ ("Temporal Dead Zone").

The TDZ refers to places in code where a variable reference cannot yet be made, because it hasn't reached its required initialization.

The most clear example of this is with ES6 `let` block-scoping:

```js
{
	a = 2;		// ReferenceError!
	let a;
}
```

The assignment `a = 2` is accessing the `a` variable (which is indeed block-scoped to the `{ .. }` block) before it's been initialized by the `let a` declaration, so it's in the TDZ for `a` and throws an error.

Interestingly, while `typeof` has an exception to be safe for undeclared variables (see Chapter 1), no such safety exception is made for TDZ references:

```js
{
	typeof a;	// undefined
	typeof b;	// ReferenceError! (TDZ)
	let b;
}
```

## Function Arguments

Another example of a TDZ violation can be seen with ES6 default parameter values (see the *ES6 & Beyond* title of this series):

```js
var b = 3;

function foo( a = 42, b = a + b + 5 ) {
	// ..
}
```

The `b` reference in the assignment would happen in the TDZ for the parameter `b` (not pull in the outer `b` reference), so it will throw an error. However, the `a` in the assignment is fine since by that time it's past the TDZ for parameter `a`.

When using ES6's default parameter values, the default value is applied to the parameter if you either omit an argument, or you pass an `undefined` value in its place:

```js
function foo( a = 42, b = a + 1 ) {
	console.log( a, b );
}

foo();					// 42 43
foo( undefined );		// 42 43
foo( 5 );				// 5 6
foo( void 0, 7 );		// 42 7
foo( null );			// null 1
```

**Note:** `null` is coerced to a `0` value in the `a + 1` expression. See Chapter 4 for more info.

From the ES6 default parameter values perspective, there's no difference between omitting an argument and passing an `undefined` value. However, there is a way to detect the difference in some cases:

```js
function foo( a = 42, b = a + 1 ) {
	console.log(
		arguments.length, a, b,
		arguments[0], arguments[1]
	);
}

foo();					// 0 42 43 undefined undefined
foo( 10 );				// 1 10 11 10 undefined
foo( 10, undefined );	// 2 10 11 10 undefined
foo( 10, null );		// 2 10 null 10 null
```

Even though the default parameter values are applied to the `a` and `b` parameters, if no arguments were passed in those slots, the `arguments` array will not have entries.

Conversely, if you pass an `undefined` argument explicitly, an entry will exist in the `arguments` array for that argument, but it will be `undefined` and not (necessarily) the same as the default value that was applied to the named parameter for that same slot.

While ES6 default parameter values can create divergence between the `arguments` array slot and the corresponding named parameter variable, this same disjointedness can also occur in tricky ways in ES5:

```js
function foo(a) {
	a = 42;
	console.log( arguments[0] );
}

foo( 2 );	// 42 (linked)
foo();		// undefined (not linked)
```

If you pass an argument, the `arguments` slot and the named parameter are linked to always have the same value. If you omit the argument, no such linkage occurs.

But in `strict` mode, the linkage doesn't exist regardless:

```js
function foo(a) {
	"use strict";
	a = 42;
	console.log( arguments[0] );
}

foo( 2 );	// 2 (not linked)
foo();		// undefined (not linked)
```

It's almost certainly a bad idea to ever rely on any such linkage, and in fact the linkage itself is a leaky abstraction that's exposing an underlying implementation detail of the engine, rather than a properly designed feature.

Use of the `arguments` array has been deprecated (especially in favor of ES6 `...` rest parameters -- see the *ES6 & Beyond* title of this series), but that doesn't mean that it's all bad.

Prior to ES6, `arguments` is the only way to get an array of all passed arguments to pass along to other functions, which turns out to be quite useful. You can also mix named parameters with the `arguments` array and be safe, as long as you follow one simple rule: **never refer to a named parameter *and* its corresponding `arguments` slot at the same time.** If you avoid that bad practice, you'll never expose the leaky linkage behavior.

```js
function foo(a) {
	console.log( a + arguments[1] ); // safe!
}

foo( 10, 32 );	// 42
```

## `try..finally`

You're probably familiar with how the `try..catch` block works. But have you ever stopped to consider the `finally` clause that can be paired with it? In fact, were you aware that `try` only requires either `catch` or `finally`, though both can be present if needed.

The code in the `finally` clause *always* runs (no matter what), and it always runs right after the `try` (and `catch` if present) finish, before any other code runs. In one sense, you can kind of think of the code in a `finally` clause as being in a callback function that will always be called regardless of how the rest of the block behaves.

So what happens if there's a `return` statement inside a `try` clause? It obviously will return a value, right? But does the calling code that receives that value run before or after the `finally`?

```js
function foo() {
	try {
		return 42;
	}
	finally {
		console.log( "Hello" );
	}

	console.log( "never runs" );
}

console.log( foo() );
// Hello
// 42
```

The `return 42` runs right away, which sets up the completion value from the `foo()` call. This action completes the `try` clause and the `finally` clause immediately runs next. Only then is the `foo()` function complete, so that its completion value is returned back for the `console.log(..)` statement to use.

The exact same behavior is true of a `throw` inside `try`:

```js
 function foo() {
	try {
		throw 42;
	}
	finally {
		console.log( "Hello" );
	}

	console.log( "never runs" );
}

console.log( foo() );
// Hello
// Uncaught Exception: 42
```

Now, if an exception is thrown (accidentally or intentionally) inside a `finally` clause, it will override as the primary completion of that function. If a previous `return` in the `try` block had set a completion value for the function, that value will be abandoned.

```js
function foo() {
	try {
		return 42;
	}
	finally {
		throw "Oops!";
	}

	console.log( "never runs" );
}

console.log( foo() );
// Uncaught Exception: Oops!
```

It shouldn't be surprising that other nonlinear control statements like `continue` and `break` exhibit similar behavior to `return` and `throw`:

```js
for (var i=0; i<10; i++) {
	try {
		continue;
	}
	finally {
		console.log( i );
	}
}
// 0 1 2 3 4 5 6 7 8 9
```

The `console.log(i)` statement runs at the end of the loop iteration, which is caused by the `continue` statement. However, it still runs before the `i++` iteration update statement, which is why the values printed are `0..9` instead of `1..10`.

**Note:** ES6 adds a `yield` statement, in generators (see the *Async & Performance* title of this series) which in some ways can be seen as an intermediate `return` statement. However, unlike a `return`, a `yield` isn't complete until the generator is resumed, which means a `try { .. yield .. }` has not completed. So an attached `finally` clause will not run right after the `yield` like it does with `return`.

A `return` inside a `finally` has the special ability to override a previous `return` from the `try` or `catch` clause, but only if `return` is explicitly called:

```js
function foo() {
	try {
		return 42;
	}
	finally {
		// no `return ..` here, so no override
	}
}

function bar() {
	try {
		return 42;
	}
	finally {
		// override previous `return 42`
		return;
	}
}

function baz() {
	try {
		return 42;
	}
	finally {
		// override previous `return 42`
		return "Hello";
	}
}

foo();	// 42
bar();	// undefined
baz();	// "Hello"
```

Normally, the omission of `return` in a function is the same as `return;` or even `return undefined;`, but inside a `finally` block the omission of `return` does not act like an overriding `return undefined`; it just lets the previous `return` stand.

In fact, we can really up the craziness if we combine `finally` with labeled `break` (discussed earlier in the chapter):

```js
function foo() {
	bar: {
		try {
			return 42;
		}
		finally {
			// break out of `bar` labeled block
			break bar;
		}
	}

	console.log( "Crazy" );

	return "Hello";
}

console.log( foo() );
// Crazy
// Hello
```

But... don't do this. Seriously. Using a `finally` + labeled `break` to effectively cancel a `return` is doing your best to create the most confusing code possible. I'd wager no amount of comments will redeem this code.

## `switch`

Let's briefly explore the `switch` statement, a sort-of syntactic shorthand for an `if..else if..else..` statement chain.

```js
switch (a) {
	case 2:
		// do something
		break;
	case 42:
		// do another thing
		break;
	default:
		// fallback to here
}
```

As you can see, it evaluates `a` once, then matches the resulting value to each `case` expression (just simple value expressions here). If a match is found, execution will begin in that matched `case`, and will either go until a `break` is encountered or until the end of the `switch` block is found.

That much may not surprise you, but there are several quirks about `switch` you may not have noticed before.

First, the matching that occurs between the `a` expression and each `case` expression is identical to the `===` algorithm (see Chapter 4). Often times `switch`es are used with absolute values in `case` statements, as shown above, so strict matching is appropriate.

However, you may wish to allow coercive equality (aka `==`, see Chapter 4), and to do so you'll need to sort of "hack" the `switch` statement a bit:

```js
var a = "42";

switch (true) {
	case a == 10:
		console.log( "10 or '10'" );
		break;
	case a == 42:
		console.log( "42 or '42'" );
		break;
	default:
		// never gets here
}
// 42 or '42'
```

This works because the `case` clause can have any expression (not just simple values), which means it will strictly match that expression's result to the test expression (`true`). Since `a == 42` results in `true` here, the match is made.

Despite `==`, the `switch` matching itself is still strict, between `true` and `true` here. If the `case` expression resulted in something that was truthy but not strictly `true` (see Chapter 4), it wouldn't work. This can bite you if you're for instance using a "logical operator" like `||` or `&&` in your expression:

```js
var a = "hello world";
var b = 10;

switch (true) {
	case (a || b == 10):
		// never gets here
		break;
	default:
		console.log( "Oops" );
}
// Oops
```

Since the result of `(a || b == 10)` is `"hello world"` and not `true`, the strict match fails. In this case, the fix is to force the expression explicitly to be a `true` or `false`, such as `case !!(a || b == 10):` (see Chapter 4).

Lastly, the `default` clause is optional, and it doesn't necessarily have to come at the end (although that's the strong convention). Even in the `default` clause, the same rules apply about encountering a `break` or not:

```js
var a = 10;

switch (a) {
	case 1:
	case 2:
		// never gets here
	default:
		console.log( "default" );
	case 3:
		console.log( "3" );
		break;
	case 4:
		console.log( "4" );
}
// default
// 3
```

**Note:** As discussed previously about labeled `break`s, the `break` inside a `case` clause can also be labeled.

The way this snippet processes is that it passes through all the `case` clause matching first, finds no match, then goes back up to the `default` clause and starts executing. Since there's no `break` there, it continues executing in the already skipped over `case 3` block, before stopping once it hits that `break`.

While this sort of round-about logic is clearly possible in JavaScript, there's almost no chance that it's going to make for reasonable or understandable code. Be very skeptical if you find yourself wanting to create such circular logic flow, and if you really do, make sure you include plenty of code comments to explain what you're up to!

## Review

JavaScript grammar has plenty of nuance that we as developers should spend a little more time paying closer attention to than we typically do. A little bit of effort goes a long way to solidifying your deeper knowledge of the language.

Statements and expressions have analogs in English language -- statements are like sentences and expressions are like phrases. Expressions can be pure/self-contained, or they can have side effects.

The JavaScript grammar layers semantic usage rules (aka context) on top of the pure syntax. For example, `{ }` pairs used in various places in your program can mean statement blocks, `object` literals, (ES6) destructuring assignments, or (ES6) named function arguments.

JavaScript operators all have well-defined rules for precedence (which ones bind first before others) and associativity (how multiple operator expressions are implicitly grouped). Once you learn these rules, it's up to you to decide if precedence/associativity are *too implicit* for their own good, or if they will aid in writing shorter, clearer code.

ASI (Automatic Semicolon Insertion) is a parser-error-correction mechanism built into the JS engine, which allows it under certain circumstances to insert an assumed `;` in places where it is required, was omitted, *and* where insertion fixes the parser error. The debate rages over whether this behavior implies that most `;` are optional (and can/should be omitted for cleaner code) or whether it means that omitting them is making mistakes that the JS engine merely cleans up for you.

JavaScript has several types of errors, but it's less known that it has two classifications for errors: "early" (compiler thrown, uncatchable) and "runtime" (`try..catch`able). All syntax errors are obviously early errors that stop the program before it runs, but there are others, too.

Function arguments have an interesting relationship to their formal declared named parameters. Specifically, the `arguments` array has a number of gotchas of leaky abstraction behavior if you're not careful. Avoid `arguments` if you can, but if you must use it, by all means avoid using the positional slot in `arguments` at the same time as using a named parameter for that same argument.

The `finally` clause attached to a `try` (or `try..catch`) offers some very interesting quirks in terms of execution processing order. Some of these quirks can be helpful, but it's possible to create lots of confusion, especially if combined with labeled blocks. As always, use `finally` to make code better and clearer, not more clever or confusing.

The `switch` offers some nice shorthand for `if..else if..` statements, but beware of many common simplifying assumptions about its behavior. There are several quirks that can trip you up if you're not careful, but there's also some neat hidden tricks that `switch` has up its sleeve!
