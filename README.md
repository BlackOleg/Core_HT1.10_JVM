# Задача "Понимание JVM"

## Поэтапно

### ClassLoader'ы

Согласно спецификации Java SE для того, чтобы получить работающий в JVM код, необходимо выполнить три этапа:

1. Загрузка байт-кода из ресурсов и создание экземпляра класса Class

сюда входит поиск запрошенного класса среди загруженных ранее, получение байт-кода для загрузки и проверка его корректности, создание экземпляра класса Class (для работы с ним в runtime), загрузка родительских классов. Если родительские классы и интерфейсы не были загружены, то и рассматриваемый класс считается не загруженным.

2. Связывание (или линковка)

по спецификации этот этап разбивается еще на три стадии:

- Verification, происходит проверка корректности полученного байт-кода.
- Preparation, выделение оперативной памяти под статические поля и инициализация их значениями по умолчанию (при этом явная инициализация, если она есть, происходит уже на этапе инициализации).
- Resolution, разрешение символьных ссылок типов, полей и методов.

3. Инициализация полученного объекта

**Сборка согласно  схемы:**

<img src="pic\classloaders_shema.png" alt="classloaders shema" style="width:600px">

Все найденные классы загружаются в  Metaspace

- данные о классе (имя, методы, поля и др);
- константы.  (У нас в примере констант - нет)

    области памяти (стэк (и его фреймы), хип, метаспейс)
    сборщик мусора

# Код для исследования и комментариев #

```java
public class JvmComprehension {         // placed in MetaSpace
    public static void main(String[] args) {  
        int i = 1;                      // 1   создается фрейм в Stack Memory для i=1     
        Object o = new Object();        // 2   создается фрейм в Stack Memory для о,
                                        // а  память для объекта в heap area 
        Integer ii = 2;                 // 3   Integer - ccылочный тип т.е. создается объект
                                        // и значит создается фрейм в Stack Memory ii, а  память 
                                        // для объекта в heap ** 
        printAll(o, i, ii);             // 4   в момент вызова создается фрейм в  Stack Memory   
        System.out.println("finished"); // 7   Создастся новый фрейм в стеке, куда передадим текст
    }
```


```java

   private static void printAll(Object o, int i, Integer ii) {
        Integer uselessVar = 700;                   // 5 Integer - ccылочный тип т.е. создается объект 
                                                    // и значит создается фрейм в Stack Memory ii,
                                                    // а память для объекта в heap
        System.out.println(o.toString() + i + ii);  // 6 Создастся новый фрейм в стеке, куда 
                                                    //   передадим ссылку на комбинацию переменных
    }
}

```
