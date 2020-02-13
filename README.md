# JS. 10 Правил хороших практик.
## 1. Не используйте var!
> Объявляйте все переменные с помощью *const* или *let*. Используйте const по умолчанию, если переменная не требует переназначения. Ключевое слово *var* не должно использоваться.

```js
    // Плохо
       var example = 42;
    // Хорошо
       let example = 42;
```

## 2. Объявление на "вершине"
Это хорошая практика кодирования, поместить все объявления в верхней части каждого сценария или функции.
Будет хорошим тоном:
+ Предоставить более чистый код;
+ Обозначить единственное место для поиска локальных переменных;
+ Сделать его проще, чтобы избежать нежелательных (подразумеваемых) глобальных переменных;
+ Уменьшить вероятность нежелательных повторных объявлений;
+ Писать комментарии к коду, который может быть неправильно воспринят.
___

```js
    // Объявить в самом начале
       let firstName, lastName, price, discount, fullPrice;

    // Далее присвоить значения объявленным переменным
       firstName = "Андрей";
       lastName = "Щипунов";

       price = 19.90;
       discount = 0.10;

       fullPrice = price * 100 / discount;
```

Это также относится к переменным цикла:
```js
      // Объявить в самом начале
         const i;

      // Использовать позже
         for (i = 0; i < 5; i++) {
```

