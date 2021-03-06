# Локальный CSS

Когда у тега `<style>` есть атрибут `scoped`, то его CSS будет применяться только к элементам текущего компонента. Это похоже на инкапсуляцию стилей в Shadow DOM. Ими можно пользоваться с некоторыми оговорками, но не требуется никаких полифиллов. Это достигается за счёт использования PostCSS для преобразования следующего:

``` html
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>
```

В что-то подобное:

``` html
<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>

<template>
  <div class="example" data-v-f3f3eg9>hi</div>
</template>
```

#### Советы

### Использование локальных и глобальных стилей

Вы можете сочетать использование в компоненте локальных и глобальных стили одновременно:

``` html
<style>
/* глобальные стили */
</style>

<style scoped>
/* локальные стили */
</style>
```

### Корневой узел дочернего компонента

С помощью `scoped`, стили родительского компонента не будут влиять на содержимое дочерних компонентов. Тем не менее, корневой узел дочернего компонента будет зависеть как от scoped CSS из родительского элемента, так и от scoped CSS дочернего. Это предусмотрено специально, чтобы родительский элемент мог стилизовать корневой элемент дочернего компонента например для целей макета.

### Глубокие селекторы

Если вы хотите, чтобы селектор в `scoped` стилях был "глубоким", т.е. влиял на дочерние компоненты, вы можете использовать комбинатор `>>>`:

``` html
<style scoped>
.a >>> .b { /* ... */ }
</style>
```

Указанное выше будет скомпилировано в:

``` css
.a[data-v-f3f3eg9] .b { /* ... */ }
```

Некоторые пре-процессоры, такие как SASS, не могут правильно обработать `>>>`. В таких случаях вы можете использовать комбинатор `/deep/` — это псевдоним для `>>>` работающий точно также.

### Динамически генерируемый контент

DOM-содержимое, создаваемое с помощью `v-html` не попадает под область действия scoped-стилей, но вы всё равно можете его стилизовать воспользовавшись deep-селекторами.

### О чём следует помнить

- **Локальные стили не устраняют необходимость классов**. Из-за того как браузеры рендерят различные CSS-селекторы, `p { color: red }` может быть в разы медленнее при использовании в локальных стилях (например, когда комбинируется с селектором по атрибуту). Если же вы используете классы или ID, такие как `.example { color: red }`, тогда вы практически полностью исключаете ухудшение производительности. [Вот пример](http://stevesouders.com/efws/css-selectors/csscreate.php) где вы можете проверить разницу самостоятельно.

- **Будьте внимательны с селекторами потомков в рекурсивных компонентах!** Для CSS-правила с селектором `.a .b`, если элемент, который соответствует `.a` содержит рекурсивный компонент потомок, тогда все `.b` в этом компоненте потомке будут также соответствовать правилу.