---
title: ETag
slug: Web/HTTP/Headers/ETag
translation_of: Web/HTTP/Headers/ETag
original_slug: Web/HTTP/Заголовки/ETag
---
{{HTTPSidebar}}

`Заголовок HTTP ответа ETag` является идентификатором специфической версии ресурса. Он позволяет более эффективно использовать кеш и сохраняет пропускную способность, позволяя серверу отправлять не весь ответ, если содержимое не изменилось. С другой стороны, если контент все-таки поменялся, `Etag` помогает предотвратить одновременное обновление ресурса от перезаписи друг друга ("воздушная коллизия").

Если ресурс по заданному URL изменился, будет сгенерированно новое значение `Etag`. Поэтому `Etag` чем-то похож на отпечаток ("fingerprints") и может также быть использован для отслеживания предназначения некоторых серверов. Сравнение этих заголовков позволяет быстро определить являются ли два представления ресурса одними и теме же. Отслеживаемый сервер также может задать сохранять их постоянно.

<table class="properties">
  <tbody>
    <tr>
      <th scope="row">Тип заголовка</th>
      <td>{{Glossary("Заголовок ответа")}}</td>
    </tr>
    <tr>
      <th scope="row">
        {{Glossary("Запрещённое имя заголовка")}}
      </th>
      <td>нет</td>
    </tr>
  </tbody>
</table>

## Синтаксис

```
ETag: W/"<etag_value>"
ETag: "<etag_value>"
```

## Директивы

- `W/` {{optional_inline}}
  - : `'W/'` (чувствителен к регистру) указывает, что используется [слабый валидатор](/ru/docs/Web/HTTP/Conditional_requests#Weak_validation). Слабые валидаторы легко сгенерировать, но они намного реже используются для сравнения. Сильные валидаторы идеальны для сравнения, но их может быть очень сложно сгенерировать эффективно. Слабое значение `Etag` двух представлений одного и того же ресурса может быть семантически одинаково, но не байт-в-байт.
- "\<etag_value>"
  - : Тэг сущности, уникально представляющий запрашиваемый ресурс. Это строка ASCII кодов, заключённая в двойные кавычки (например, `"675af34563dc-tr34"`). Метод, по которому генерируются значения `ETag`, не определён. Обычно, используется хэш контента, хэш последнего времени модификации или просто номер ревизии. Например, MDN использует шестнадцатеричных хэш wiki-содержимого.

## Примеры

```
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
ETag: W/"0815"
```

### Как избежать коллизий в процессе работы приложения

С помощью заголовков `ETag` и {{HTTPHeader("If-Match")}}, существует возможность обнаружить коллизии в процессе работы приложения.

Например, при редактировании MDN, текущее содержимое статьи захешировано и помещено в ответ при помощи заголовка `Etag`:

```
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

При сохранении изменений в статье (данные отправляются), {{HTTPMethod("POST")}} запрос будет содержать заголовок {{HTTPHeader("If-Match")}}, значение которого эквивалентно значению `ETag`. Это позволяет проверить актуальность данных.

```
If-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

Если хэши из заголовков не совпадают, это означает что данные уже были изменены между запросами (in-between) и будет возвращена ошибка {{HTTPStatus("412")}} `Precondition Failed`.

### Кеширование неизменяемых ресурсов

Другая типичная ситуация для использования `ETag` — кеширование ресурсов, которые не будут изменяться. Если пользователь повторно посещает URL-адрес (с установленным заголовком `ETag`), и при этом данные слишком устарели и не могут быть использованы, тогда клиент отправит значение `ETag` внутри заголовка {{HTTPHeader("If-None-Match")}}:

```
If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

После чего сервер сравнит клиентский `ETag` (отправленный с помощью `If-None-Match`) с `ETag` для текущей версии ресурса и, если их значения совпадают (т.е. ресурсы не были изменены), сервер вернёт статус {{HTTPStatus("304")}} `Not Modified`, без тела ответа. Такой ответ сервера сообщает клиенту, что закешированная версия ресурса актуальна и готова к использованию.

## Спецификации

| Specification                            | Title                                                        |
| ---------------------------------------- | ------------------------------------------------------------ |
| {{RFC("7232", "ETag", "2.3")}} | Hypertext Transfer Protocol (HTTP/1.1): Conditional Requests |

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- {{HTTPHeader("If-Match")}}
- {{HTTPHeader("If-None-Match")}}
- {{HTTPStatus("304")}} `Not Modified`
- {{HTTPStatus("412")}} `Precondition Failed`
- [W3C Note: Editing the Web – Detecting the Lost Update Problem Using Unreserved Checkout](https://www.w3.org/1999/04/Editing/)