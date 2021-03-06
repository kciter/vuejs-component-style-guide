<p align="center">
  <img src="img/logo.png"/>
</p>

### Переводы
 * [бразильский португальский](README-PTBR.md)
 * [английский](README.md)
 * [китайский язык](README-CN.md)

Этот документ предалагает для вас ряд правил по разработке компонетов Vue которые:

 * потом будет легче для вашей команды (или для вас в будущем) понять или найти что и как работает
 * ваш редактор кода (IDE, среда разработки) поймет с меньшим количеством ошибок и предложит лучшие подсказки
 * улучшит переиспользование в данном проекте и в других ваших проектах
 * лучше кешируются и выделяются в отдельные компоненты

##  Содержание

* Модульная разработка
* Наименование копонентов vue
* Выражения в компонентах должны быть простыми
* Оставляйте свойства простыми
* Правильно используйте свойства компонента
* Определяйте `this` как `component`
* Структура компонента
* Именование событий
* Избегайте `this.$parent`
* Используйте `this.$refs` осторожно
* Используйте ограниченные стили
* Документируйте API компонента
* Добавляйте демо
* Форматируйте код файлов


## Модульная разработка

 Всегда старайтесь чтобы ваше приложение состояло из небольших модулей, каждый из которых умеет выполнять только одну функцию, но делает это хорошо.

 Модуль по определению это небольшая ограниченная часть приложения. "Строительный блок", самодостаночный функционально. Организация Vue позволяет создавать подобные модули оринетируясь на визуальные компоненты.

### Почему?

Модули небольших размеров легче взять для использования - понять что они делают, дорабатывать или переиспользовать. И вам и всей вашей команде.

### Как?

