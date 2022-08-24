# Документация REST API TDMS
Документация REST API для конфигураций, разработанных на базе [TDMS](https://tdms.ru) (Technical Data Management System ) с использованная в приложении TDMS Application Server. Текущая поддерживаемая версия [`6.1.150.0`]( https://ftp.csoft.ru/file_36462546162fb6eda0c2c8)

## Содержание
- [Развертывание](#Развертывание)
- [Обзор](#Обзор)
- [Авторизация](#Авторизация)
- [Request body](#Request-body)
    - [TObject](#TObject)
    - [TUser](#TUser)
    - [TAttribute](#TAttribute)
    - [JParam](#JParam)
    - [JAPIEventFilter](#JAPIEventFilter)
- [Создание объекта](#Createobject-post)
- [Изменение объекта](#Editobject-post)
- [Выполнение exetrn функции](#ExecuteFunc-post)
- [Получение событий](#GetAPIEvents-post)

## Развертывание
Для развертывания решения необходимо скачать репозиторий, нажав на `Download ZIP`:
![download repository!](./assets/001.png)

Далее разархивировать архив и скопировать в составе репозитория папку `TDMSAPI` и поместить ее в папку `Extensions` в пути установки `TDMS Application Server`: `C:\Program Files (x86)\CSoft\TDMS Server 6.1\Extensions`

Затем в приложении `TDMS Конфигуратор` (в составе `TDMS Application Server`) во вкладке `Модули` включить модуль `TDMSAPI`:
![download repository!](./assets/002.png)

Далее необходимо принять изменения и запустить сервер

## Обзор
Для обращения к API используйте адрес хоста, указанный в настройках приложения `TDMS Конфигуратор`. Далее к адресу хоста добавляется endpoint `/api`

```
http://your_host/api
```
Пример:
```
http://tdms-srv-virt:444/api
```
Используются запросы `POST` и `GET`. Тело запроса `POST` передается в формате `JSON`. Требуемый метод API (`Createobject`, `Editobject` и т.д.) необходимо указывать в параметре `mode` в содержимом запроса `POST`. Перечень обязательных параметров запроса для каждого метода API индивидуален

## Авторизация
Все запросы авторизуются с помощью `access_token`, который необходимо добавить в `headers`. 
```
Authorization: Bearer eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..etmTl5Im6-yxCVhjgP5Ajw.-iuDNbbDtq289WBAHg56PPlq4-dMqsx6YTOFoVbjNc1wHEHcXQwj-uDTr-KnnujSq8S8DBgMtxx7WCbFzwtpRYlr_VprBbiGt1IvcGvaHU0_paGFfl8hqp0sH-dOC_IjWoNq0JzibpXQrTBWDBDYbCyREY0dsHd4RRZhduG5qK_48MPx3VVv6UBeFH6KqS5kKWlkk0uek8LJaeRKxldDmJBC1mry99Epx8-E-mLHn2MBcGiplYTpqsU93B4fLQtfwqFc6siNSkwj6jP-xK8jgNPKBBdxNQUqlo_dzX4hC5odeD2ZZZ71CdrM7zRuJxXv3NRpx8EWQwSSVfue8H28Ez-wdpprEsJjhRZc6Fe-IAtLkCHEAo8bTCSbiSGwv-iWj9dzfCF2N8ZWObViTbRkDTElyEqQBnmWwwW8F2IFHgiKm-8OhJPjuzkpFXHxmyQKtn3cmpOHwVhG-ReK6sogoVNo4ipC7DoAUnUU_sLgw-h9ybtIaSenk8kmHQMTyD1lVP-HBM8vEVd3PZiKF-SFwvbTgbFfR1c3cpuFu1SRbUesN7xYZ_zI5OrqL8xQ3x0nGbN2U5n8gH_-uEMcGr6yT-d4rdgKuN3hiZ8YMTnZsoIFZcD8pUoi2aQVf4YLl9C4.93DwkSd8A2kYo1verZcV363GPwhBycjwDd9Pw2VAkws
```
Время жизни `access_token` по умолчанию 720 мин. Этот параметр может быть изменен в файле `Tdms.Config`
```xml
 <server AccessTokenExpireMinutes="720" RefreshTokenExpireMinutes="4320" ResourcesExpireMinutes="720"
```
Для получения `access_token` необходимо отправить запрос на endpoint `/token`
```
http://your_host/token
```
где в `headers` поместить `hash`:
```
Authorization: base64_encode(login:password)
```
Пример:
```
Authorization: Basic bGFiYWtzaGluYToxMjNxd2U=
```
В ответе будет получен `grant` авторизации в формате `json`:
```
Authorization: Basic bGFiYWtzaGluYToxMjNxd2U=
```
>ВАЖНО: при перезагрузке приложения TDMS Application Server выданные токены становятся недействительными

Пример:
```json
{
    "$id": "1",
    "$type": "Tdms.Server.Security.TokensInfo, Tdms.Server.Host",
    "access_token": "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..etmTl5Im6-yxCVhjgP5Ajw.-iuDNbbDtq289WBAHg56PPlq4-dMqsx6YTOFoVbjNc1wHEHcXQwj-uDTr-KnnujSq8S8DBgMtxx7WCbFzwtpRYlr_VprBbiGt1IvcGvaHU0_paGFfl8hqp0sH-dOC_IjWoNq0JzibpXQrTBWDBDYbCyREY0dsHd4RRZhduG5qK_48MPx3VVv6UBeFH6KqS5kKWlkk0uek8LJaeRKxldDmJBC1mry99Epx8-E-mLHn2MBcGiplYTpqsU93B4fLQtfwqFc6siNSkwj6jP-xK8jgNPKBBdxNQUqlo_dzX4hC5odeD2ZZZ71CdrM7zRuJxXv3NRpx8EWQwSSVfue8H28Ez-wdpprEsJjhRZc6Fe-IAtLkCHEAo8bTCSbiSGwv-iWj9dzfCF2N8ZWObViTbRkDTElyEqQBnmWwwW8F2IFHgiKm-8OhJPjuzkpFXHxmyQKtn3cmpOHwVhG-ReK6sogoVNo4ipC7DoAUnUU_sLgw-h9ybtIaSenk8kmHQMTyD1lVP-HBM8vEVd3PZiKF-SFwvbTgbFfR1c3cpuFu1SRbUesN7xYZ_zI5OrqL8xQ3x0nGbN2U5n8gH_-uEMcGr6yT-d4rdgKuN3hiZ8YMTnZsoIFZcD8pUoi2aQVf4YLl9C4.93DwkSd8A2kYo1verZcV363GPwhBycjwDd9Pw2VAkws",
    "username": "labakshina",
    "expires_in": 43260,
    "refresh_token": "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..VX1prrofk14U5yjmmVfSqA.E1byQDYghJWDfP2Tpxxdbz7P4wdTbCsDwnYLg-WY2WXX_sOqXqISaYV647pDM71nAtwy53y19Ou1SAIoUqfeA9xriEqbFNk1MwTCWQ7S4NnW1EPlnD9KcU4aaTWPfgOLQsgVvwYMPPNUEs8EDu20RQZweSPqyGoDk4EyLZ0xoXhS3f38ZjWEz2w1kSWdYeZD4Bqc000rvVuYUgxmkOp1ksFGuDi8UTFPB8RlkGUvnCd4nmj6mKp1S-iOoe561IDqg98YJsdnGyHmCgT0wfOtPPpZ4pr-Ey-6jygYDNqw1tSe5kMkVVYaeUoAq26ixZxdGsh2eeTDSVRzYD5Tg15Cin1he8wapmZ8O4NKsCV6AhJvKNII5MO5TEgMZEWZKj70QH_L0Om7cAXxoMFkyEidyCWUPV-YzCUytY67lpNZx5MdvOSppm88lEZHCvKtF5LavLeaQ35d-6lGVhE9YDBinTUoCDlr-lg2_1s25DPZHS_2mqZr-UqNU2vXp8KtMPXV2cpaklvUY5-qHVPn1eYsHM-m2smTp6LAI8oW-v62bR8LFqrEcg-I52MkYf9g-qq9POKRcw17w6SeODKWzuP90oDuCRidQsJF70OfzvEXrNhxT-qp1v3T6VPNoDeE994hos-j6KskQ_ny6eDWOYt71o4q8KjSeLhAxK6_u9n0D7w.UPU1E5fMfK3yoaXBEFu65v0ysheV38tMM38WzXj2_RI"
}
```

## Request body
Ниже представлено тело `POST` запроса. Оно содержит параметры требуемые для метода API, указанного в параметре `mode` 

```json
{
    "mode": "",
    "TObject": {},
    "TUser": {},
    "Parameters": [],
    "APIEventFilters": []
}
```
|Parameter      |Type                               |Description
|-              |-                                  |-               
|mode           |string                             |Вызываемый метод API
|TObject        |[TObject](#TObject)                |Описание объекта TDMS
|TUser          |[TUser](#TUser)                    |Описание пользователя TDMS
|Submode        |string                             |Задает именованные параметры submode
|Parameters     |[JParam](#JParam)                  |Задает имя пользовательского метода в конфигурации, который необходимо выпонить
|APIEventFilters|[JAPIEventFilter](#JAPIEventFilter)|Задает фильтры для получения специальных событий

### `TObject`
Объект `TObject` иллюстрирует информационный объект TDMS. С его помощью передаются свойства, атрибуты и состояния последнего. Также используется в ответах на запросы

```json
{
    "GUID": "{5C7CF22C-F5C6-48B5-AB23-64039177D9A5}",
    "ObjectGuid": "{5689EAE5-41EF-4D5A-9BF7-487640B5E873}",
    "ObjectDefName": "O_DocClaim",
    "Description": "Hello",
    "Parent": "{58B5507B-2E39-48E2-901F-882DD5676783}",
    "StatusName": "S_DocClaim_Actual",
    "ModifyTime": "24.08.2022 10:49:11",
    "ModifyUser": "AS7E-WTDG-YFHR-YFHR-JW7F",
    "ActiveVersion": "true",
    "VersionDescription": "Выдано на экспертизу",
    "VersionName": "1",
    "VersionCreateTime": "12.02.2021 12:48:48",
    "jAttributes":[
        {"SysName": "A_Int_DocVersion", "Value":2, "Type": "tdmString"},
        {"SysName": "A_Real_SheetLength", "Value":1.22, "Type": "tdmReal"},
        {"SysName": "A_User_Author", "Value":"USER_ABA3440A_0B0C_40AD_A329_D85B5FBABFFE", "Type": "tdmUserLink"},
        {"SysName": "A_Str_Answer", "Value":"Some answer", "Type": "tdmString"},
        {"SysName": "A_Str_Designation", "Value":"777", "Type": "tdmString"},
        {"SysName": "A_Ref_Project", "Value":"{F164E1BB-A204-4996-9A2D-799B65B0D15E}", "Type": "tdmObjectLink"}
    ]
}
```
|Parameter         |Type                       |Read only|Description
|-                 |-                          |-        |-               
|GUID              |string                     |true     |Идентификатор объекта TDMS
|ObjectGuid        |string                     |true     |Общий для всех версий идентификатор объекта TDMS
|ObjectDefName     |string                     |true     |Имя типа объекта TDMS
|Description       |string                     |         |Описание типа объекта TDMS
|Parent            |string                     |true     |Идентификатор родительского объекта TDMS
|StatusName        |string                     |         |Статус объекта TDMS
|ModifyTime        |string                     |true     |Дата последнего изменения объекта TDMS 
|ModifyUser        |string                     |true     |Пользователь последним изменивший объект TDMS
|ActiveVersion     |bool                       |         |Является ли текущая версия объекта TDMS активной
|VersionDescription|string                     |         |Описание версии объекта TDMS
|VersionName       |string                     |         |Имя версии объекта TDMS
|VersionCreateTime |string                     |         |Дата создания версии объекта TDMS
|jAttributes       |[[TAttribute](#TAttribute)]|         |Массив атрибутов объекта TDMS

### `TAttribute`
Экземпляр атрибута объекта TDMS. Как правило входит в коллекцию атрибутов `JAttributes`
```json
{
    "Sysname": "ATTR_NAME",
    "Value": "Сооружение жилого типа",
}
```
|Parameter|Type  |Required|Description
|-        |-     |-       |-               
|Sysname  |string|true    |системное имя атрибута
|Value    |string|true    |значение атрибута

### `TUser`
Описывает пользователя TDMS. Также используется в ответах на запросы

```json
{
    "Sysname": "U_X_175CCDF4-3D47-4C0A-A246-8033F90D274D",
    "Description": "Василий Пупкин",
    "Login": "vpupkin",
    "Password": "",
    "Firstname": "Вася",
    "Lastname": "Пупкин",
    "Middlename": "Михайлович",
    "Phone": "89271234567",
    "Mail": "vpupkin@gmail.com",
    "Position": "NODE_F6FFCDF6_6F8F_423A_81FA_68693B2B5218"
    "Department": "NODE_B21A1E89_B7D8_4A56_BB54_99E1F6C84283"
}

```

|Parameter      |Type  |Read only|Description
|-              |-     |-        |-               
|Sysname        |string|true     |Системное имя
|Description    |string|         |Описание (обычно фамилия и инициалы пользователя)
|Login          |string|         |Логин
|Password       |string|         |Пароль
|Firstname      |string|         |Имя
|Lastname       |string|         |Фамилия
|Middlename     |string|         |Отчество
|Phone          |string|         |Телефон
|Mail           |string|         |Электронная почта
|Position       |string|         |Занимаемая должность
|Department     |string|         |Подразделение (отдел), в состав которого включен пользователь

### `JParam`
Представляет параметр функции указанной в `submode`
```json
{
    "Name": "i",
    "Type": "int",
    "Value": "123"
}
```
|Parameter |Type  |Required|Description
|-         |-     |-       |-               
|Name      |string|true    |Имя параметра в соответствии с декларацией метода
|Type      |string|        |Тип параметра, в который должна быть произведена конвертация перед вызовом метода. Указывается только в том случае, если декларация метода не содержит явного указания типа (например в vbs). Если тип не указан ни в декларации метода ни в этом поле, подразумевается тип string.
|Value     |string|true    |Строковое представление значения параметра (корректно конвертируемое в указанный type)

### `JAPIEventFilter`
Возвращать события начиная с этой временной точки (включая ее) (UTC)
```json
{
    "Sysname": "ATTR_NAME",
    "Value": "Сооружение жилого типа",
}
```
|Parameter |Type    |Required|Description
|-         |-       |-       |-               
|From      |DateTime|        |Возвращать события начиная с этой временной точки (включая ее) (UTC)
|Before    |DateTime|        |Возвращать события начиная до этой временной точки (не включая ее) (UTC)
|Type      |string  |true    |Возвращать события данного типа

## Createobject `POST`
Создание объекта TDMS. Объект может быть создан как в составе указанного родителя `parent`, так и не имея родителя. Не все свойства объекта TDMS могут быть изменены

### Request:
```json
Content-Type: application/json
```
```json
{
    "mode": "CreateUser",
    "TObject": {
        "ObjectDefName": "O_DocClaim",
        "Description": "Hello",
        "Parent": "{58B5507B-2E39-48E2-901F-882DD5676783}",
        "StatusName": "S_DocClaim_Actual",
        "jAttributes":[
            {"SysName": "A_Int_DocVersion", "Value":2},
            {"SysName": "A_Str_Answer", "Value":"Some answer"},
            {"SysName": "A_Str_Designation", "Value":"777"},
            {"SysName": "A_Ref_Project", "Value":"{F164E1BB-A204-4996-9A2D-799B65B0D15E}"},
        ]
    }
}
```

Обязательные параметры:

|Parameter                  |Type               |Description
|-                          |-                  |-               
|mode                       |string             |Createobject - вызываемый метод API
|TObject                    |[TObject](#TObject)|Объект TDMS
|TObject.ObjectDefName      |string             |Имя типа объекта TDMS

### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

GUID созданного объекта TDMS:
```
{USER_5CC201F2_3306_4A92_A2D1_65FAD0E25916}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

## Editobject `POST`
Изменение объекта TDMS. Могут быть изменены свойства, атрибуты и состояния объекта TDMS. Перечень изменяемых параметров описыватся объектом [TObject](#TObject)

### Request:

```json
{
    "mode": "Editobject",
    "TObject": {
        "ObjectDefName": "O_DocClaim",
        "Description": "Новое описание",
        "GUID": "{5689EAE5-41EF-4D5A-9BF7-487640B5E873}",
        "StatusName": "S_DocClaim_Actual",
        "jAttributes":[
            {"SysName": "A_Int_DocVersion", "Value":2},
            {"SysName": "A_Str_Answer", "Value":"Some answer"},
            {"SysName": "A_Str_Designation", "Value":"777"},
            {"SysName": "A_Ref_Project", "Value":"{F164E1BB-A204-4996-9A2D-799B65B0D15E}"},
        ]
    }
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|mode        |string             |Editоbject - вызываемый метод API
|TObject     |[TObject](#TObject)|объект TDMS
|TObject.GUID|string             |идентификатор объекта TDMS

### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Статус успешно выполненного запроса:

```
ok
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

## Getobject `POST`
Получение информации об объекте TDMS по GUID указанном в [TObject](#TObject)

### Request:

```json
{
    "mode": "Getobject",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|mode        |string             |Editоbject - вызываемый метод API
|TObject     |[TObject](#TObject)|объект TDMS
|TObject.GUID|string             |идентификатор объекта TDMS

### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Статус успешно выполненного запроса:

```
ok
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

## Createuser `POST`
--------------------------------------------------------------------------------------------------------------------
Создание пользователя TDMS. Возвращает системное имя пользователя. Логин пользователя генерится автоматически по правилу:
`{Lastname}{Firstname}`

### Request:

```
Content-Type: application/json
```

```json
{
    "Mode": "Createuser",
    "TUser": {
        "Firstname": "Вася",
        "Lastname": "Пупкин",
        "Middlename": "Михайлович",
        "Password": "uey6R4wHu",
        "Phone": "89271234567",
        "Mail": "vpupkin@gmail.com"
    }
}
```
Обязательные параметры:

|Parameter          |Type            |Description
|-                  |-               |-               
|mode               |string          |Createuser - вызываемый метод API
|user               |[TUser](#TUser) |
|user.Firstname     |string          |
|user.Lastname      |string          |
|user.Password      |string          |
|user.Phone         |string          |
|user.Mail          |string          |

### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Системное имя пользовател TDMS:

```
USER_5CC201F2_3306_4A92_A2D1_65FAD0E25916
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

---

## UpdateUser `POST`

Updates attributes for existing TDMS User

### Request:
```
Content-Type: application/json
```
```json
{
    "mode": "UpdateUser",
    "user": {
        "sysname": "USER_50DC2BB0_C6F7_4467_B0DA_8C24DAC292D7",
        "login": "",
        "password": "",
        "firstname": "Вася",
        "lastname": "Пупкин",
        "patronymic": "Михайлович",
        "phone": "89274433788",
        "email": "vpupkin@gmail.com",
        "position": "NODE_C9A2678A_A99B_4BF1_AD81_C497ACBF2DF9",
        "department": "NODE_83E68FBA_0D8A_4D3C_8F82_95161D5BBCA5"
    }
```

|Parameter      |Type  |Required|Description
|-              |-     |-       |-               
|mode           |string|**true**|name of method your invoke
|user           |object|**true**|object user contains TDMS user's properties
|user.sysname   |string|**true**|TDMS users's sysname
|user.login     |string|false   |TDMS users's login
|user.firstname |string|false   |TDMS users's firstname
|user.lastname  |string|false   |TDMS users's lastname
|user.patronymic|string|false   |TDMS users's patronymic
|user.password  |string|false   |TDMS users's password
|user.phone     |string|false   |TDMS users's phone
|user.email     |string|false   |TDMS users's email
|user.position  |string|false   |TDMS Position Classifier's sysname
|user.department|string|false   |TDMS Department Classifier's sysname

### Response
```
Status: 200
Content-Type: text/plain; charset=UTF-8
```
Status name:
```
ok
```

Ошбки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Method {mode} not found
|404 NotFound  |Invalid 'mode' value. Should be 'createuser', 'updateuser' etc.
|404 NotFound  |TDMS User sysname = {sysname} not found";

## ExecuteFunc `POST`
Метод позволяет выполнить специальную функцию, определенную в конфигурации (на vbs) или в специальном расширении сервера. Функция должна быть помечена в скрипте как `extern`.

Функция должна возвращать только примитивный тип данных (string, date, bool, int, float).

Запускается функция с правами авторизовавшегося пользователя, но внутри функции возможно повышение привилегий (через `SysadminPermissions` или `SysadminModeOn`).

Исключения, возникающие при вызове, генерируют ошибку HTTP. Превышение времени ожидания генерирует ошибку HTTP. 

### Request:
```json
Content-Type: application/json
```
```json
{
  "Mode": "ExecuteFunc",
  "Submode": "C_TST.Test",
  "Parameters": [
    {
      "name": "i",
      "type": "int",
      "value": "123"
    },
    {
      "name": "s",
      "value": "Some String"
    }
  ]
}
```

Обязательные параметры:

|Parameter  |Type               |Description
|-          |-                  |-               
|mode       |string             |ExecuteFunc - вызываемый метод API
|Submode    |string             |Задает имя пользовательского метода в конфигурации, который необходимо выпонить. Если имя метода содержит точку, то точка должна быть строго одна (и не должна стоять в начале или в конце строки). Имя слева от точки трактуется как имя произвольного класса доступного в сборке, реализующей контроллер на базе TDMSCPIController, а имя справа от точки трактуася как имя публичного метода этого класса. Если имя метода не содержит точки, оно трактуется как имя метода непосредственно в классе контроллера (основанного на TDMSCPIController).
|Parameters |[[JParam](#JParam)]|Задает именованные параметры submode

### Response

```
Status: 200
Content-Type: application/json
```

Возвращает результат выполнения преобразованный в текстовый формат

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |Не указано имя функции
|404 NotFound  |Не найдена функция расширения, либо недостаточно прав для ее вызова

## GetAPIEvents `POST`
Позволяет получить список определенных событий, произошедших в TDMS за определенный период времени. Можно получить специальные события API, а также некоторые стандартные события TDMS связанных с объектами TDMS определенного типа.

Специальные события регистрируется при вызове функции внутри vbs-конфигурации `L_Misc.APIEvent`. В текущей версии используется стандартный журнал событий TDMS, но в последующих версиях реализация может быть изменена.
Для получения стандартных событий необходимо указать в фильтре идентификатор типа в формате `Тип стандартного события`@`Тип объекта`, где "тип стандартного события" соответствует одному из имени в перечислении TDMSEventType.
Каждое специальное событие имеет тип, идентифицируемый строкой длинной от 3 до 16 символов без пробелов, время наступления, необязательное краткое сообщение (до 1000 символов) и необязательный один прикрепленный объект TDMS
Для каждого стандартного события возвращается тип, идентифицируемый типом стандартного события, время наступления, описание события и обязательный один прикрепленный объект TDMS заданного типа

### Request:
```json
Content-Type: application/json
```
```json
{
  "Mode": "GetAPIEvents",
  "APIEventFilters": [
    {
      "From": "0001-01-01T00:00:00",
      "Type": "test_event_type"
    },
    {
      "From": "2022-07-26T12:57:39.255564+04:00",
      "Type": "tdmEventObjectCreate@OBJ_DEF1"
    }
  ]
}
```

Обязательные параметры:

|Parameter       |Type                                 |Description
|-               |-                                    |-               
|mode            |string                               |GetAPIEvents - вызываемый метод API
|APIEventFilters |[[JAPIEventFilter](#JAPIEventFilter)]|Задает фильтры для получения специальных событий

### Response

```
Status: 200
Content-Type: application/json
```

Возвращает коллекцию найденных событий в представлении json. Для каждого события возвращается: время события, тип события, сообщение и прикрепленный к событию объект
```json
[
  {
    "Time": "2022-07-22T06:44:41Z",
    "Type": "test_event_type",
    "Message": "123Big Blue Panda",
    "TObject": {
      "GUID": "{D4CD6ECC-4876-4837-B69E-F2C9F7757B2C}",
      "ObjectGuid": "{D4CD6ECC-4876-4837-B69E-F2C9F7757B2C}",
      "ObjectDefName": "O_NTB_FolderRoot"
    }
  },
  {
    "Time": "2022-07-22T06:45:52Z",
    "Type": "test_event_type",
    "Message": "123Big Blue Panda",
    "TObject": {
      "GUID": "{D4CD6ECC-4876-4837-B69E-F2C9F7757B2C}",
      "ObjectGuid": "{D4CD6ECC-4876-4837-B69E-F2C9F7757B2C}",
      "ObjectDefName": "O_NTB_FolderRoot"
    }
  },
  {
    "Time": "2022-07-26T13:30:28Z",
    "Type": "tdmEventObjectCreate",
    "Message": "Выдача задания 0203.009.П.0004.000.000.0000.000-КЗЭФ - Комплексное задание на разработку энергетической эффективности",
    "TObject": {
      "GUID": "{1CC87B0A-5311-42FF-BDA1-3789840E68B0}",
      "ObjectGuid": "{1CC87B0A-5311-42FF-BDA1-3789840E68B0}",
      "ObjectDefName": "O_PRC_Process"
    }
  },
  {
    "Time": "2022-07-26T13:32:34Z",
    "Type": "tdmEventObjectCreate",
    "Message": "Выдача задания 0203.009.П.0004.000.000.0000.000-КЗОФ-01 - Комплексное задание на опоры и фундаменты в зданиях",
    "TObject": {
      "GUID": "{27EA5ABA-2C2C-4DDC-A259-80632A67C012}",
      "ObjectGuid": "{27EA5ABA-2C2C-4DDC-A259-80632A67C012}",
      "ObjectDefName": "O_PRC_Process"
    }
  }
]
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |Не заданы фильтры для отбора событий
|404 NotFound  |Не задан ни один фильтр для отбора событий
|404 NotFound  |Не задан тип события в фильтре отбора событий
|404 NotFound  |Некорректный идентификатор типа события: {type}
|404 NotFound  |Некорректный идентификатор стандартного типа события: {etype}
|404 NotFound  |Некорректный идентификатор типа объекта: {otype}
|404 NotFound  |В конфигурации отсутствует выборка {Q_API_StdEvents}