> *По умолчанию JavaScript перемещает все объявления наверх [(JavaScript Всплытие)](https://schoolsw3.com/js/js_hoisting.php)*


## 3. Объекты.
+ Для создания объекта используйте фигурные скобки. Не создавайте объекты через конструктор *new Object*.

```js
      // плохо
         let item = new Object();

      // хорошо
         let item = {};
```

+ Не используйте [зарезервированные слова](https://es5.github.io/#x7.6.1) в качестве ключей объектов. Они не будут работать в IE8. [Подробнее](https://github.com/airbnb/javascript/issues/61)

```js
      // плохо
         var superman = {
         default: { clark: 'kent' },
         private: true
      };
      // хорошо
         var superman = {
         defaults: { clark: 'kent' },
         hidden: true
      };
```
+ Не используйте ключевые слова (в том числе измененные). Вместо них используйте синонимы.

```js
        // плохо
           var superman = {
           class: 'alien'
      };

        // плохо
           var superman = {
           klass: 'alien'
      };

        // хорошо
           var superman = {
           type: 'alien'
       };
```

## 4. Массивы.
+ Для создания массива используйте квадратные скобки. Не создавайте массивы через конструктор *new Array*.
   
```js   
        // плохо
           var items = new Array();

        // хорошо
           var items = [];
 ```          

+ Если вам необходимо скопировать массив, используйте *Array::slice*. [jsPerf](https://jsperf.com/converting-arguments-to-an-array/7)

```js   
        let len = items.length,
        itemsCopy = [],
        i;

        // плохо
           for (i = 0; i < len; i++) {
           itemsCopy[i] = items[i];
         }

        // хорошо
           itemsCopy = items.slice();
 ```  
 
 + Чтобы скопировать похожий по свойствам на массив объект (например, NodeList или Arguments), используйте *Array::slice*.
 
 ```js   
    function trigger() {
    let args = Array.prototype.slice.call(arguments);
    ...
}
 ```  
 ## 5. Функции.
 + Объявление функций:
 ```js
   // объявление анонимной функции
        const anonymous = function() {
        return true;
     };

    // объявление именованной функции
        const named = function named() {
        return true;
     };

    // объявление функции, которая сразу же выполняется (замыкание)
        (function() {
            console.log('Если вы читаете это, вы открыли консоль.');
     })();
 ```
 + Никогда не объявляйте функцию внутри блока кода — например в if, while, else и так далее. Единственное исключение — блок функции. Вместо этого присваивайте функцию уже объявленной через var переменной. Условное объявление функций работает, но в различных браузерах работает по-разному.
 > __Примечание__ ECMA-262 устанавливает понятие блока как списка операторов. Объявление функции (не путайте с присвоением функции переменной) не является оператором. [Комментарий по этому вопросу в ECMA-262](https://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97).
 
 ```js
        // плохо
           if (currentUser) {
           function test() {
            console.log('Плохой мальчик.');
           }
        }

        // хорошо
           const test;
           if (currentUser) {
           test = function test() {
              console.log('Молодец.');
            };
        }
```
+ Никогда не используйте аргумент функции *arguments*, он будет более приоритетным над объектом *arguments*, который доступен без объявления для каждой функции.

 ```js
           // плохо
            function nope(name, options, arguments) {
           // ...код...
            }

           // хорошо
            function yup(name, options, args) {
           // ...код...
            }
```
 ## 6. Области видимости.
+ Объявление переменных ограничивается областью видимости, а присвоение — нет.

 ```js
        // Мы знаем, что это не будет работать
        // если нет глобальной переменной notDefined
            function example() {
                console.log(notDefined); // => выбрасывает код с ошибкой ReferenceError
            }

        // Декларирование переменной после ссылки на нее
        // не будет работать из-за ограничения области видимости.
            function example() {
                console.log(declaredButNotAssigned); // => undefined
            const declaredButNotAssigned = true;
            }

        // Интерпретатор переносит объявление переменной
        // к верху области видимости.
        // Что значит, что предыдущий пример в действительности
        // будет воспринят интерпретатором так:
            function example() {
            const declaredButNotAssigned;
                console.log(declaredButNotAssigned); // => undefined
            declaredButNotAssigned = true;
            }
```   
+ Объявление анонимной функции поднимает наверх области видимости саму переменную, но не ее значение.

```js
        function example() {
            console.log(anonymous); // => undefined

        anonymous(); // => TypeError anonymous is not a function
    // Ошибка типов: переменная anonymous не является функцией и не может быть вызвана

        const anonymous = function() {
            console.log('анонимная функция');
        };
    }
```
+ Объявления функции поднимают на верх текущей области видимости и имя, и свое значение.

```js
        function example() {
        superPower(); // => Я лечууууу

        function superPower() {
            console.log('Я лечууууу');
        }
     }
```
 ## 7. Условные выражения и равенства.
+ Используйте === и !== вместо == и !=.
+ Условные выражения вычисляются посредством приведения к логическому типу *Boolean* через метод *ToBoolean* и всегда следуют следующим правилам:
    -__Object__ всегда соответствует __true__
    -__Undefined__ всегда соответствует __false__
    -__Null__ всегда соответствует __false__
    -__Boolean__ остается неизменным
    -__Number__ соответствует __false__, если является __+0, -0, или NaN,__ в противном случае соответствует __true__
    -__String__ означает __false__, если является пустой строкой __''__, в противном случае __true__. Условно говоря, для строки происходит сравнение не ее самой, а ее длины – в соответствии с типом *number*.
    
    ```js
          if ([0]) {
        // true
        // Массив(Array) является объектом, объекты преобразуются в true
}
    ```
+ Используйте короткий синтаксис.
    
    ```js
            // плохо
                if (name !== '') {
            // ...код...
        }

            // хорошо
                if (name) {
            // ...код...
        }

            // плохо
                if (collection.length > 0) {
            // ...код...
        }

            // хорошо
                if (collection.length) {
            // ...код...
        }
    ```
+ Более подробно можно прочитать в статье [Truth Equality and JavaScript](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/) от Angus Croll   
 
 ## 8. Блоки кода.
 + Используйте фигурные скобки для всех многострочных блоков.
 ```js
        // плохо
            if (test)
             return false;

        // хорошо
            if (test) return false;

        // хорошо
            if (test) {
              return false;
            }

        // плохо
            function() { return false; }

        // хорошо
            function() {
                return false;
            }
 ```
 
  ## 9. Комментарии.
  
  + Используйте */** ... */* для многострочных комментариев. Включите описание, опишите типы и значения для всех параметров и возвращаемых значений в формате *jsdoc*.
  
  ```js
       // плохо
        // make() возвращает новый элемент
        // основываясь на получаемом имени тэга
        //
        // @param <String> tag
        // @return <Element> element
            function make(tag) {

        // ...создаем element...

            return element;
        }

       // хорошо
        /**
        * make() возвращает новый элемент
        * основываясь на получаемом имени тэга
        *
        * @param <String> tag
        * @return <Element> element
        */
            function make(tag) {

        // ...создаем element...

            return element;
        }
  ```
  + Используйте // для комментариев в одну строку. Размещайте комментарии на новой строке над темой комментария. Добавляйте пустую строку над комментарием.
  
  ```js
        // плохо
            const active = true;  // устанавливаем активным элементом

        // хорошо
        // устанавливаем активным элементом
            const active = true;

        // плохо
            function getType() {
                console.log('проверяем тип...');
        // задаем тип по умолчанию 'no type'
            const type = this._type || 'no type';

        return type;
    }

        // хорошо
            function getType() {
             console.log('проверяем тип...');

        // задаем тип по умолчанию 'no type'
             const type = this._type || 'no type';

        return type;
    }
  ```

   ## 10. Пробелы.
   + Используйте программную табуляцию (ее поддерживают все современные редакторы кода и IDE) из двух пробелов.
   
   ```js
        // плохо
            function() {
            ∙∙∙∙const name;
        }

        // плохо
            function() {
            ∙const name;
        }

        // хорошо
            function() {
            ∙∙const name;
        }
   ```
   + Устанавливайте один пробел перед открывающей скобкой.
   
   ```js
        // плохо
            function test(){
                console.log('test');
        }

        // хорошо
            function test() {
                console.log('test');
        }

        // плохо
            dog.set('attr',{
            age: '1 year',
            breed: 'Bernese Mountain Dog'
        });

        // хорошо
            dog.set('attr', {
            age: '1 year',
            breed: 'Bernese Mountain Dog'
        });
   ```
   + Используйте отступы, когда делаете цепочки вызовов.
   
   ```js
        // плохо
            $('#items').find('.selected').highlight().end().find('.open').updateCount();

        // хорошо
            $('#items')
              .find('.selected')
                .highlight()
                .end()
              .find('.open')
                .updateCount();

        // плохо
            const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').class('led', true)
            .attr('width',  (radius + margin) * 2).append('svg:g')
            .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
            .call(tron.led);

        // хорошо
            const leds = stage.selectAll('.led')
            .data(data)
                .enter().append('svg:svg')
                .class('led', true)
                .attr('width',  (radius + margin) * 2)
            .append('svg:g')
                .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
                .call(tron.led);
   ```