Старайтесь чтобы каждый Vue компонент соответствовал принципам [FIRST](https://addyosmani.com/first/):
 - Решающий одну задачу,
 - Назависимый,
 - Переиспользуемый,
 - Небольшой,
 - Простой в тестировании.

 [↑ наверх](#Содержание)



## Наименование компонентов vue

Имя каждого компонента должно соответствовть следующим критериям:

* **Понятное**: в меру детальным, в меру абстрактным
* **Короткое**: не более 2-3 слов
* **Произносимое**: чтобы его можно было упомнянуть в обсуждении

### Почему?

* Имя компонента используется людьми и должно облегчать коммуникацию

### Как?

```html
<!-- правильно -->
<app-header></app-header>
<user-list></user-list>
<range-slider></range-slider>

<!-- неправильно -->
<btn-group></btn-group> <!-- короткое, но произносить - язык сломаешь -->
<ui-slider></ui-slider> <!-- все коменпоненты - так или иначе UI элементы, приставка не нужна -->
<slider></slider> <!--не соответствует спецификации HTML5 -->
```

[↑ наверх](#Содержание)



## Выражения в компонентах должны быть простыми

Инлайновые выражение которые вы можете использовать в шаблонах Vue - это самые обычные Javascript выражения. Это дает максимальную свободу и мощность, однако изза этого они могут стать слишком сложными. Не злоупотребляйте этим - **оставляйте инлайновые выражения простыми**.

### Почему?

 * Сложные выражения сложнее прочесть и понять
 * Инлайновые выражения нелоьзя переиспользовать, это очевидно ведет дупликации кода и ухуджению его качества.
 * Редакторы и IDE обычно не могут парисить такие выражения а значит у вас не будет автодополнения и валидации.

### Как?

Простое правило - если код инлайнового Javascript выражения становится слишком сложным - **выносите его как отдельный метод в блок methods или computed-свойство, соответственно в блок computed**.

```html
<!-- правильно -->
<template>
	<h1>
		{{ `${year}-${month}` }}
	</h1>
</template>
<script type="text/javascript">
  export default {
    computed: {
      month() {
        return this.twoDigits((new Date()).getUTCMonth() + 1);
      },
      year() {
        return (new Date()).getUTCFullYear();
      }
    },
    methods: {
      twoDigits(num) {
        return ('0' + num).slice(-2);
      }
    },
  };
</script>

<!-- неправильно -->
<template>
	<h1>
		{{ `${(new Date()).getUTCFullYear()}-${('0' + ((new Date()).getUTCMonth()+1)).slice(-2)}` }}
	</h1>
</template>
```

[↑ наверх](#Содержание)

## Оставляйте свойства простыми

Хотя Vue и подерживает передачу атрибутов в виде сложных объектов, старайтесь избегать этого. Старайтесь ограничиться [простыми типами JavaScript](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) и функциями для этого. Не передавайте сложные объекты в компоненты-наследники.

### Почему?

* Используя для каждого свойства отдельный атрибут - API вашего компонента будет более наглядным
* Такой подход совместим с API к которому мы все привыкли у нативных HTML(5) элементов
* Отдельные атрибуты которые вы создали будет легче понять другим членам команды
* При передаче сложных объектов сразу не видно какие из его свойст далее используются - это затруднит рефакторинг.

### Как?

Используйте отдельные атрибуты для каждой опции и передавайте в нее примитив (флаг, строку, число) или функцию.

```html
<!-- правильно -->
<range-slider
  :values="[10, 20]"
  min="0"
  max="100"
  step="5"
  :on-slide="updateInputs"
  :on-end="updateResults">
</range-slider>

<!-- неправильно -->
<range-slider :config="complexConfigObject"></range-slider>
```

[↑ наверх](#Содержание)

## Ограничивайте используйте свойства компонента

В Vue свойства компонента (props) являются отражением его API. Ясное, понятное API делает для других разработчиков проще использовать ваши коменпоненты.

Свойства передаются с использованием специальных атрибутов тега. Этих атрибуты могут быть либо указаны как пустые значения (`:attr`), либо присвоены строкам (`:attr="value"` или `v-bind:attr="value"`). Обратите внимание на подобные возможности при описании свойств.

### Почему?

Грамотное использование свойств обеспечивает что компонент всегда будет отрабатывать без ошибок. Даже если в последствии ваши компоненты будут использоваться так как вы не предполагали изначлаьно.

### Как?

* Используйте свойства по-умолчанию для указания значений свойств
* Используйте свойство `type` для [валидации](http://vuejs.org/v2/guide/components.html#Prop-Validation) значений свойства.
* Всегда проверяйте что свойство определено прежде чем использовать его

```html
<template>
  <input type="range" v-model="value" :max="max" :min="min">
</template>
<script type="text/javascript">
  export default {
    props: {
      max: {
        type: Number, // это обеспечивает проверку что свойство max будет типа Number
        default() { return 10; },
      },
      min: {
        type: Number,
        default() { return 0; },
      },
      value: {
        type: Number,
        default() { return 4; },
      },
    },
  };
</script>
```

[↑ наверх](#Содержание)


## Определяйте `this` как `component`

В контексте кода компонента Vue `this` всегда означает экземпляр самого компонента. Таким образом если вам понадобится обратиться к ней в другом контексте сделайте так чтобы `this` означало  `component`.

Тоесть, **не используйте** устаревшие конструкции присваивания вроде `const self = this;`. Можно и нужно использовать `component` в Vue компонентах для этого.

### Почему?

* Присваивая `this` к переменной названной `component` напрямую укажет тем кто это будет использовать что это означает сам компонент.

### Как?


```html
<script type="text/javascript">
export default {
  methods: {
    hello() {
      return 'hello';
    },
    printHello() {
      console.log(this.hello());
    },
  },
};
</script>

<!-- неправильно -->
<script type="text/javascript">
export default {
  methods: {
    hello() {
      return 'hello';
    },
    printHello() {
      const self = this; // не нужно
      console.log(self.hello());
    },
  },
};
</script>
```
[↑ наверх](#Содержание)



## Структура компонента

Добейтесь чтобы порядок описания компонента было понятно и логично читать.

### Почему?

* Экспортируемый объект (речь о `.js` файле или блоке `<script>` в `.vue` файле) компонента построенный, в каждом случае, по одинаковым принципам ускорит работу с ним других разработчиков и будет служить внутренним страндартом
* Если свойства будут расположены по алфавиту их будет легче просмотреть и найти нужное
* Группировка сходных свойств также облегчает чтение и ориентирование в коде, например props, data, computed; далее watch, d methods; далее lifecycle methods, и тд.
* Используйте имя компонента `name`. Далее разрабтка с использованием [vue devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=en) будет более удобной с именованными компонентами.
* Используйте одну из отраслевых технологий для именования CSS элементов, например [BEM](https://medium.com/tldr-tech/bem-blocks-elements-and-modifiers-6b3b0af9e3ea#.bhnomd7gw)
* Предпочтительный порядок расположения блоков в `.vue` файле: template, потом script и далее style. Потому что большую часть времени любой разработчик проводит в написании HTML и далее JavaScript.

### Как?

Структура компонента, описание свойств в логичном порядке:

```html
<template lang="html">
	<div class="Ranger__Wrapper">
		<!-- ... -->
	</div>
</template>

<script type="text/javascript">
  export default {
		// обязательно не забываем имя к.
    name: 'RangeSlider',
    // можем использовать композицию уже существующих к.
    extends: {},
    // перечисление свойств и переменных
    props: {
	bar: {}, // еще лучше если по-алфавиту
	foo: {},
	fooBar: {},
	},
    data() {},
    computed: {},
    // когда внутри используются другие к.
    components: {},
    // методы
    watch: {},
    methods: {},
    // методы жизненного цикла к.
    beforeCreate() {},
    mounted() {},
};
</script>

<style scoped>
  .Ranger__Wrapper { /* ... */ }
</style>
```

[↑ наверх](#Содержание)



## Именование событий

В Vue все инлайновые выражения и методы компонента напрямую относятся к MV (ViewModel) и меняют ее состояние. При декларации собственных событий важно их грамотно называть чтобы избежать сложности при дальнейшем разработка и использовании компонента.

### Почему?

* разработчики могут использовать имена совпадает с нативными что может вызывать проблема
* полная свобода в выборе имён событий может даже вести к проблемам с обработкой шаблоны

### Как?

* Названия событий стоит использовать кебаб нотацию `kebab-cased`
* название к. должно быть уникальным и отражать что происходит в компоненте, например: upload-success, upload-error or even dropzone-upload-success, dropzone-upload-error
* в именовании лучше использовать только существительные и глаголы, например: client-api-load, drive-upload-success. ([источник](https://github.com/GoogleWebComponents/style-guide#events))

[↑ наверх](#Содержание)



## Избегайте использования `this.$parent`

Vue поддерживает вложенности компонентов, поэтому дочерние компоненты могут обращаться к данным родителя.
Обращение к внутреннему состоянию компонент снаружи в принципе нарушает [FIRST](https://addyosmani.com/first/). Старайтесь избегать конструкции `this.$parent`. Возможны случаи когда это разумный выход, но то слишком плохой дизайн чтобы использовать его всегда.

### Почему?

* компонент Vue, как и любой другой должен работать изолированно. Если для работы требуется взаимодействия с соседними скоупами то нарушается принципе компонентной разработки
* если компоненту требуется обращение к соседям - такой компонент не может быть полноценно переиспользован


### Как?

* передавайте данные из родителя а дочерний компонент используя атрибуты и свойства
* передавайте методы используя коллбеки и выражения в атрибутах
* в обратную сторону - дочерние к. должны генерировать события которые будет перехватить родитель

[↑ наверх](#Содержание)


## Используйте `this.$refs` осторожно

Vue как и React поддерживает обращение к другим компонентам и html-элементам с использованием атрибута ref.
Через обращение к this.$refs разработчик может получить доступ к контексту других компонентов или тегов. В большинстве случаев можно не использовать this.$refs для обращения к другим компонентам.

### Почему?

* если компонент не работает в изоляции это признак плохого дизайна
* для подавляющего большинства случаев, достаточно использовать свойства компонента и его события

### Как?

* Серьезно относитесь к дизайну API ваших компонентов
* Старайтесь избегать умножнений и ветвлений пути исполнения кода в компонентах. Наличие таких фрагментов является признаком того что либо API не достаточно общее, либо вам нужно создать и использовать другие компоненты для других юзкейсов
* Используя компонент, обратите внимание если вдруг какого-то из свойств не хватает; если это так, то добавьте их сами и/или создайте тикет если это osc библиотека
* то же самое с событиями - если чего либо не хватает значит другой разработчик (или вы сами, в прошлом) не добавили их. Нужно это пофиксить - либо добавив отсутсвующее, либо проверить бизнес-логику компонента, возможно это событие уже не используется, тогда его можно просто удалить
* используйте `this.$refs` только если других путей нет и вам никак не обойтись событиями и свойствами
* если по другому никак, то отдавайте предпочтение refs по сравнению с jQuery или `document.queryElement`. Так вы останетесь на одном уровне абстракции (который даёт Vue) а не будете мешать их в трудно понимаемую кучу.


```html
<!-- отлично, можно обойтись без ref -->
<range :max="max"
  :min="min"
  @current-value="currentValue"
  :step="1"></range>
```

```html
<!-- тут придется использовать this.$refs -->
<modal ref="basicModal">
  <h4>Basic Modal</h4>
  <button class="primary" @click="$refs.basicModal.close()">Close</button>
</modal>
<button @click="$refs.basicModal.open()">Open modal</button>

<!-- Modal component -->
<template>
  <div v-show="active">
    <!-- ... -->
  </div>
</template>

<script>
  export default {
    // ...
    data() {
        return {
            active: false,
        };
    },
    methods: {
      open() {
      	this.active = true;
      },
      hide() {
      	this.active = false;
      },
    },
    // ...
  };
</script>

```

```html
<!-- тут можно было обойтись событиями -->
<template>
  <range :max="max"
    :min="min"
    ref="range"
    :step="1"></range>
</template>

<script>
  export default {
    // ...
    methods: {
      getRangeCurrentValue() {
      	return this.$refs.range.currentValue;
      },
    },
    // ...
  };
</script>
```
[↑ наверх](#Содержание)



## Используйте ограниченные стили CSS

В Vue тег конпонента является кастомным HTML-тегом который может использоваться как корневой элемент CSS стилей (`vertical-slider .list-element`). Либо, другой вариант, имя компонета может быть использовано как префикс CSS класса всех стилей компонента (`.vertical-slider .list-element`).

### Почему?

* Ограничивать CSS стили областью компонента делает поведение UI более предсказуемым. А также предохраняет переопределение стилей других элементов на странице из-за сходства селекторов (т.н. "утечку  стилей")
* Использование одинакового имени для названия папки модуля, названия компонента и корневого CSS стиля компонента делает прозрачным для разработчков что это части одного целого.

### Как?

Используйте имя компонента как неймспейс префикс используя методологии BEM и OOCSS и **важно** не пропускайте атрибут `scoped` на теге `<style>`.
Использование этого атрибута оставит инструкцию для компилятора Vue добавить дополнительную сигнатуру на каждый класс который пристутствует в стилях вашего компонента. Это позволит при парсинге браузером (если он поддерживает такую возможность) применять ваши стили к тегам которые составляют ваш компонент по всему приложению и только к ним, предотвращая тн "утечку стилей".


```html
<style scoped>
	/* правильно */
	.MyExample { }
	.MyExample li { }
	.MyExample__item { }

	/* неправильно */
	.My-Example { } /* не ограничен именем компонента или модуля, не соответствует спецификации BEM например */
</style>
```
[↑ наверх](#Содержание)



## Документируйте API компонента

Экземпляр Vue компонента создается при размещении элемента к. в коде приложения. Дополнительная конфигурация экземпляра осуществляется при использовании атрибутов. Для того чтобы компонент мог быть успешно переиспользован другими разработчиками эти атрибуты, по сути они и есть API вашего к. могут быть доходчиво описаны в сопроводительном файле `README.md`.

### Почему?

* Документация предлагает разработчику высокоуровневое описание к. без необходимости вникать в его код. Это делать использование к. более быстрым и простым.
* API компонента в Vue это набор его атрибутов, именно с помощью них он может быть дополнительно настроен. Тоесть именно атрибуты компонента являются важными для тех разработчиков которые будут его в дальнейшем использовать.
* Документация формализует API, показывает разработчикам какая часть функционала сохранять для обратной совместимости при модификации кода к.
* Название `README.md` это по факту отраслевой стандарт названия для документации которую разработчику стоит прочесть прежде чем использовать проект. Многие платформы управления кодом (Github, Bitbucket, Gitlab) по-умолчанию показывают содержание README-файла при просмотре контента любой директории.

### Как?

Добавляйте `README.md` файл в папку с файлами одного компонента:

```
range-slider/
├── range-slider.vue
├── range-slider.less
└── README.md
```

В этом README файле можн описать функционал модуля и варианты использования. Для компонентов vue самым полезным является описать атрибуты к. тк именно они являются выражением API компонента.

[↑ наверх](#Содержание)



## Добавляйте демо компонента

Добавьте `index.html` файл с демо вида компонента в разных конфигурациях показывая как к. может быть использован.

### Почему?

* Наличие демо показывает что модуль работает отдельно
* Демо помогает другим разработчикам посмотреть на результаты - что получится прежде чем разбираться с кодом и/или документацией
* Демо к. исчерпывающе показывает различные варианты конфигурации использования компонента

[↑ наверх](#Содержание)


## Форматируйте код ваших файлов

Линтеры улучшают качество код и это помогает отлавливать синтаксические ошибки. `.vue` файлы можно обрабатывать плагином `eslint-plugin-html`. Если вы используете `vue-cli` то ESLint является доступной опцией по-умолчанию.

### Почему?

* Использование линтера обеспечивает что все разработчики читают и пишут код с одинаковыми правилами форматирования
* Использование линтера помогает обнаружить и избежать ошибок раньше чем код файла сохранен.

### Как?

Чтобы линтеры могли выделять Javascript код из ваших `vue` файлов он должент быть внутри тега `<script></script>`. Также, старатесь сохранять инлайн выражения простыми (см. выше) так как литнет не может их распарсить.

Также настройте линтер указав ему что определена глобальная переменная `vue` в секции `opts`.

#### ESLint

[ESLint](http://eslint.org/) для использования требуется [плагин ESLint HTML](https://github.com/BenoitZugmeyer/eslint-plugin-html#eslint-plugin-html) чтобы экстрагировать Javascript  из `.vue` файлов компонентов.

Настройки ESLint соханяем в файле `modules/.eslintrc` (так чтобы редактор кода также мог его интерпретировать):
```json
{
  "extends": "eslint:recommended",
  "plugins": ["html"],
  "env": {
    "browser": true
  },
  "globals": {
    "opts": true,
    "vue": true
  }
}
```

Запуск ESLint
```bash
eslint modules/**/*.vue
```

#### JSHint

[JSHint](http://jshint.com/) может парсить HTML (используя `--extra-ext`) и выделять код в script (используя `--extract=auto`).

Настройки JSHint соханяем в файле `modules/.jshintrc` (так чтобы редактор кода также мог его интерпретировать):

```json
{
  "browser": true,
  "predef": ["opts", "vue"]
}
```

Запуск JSHint
```bash
jshint --config modules/.jshintrc --extra-ext=html --extract=auto modules/
```
Внимание: JSHint не работает с `.vue`, только `.html`.

---

## Хотите поучаствовать?

Сделайте форк этого репозитория и далее предлагайте PR. Или просто создайте [тикет](https://github.com/pablohpsilva/vuejs-component-style-guide/issues/new).

## Автор перевода

Mikhail Kuznetcov

- Github [shershen08](https://github.com/shershen08)
- Twitter [@legkoletat](https://twitter.com/legkoletat)
