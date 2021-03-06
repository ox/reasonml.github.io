---
title: Строка и символ
order: 20
---

### Строка

Строки в Reason ограничиваются **двойными** кавычками (одинарные кавычки зарезервированы для символов).

```reason
let greeting = "Hello world!";
let multilineGreeting = "Hello
 world!";
```

Спецсимволы нужно экранировать:

```reason
let oneSlash = "\\";
```

Для конкатенации строк используется `^`:

```reason
let greetings = "Hello " ^ "world!";
```

#### Quoted строка

Существует специальный синтаксис для строк, который позволяет:

- мультистроки
- отсутствие необходимости в экранировании
- хуки для специальных препроцессоров

```reason
let GreetingAndOneSlash = {|Hello
World
\
Hehe...
|};
```

Это похоже на JS строки в обратных кавычках, но без необходимости экранирования и без интерполяции. Хотя вы
можете легко сделать интерполяцию,
[как это сделал BuckleScript](http://bucklescript.github.io/bucklescript/Manual.html#_unicode_support_with_string_interpolation_since_1_7_0):

```reason
let world = {js|世界|js};
let helloWorld = {js|你好，$world|js};
```

Специальный препроцессор ищет строки с маркером `js` и трансформирует их в другой код.

#### Использование

[Большинствро строковых операций можно найти в стандартной библиотеке](/api/String.html).
Для компиляции в JS, смотрите `JS.String` биндинги в
[BuckleScript API документации](http://bucklescript.github.io/bucklescript/api/Js_string.html).
Так как строки Reason отображаются в строки JavaScript, вы можете смешивать и сопоставлять операции из обеих
стандартных библиотек.

#### Советы и трюки

https://twitter.com/jusrin00/status/875238742621028355

**Теперь у вас есть выразительная система типов**! В нетипизированном языке вы часто излишне используете
строки как:

- уникальный идентификатор: `var BLUE_COLOR = "blue"`
- идентификатор внутри структуры данных: `var BLUE = "blue"; var RED = "red"; var colors = [BLUE, RED]`
- имя поля объекта: `person["age"] = 24`
- перечисление: `if (audio.canPlayType() === 'probably') {...}`
[(ಠ\_ಠ)](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/canPlayType#Return_value)
- другие безумные паттерны, которые покажутся вам ужасными, после использования Reason

Чем больше вы перегружаете строковый тип, тем меньше система типом может вам помочь! Reason дает краткие,
быстрые и поддерживаем типы и структуры данных как альтернативу примерам выше (например, варианты, описанные в
последующей секции).

В случае нативной компиляции, Reason строки компилируются в простое представление, производительность
которого легко анализировать, но иногда требует ручной настройки. Например, наивная конкатенация строк
`"hi " ^ "how " ^ "are " ^ "you?"` выделяет память прод промежуточную строку `"are you?"` и `"how are you?"`.
В этом случае лучше использовать [`String.concat`](/api/String.html). В некотором смысле, немного приятно, что
традиционный анализ времени выполнения, который мы когда то изучали может быть полезен снова.

В случае JavaScript компиляции, Reason строки отображаются в строки JS и наоборот. Потому таких возможностей
оптимизации нет.

#### Дизайн решения

Использование quoted строки позволяет делать интересные DSL. Такие как [регулярные выражения](/api/Str.html):

```reason
let r = Str.regexp {|hello \([A-Za-z]+\)|};
```

в противоположность

```reason
let r = Str.regexp "hello \\([A-Za-z]+\\)";
```

Хотя для JS компиляции вы должны использовать [`[%bs.re]`](http://bucklescript.github.io/bucklescript/Manual.html#_regex_support).

Акцент на простоту Reason/OCaml можно видеть в его прямолинейной реализации нативных строк. Слишком сложная
реализация строки иногда может иметь [обратный эффект](http://mrale.ph/blog/2016/11/23/making-less-dart-faster.html).

### Символ

Reason имеет тип данных в виде строки, содержащей только одну букву:

```reason
let firstLetterOfAlphabet = 'a';
```

**Важно**: Символы не поддерживают Unicode или UTF-8.

#### Советы и трюки

Символ [компилируется в целое число от 0 до 255](https://bucklescript.github.io/bucklescript/js-demo/?gist=7f6d24873a48fe03fa037c7c47848a4b), для дополнительной скорости. Вы можете использовать паттерн-матчинг
(описание далее) на нем:

```reason
let isVowel = switch theChar {
| 'a' | 'e' | 'i' | 'o' | 'u' | 'y' => true
| _ => false
};
```
