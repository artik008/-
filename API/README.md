# Инструкция по работе с тестовым проектом Postman

## Создать сессию через POST-запрос «Создать сессию». 
Для этого
- На вкладке «Auth» ввести логин/пароль клиента, который был выдан при подключении к тестовому стенду Альфа-Линк (по умолчанию можно тестировать на пользователе, который сохранен в проекте Postman)
- На вкладке «Headers» в поле «CustomerId» ввести номер счета клиента, с которым был настроен тестовый обмен с банком
- Нажать «Send» и скопировать ответ «SID»

## Отправить документ через POST-запрос «Отправить документа». 
Для этого
- На вкладке «Auth» ввести логин/пароль клиента, который был выдан при подключении к тестовому стенду Альфа-Линк Линк (по умолчанию можно тестировать на пользователе, который сохранен в проекте Postman)
- На вкладке «Headers» в параметр «CustomerId» ввести номер счета клиента, в параметр «SID» = значение из 1.1
- На вкладке «Body» внести исходных xml документ (Платеж, Выписка, Ведомость в банк, Запрос статуса), созданных по схемам 1C
- Нажать «Send»

## Получить список пакетов, которые не были получены клиентом через Get-запрос «Получить список пакетов»
- На вкладке «Auth» ввести логин/пароль клиента, который был выдан при подключении к тестовому стенду Альфа-Линк
- На вкладке «Headers» в параметр «CustomerId» ввести номер счета клиента, в параметр «SID» = значение из 1.1
- Нажать «Send» и скопировать значения идентификаторов пакетов

## Получить значение пакета через Get-запрос «Получить значение пакета (извещения)»
- На вкладке «Auth» ввести логин/пароль клиента, который был выдан при подключении к тестовому стенду Альфа-Линк
- На вкладке «Headers» в параметр «CustomerId» ввести номер счета клиента, в параметр «SID» = значение из 1.1
- На вкладке «Params» ввести параметр из 3.3
- Нажать «Send» 

# Подписание документов

В зависимости от настроек обмена с банком документы могут иметь различные маршруты подписания (подробнее https://github.com/1C-Company/DirectBank/blob/master/doc/common-section/type-tables.md#edo-Settings_Data_CryptoParam[…]omerSignature_GroupSignatures)

На тестовом стенде по умолчанию устанавливаются следующие маршруты (в случае необходимости их можно изменить.):
- Платежное поручение – единоличная подпись/2 подписи;
- Выписка – без подписи;
- Выписка в банк – без подписи.

Электронная подпись формируется в формате PKCS#7 с включением сертификата. Подпись храниться в отдельных тегах схемы 1С (теги описаны в схеме XML-схема описания общих типов: 1C-Bank_Exch-Common.xsd). 

Ссылка на xsd-схемы: https://github.com/1C-Company/DirectBank/blob/master/doc/xsd-scheme/readme.md

# Формирование выписки

Исходный файл xml
```
<?xml version="1.0" encoding="UTF-8"?>
<Packet xmlns="http://directbank.1c.ru/XMLSchema" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" id="d9f1aab0-6863-432d-89e6-6c653ff7c9c6" formatVersion="2.2.2" creationDate="2020-09-28T13:51:45.483" userAgent="1С - БЭД: 1.7.2.55; БиблиотекаЭлектронныхДокументовДемо: 1.7.2.55">
  <Sender>
    <Customer id="40702810601300003716" name="Коллегия адвокатов &quot;Хренов и Партнеры&quot;" inn="7701336883"/>
  </Sender>
  <Recipient>
    <Bank bic="044525593" name="АО &quot;АЛЬФА-БАНК&quot;"/>
  </Recipient>
  <Document id="f816dfd4-4683-4f68-b912-6abd2c9e3eb4" dockind="14" formatVersion="2.2.2"> <Data>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4NCjxTdGF0ZW1lbnRSZXF1ZXN0IGNyZWF0aW9uRGF0ZT0iMjAyMC0xMi0yOFQxMzo1MTo0NS40NjYiIGZvcm1hdFZlcnNpb249IjIuMi4yIiBpZD0iZjgxNmRmZDQtNDY4My00ZjY4LWI5MTItNmFiZDJjOWUzZWI0IiB1c2VyQWdlbnQ9IjHQoSAtINCR0K3QlDogMS43LjIuNTU7INCR0LjQsdC70LjQvtGC0LXQutCw0K3Qu9C10LrRgtGA0L7QvdC90YvRhdCU0L7QutGD0LzQtdC90YLQvtCy0JTQtdC80L46IDEuNy4yLjU1IiB4bWxucz0iaHR0cDovL2RpcmVjdGJhbmsuMWMucnUvWE1MU2NoZW1hIiB4bWxuczp4cz0iaHR0cDovL3d3dy53My5vcmcvMjAwMS9YTUxTY2hlbWEiIHhtbG5zOnhzaT0iaHR0cDovL3d3dy53My5vcmcvMjAwMS9YTUxTY2hlbWEtaW5zdGFuY2UiPgoJICA8U2VuZGVyIGlkPSI0MDcwMjgxMDYwMTMwMDAwMzcxNiIgaW5uPSI3NzAxMzM2ODgzIiBrcHA9IjAxNDM1MDM3MyIgbmFtZT0i0JrQvtC70LvQtdCz0LjRjyDQsNC00LLQvtC60LDRgtC+0LIgJnF1b3Q70KXRgNC10L3QvtCyINC4INCf0LDRgNGC0L3QtdGA0YsmcXVvdDsiLz4KCSAgPFJlY2lwaWVudCBiaWM9IjA0NDUyNTU5MyIgbmFtZT0i0JDQniAmcXVvdDvQkNCb0KzQpNCQLdCR0JDQndCaJnF1b3Q7Ii8+CgkgIDxEYXRhPgoJCSAgICA8U3RhdGVtZW50VHlwZT4wPC9TdGF0ZW1lbnRUeXBlPgoJCSAgICA8RGF0ZUZyb20+MjAyMS0wMS0xNVQxMzo1MTo0NS40Nzc8L0RhdGVGcm9tPgoJCSAgICA8RGF0ZVRvPjIwMjEtMDEtMjJUMTM6NTE6NDUuNDc3PC9EYXRlVG8+CgkJICAgIDxBY2NvdW50PjQwNzAyODEwNjAxMzAwMDAzNzE2PC9BY2NvdW50PgoJCSAgICA8QmFuaz4KCQkJICAgICAgPEJJQz4wNDQ1MjU1OTM8L0JJQz4KCQkJICAgICAgPE5hbWU+0JDQniAmcXVvdDvQkNCb0KzQpNCQLdCR0JDQndCaJnF1b3Q7PC9OYW1lPgoJCQkgICAgPC9CYW5rPgoJCSAgPC9EYXRhPgo8L1N0YXRlbWVudFJlcXVlc3Q+</Data>
  </Document>
</Packet>
```
