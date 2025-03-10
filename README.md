﻿# Документация REST API TDMS
Документация REST  API для конфигураций, разработанных на базе [TDMS](https://tdms.ru) (Technical Data Management System) с использованная в приложении TDMS Application Server. 
Текущая поддерживаемые версии  
[`6.1.241.0`](https://ftp.csoft.ru/file_14367174086745c4f98a085)  
[`7.0.124`](https://ftp.csoft.ru/file_151873764678f698b5d3bc)

## Содержание
- [Развертывание](#Развертывание)
- [Обзор](#Обзор)
- [Авторизация](#Авторизация)
- [Постороение тела запросов](#Постороение-тела-запросов)
  - [Request body](#Request-body)
  - [TObject](#TObject)
  - [TObjectDef](#TObjectDef)
  - [TStatus](#TStatus)
  - [TFileDef](#TFileDef)
  - [TAttribute](#TAttribute)
  - [TShortObject](#TShortObject)
  - [TCondition](#TCondition)
  - [TVersion](#TVersion)
  - [TUser](#TUser)
  - [Parameters](#Parameters)
  - [APIEventFilters](#APIEventFilters)
- [Методы](#Методы)
  - [Получение объекта](#Getobject-post)
  - [Получение активной версии объекта](#Getobjectactiveversion-post)
  - [Создание объекта](#Createobject-post)
  - [Изменение объекта](#Editobject-post)
  - [Удаление объекта](#Deleteobject-post)
  - [Получение состава объекта](#Getobjectcontent-post)
  - [Получение результатов статичной выборки с параметрами](#Getquerysheet-post)
  - [Получение полного (сквозного) состава объекта](#Getobjectcontentall-post)
  - [Получение объектов, ссылающихся на указанный объект](#Getreferencedby-post)
  - [Получение типа объекта](#Getobjectdef-post)
  - [Получение версии объекта по описанию версии](#Getversionbydescription-post)
  - [Поиск объектов](#Findobjects-post)
  - [Создание версии](#Createversion-post)
  - [Удаление файлов](#Deletefiles-post)
  - [Создание ссылки на объект](#Createlink-post)
  - [Перемещение объекта](#Moveobject-post)
  - [Блокировка объекта](#Lockobject-post)
  - [Разблокировка объекта](#Unlockobject-post)
  - [Информация о правах доступа на объекте](#Getobjectpermissions-post)
  - [Информация о ролях на объекте](#Getobjectroles-post)
  - [Информация о файловом составе объекта](#Getobjectfiles-post)
  - [Получение файлов объекта](#Getobjectfilescontent-post)
  - [Отправка файлов в объект](#Addfiles-post)
  - [Получение статуса блокировки](#Getobjectlockstatus-post)
  - [Получение пользователя](#Getuser-post)
  - [Получение текущего пользователя](#Getcurrentuser-post)
  - [Создание пользователя](#Createuser-post)
  - [Изменение пользователя](#Edit-post)
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
Все запросы авторизуются с помощью `access_token`, который необходимо добавить в заголовки `headers`. 
```
Authorization: Bearer eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..etmTl5Im6-yxCVhjgP5Ajw.-iuDNbbDtq289WBAHg56PPlq4-dMqsx6YTOFoVbjNc1wHEHcXQwj-uDTr-KnnujSq8S8DBgMtxx7WCbFzwtpRYlr_VprBbiGt1IvcGvaHU0_paGFfl8hqp0sH-dOC_IjWoNq0JzibpXQrTBWDBDYbCyREY0dsHd4RRZhduG5qK_48MPx3VVv6UBeFH6KqS5kKWlkk0uek8LJaeRKxldDmJBC1mry99Epx8-E-mLHn2MBcGiplYTpqsU93B4fLQtfwqFc6siNSkwj6jP-xK8jgNPKBBdxNQUqlo_dzX4hC5odeD2ZZZ71CdrM7zRuJxXv3NRpx8EWQwSSVfue8H28Ez-wdpprEsJjhRZc6Fe-IAtLkCHEAo8bTCSbiSGwv-iWj9dzfCF2N8ZWObViTbRkDTElyEqQBnmWwwW8F2IFHgiKm-8OhJPjuzkpFXHxmyQKtn3cmpOHwVhG-ReK6sogoVNo4ipC7DoAUnUU_sLgw-h9ybtIaSenk8kmHQMTyD1lVP-HBM8vEVd3PZiKF-SFwvbTgbFfR1c3cpuFu1SRbUesN7xYZ_zI5OrqL8xQ3x0nGbN2U5n8gH_-uEMcGr6yT-d4rdgKuN3hiZ8YMTnZsoIFZcD8pUoi2aQVf4YLl9C4.93DwkSd8A2kYo1verZcV363GPwhBycjwDd9Pw2VAkws
```
Время жизни `access_token` по умолчанию 720 мин. Этот параметр может быть изменен в файле `Tdms.Config`
```xml
 <server AccessTokenExpireMinutes="720" RefreshTokenExpireMinutes="4320" ResourcesExpireMinutes="720"
```
### Авторизация Basic
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

>ВАЖНО: при перезагрузке приложения TDMS Application Server выданные токены становятся недействительными

#### Response
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

При формировании запросв необходимо полученный `access_token` поместить в заголовок

```
Authorization: Bearer eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..S9Sk0vs6lEzQzBQe1X2Icg.2vJCY_AlcPlh2fd17uLjUihTywOCcsyfOtrDTZvxQNlsgRCWq0gMbciuq8Ysx9WsILK4ag5ujBKK23lXAgfensKgjKqDNC545NPqiCufrfq-CdJOWxa953jg7pv93j8rTai0Wo3w4QglwJmcUjRKyML-x7O_YYKmMiQxmBtFRs_kqf6qZYTx27zh6EzfoljzIkplS8xRrpZbluKf0r8qx3F59Mm-O2VKkaXdPRI3WSjJOMgv4v5AX3TKwfGYeXqMnZ1K1KgK4lx1QfAVXkcxkEMN1NpblUnq0OexZhRVi5a3zhdWSj-y2pv5WlEIhbp9fdt1dxHYAOK7gJumEusLCoz42Un3021gBdhk7a5NvLBSeFetj2LmrxDDUx4u39gQ14aEVrqipftRNQKzq6zmcBF00WoYkiAgdpaO57fWAlKlAKkzNimNcUt2KRJWVekjo2O1jtSETZWdLcKJqkJLyFHuUxmCukYxQG8bjfTj3KI4AyE9k-GM-l8YYLjDglA-Fu3x8KvL0wZwG-KHw_9W30imKJ653z5KbbB8XZ7wmJ6Q8USegUk8wtpyYh5UFQXdsF8on6VXbt4oq3jSPS7zpKn27A9gDbnSSPbqV-p7lic_ze-JdacTkUMCnpE4MVbWUDuZ2OC-3RPkdY7sh_PZWA.5rYDn1c9hVtw5dYYFPmTdySjXxKJ9KlCepQ899fHDaE
```
### Доменная авторизация

Для получения `access_token` необходимо отправить запрос на endpoint `/token` с параметром `?authType=WIN`
```
http://your_host/token?authType=WIN
```
#### Request

```js
public object getAccessToken()
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://your_host/token?authType=WIN");
        request.Method = "POST";
        request.Accept = "/";
        request.ContentType = "text/plain";
        request.Headers.Add("WWW-Authenticate", "Negotiate");
        using (Stream requestStream = request.GetRequestStream())
        using (var writer = new StreamWriter(requestStream))
        {
            writer.WriteLine("grant_type=password&client_id=Web");
        }
        request.Credentials = CredentialCache.DefaultNetworkCredentials;
        WebResponse response = request.GetResponse();
        StreamReader sr = new StreamReader(response.GetResponseStream());
        string jsonString = sr.ReadToEnd();
        sr.Close();
        return StatusCode(StatusCodes.Status200OK, jsonString);
    }
```

#### Response
```json
{
  "$id": "1",
  "$type": "Tdms.Server.Security.TokensInfo, Tdms.Server.Host",
  "access_token": "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..zOXBMliLyX9A9LK3HOkj6g.Lx5MXGarG4JyRWw0zz7TIG3BQxwl9IYN0bjveRTz44M3WdzTqQClk1I6LLtJ5l4ack51aNtNElJyY36wXGS9VHqLZTb405mCUAtDjJDkb5K6o0TpGloIRGqNimxDtzZkjdI9xipML2tYi80z39PUNNjGnbZ9pWEYGNsTA4WVHxUtkDfuoxJevAbUR9Ers9yFn8HXzwNeA4B6NXtDOYFwkxjs6GkDCDFQKVzxwSATLzYZ0_Lr5-p7GjXhDgxa961epH5ZpMLYnUU3X16Ma6QRCOPf5hIfMzLHrbqYzWBmDiAPlsbCRfEg9x3Vbaya61J1oavKQwGUksYVNwIt5ft1ho-47UltY5iGoDqWnmsmmduiyVHE3mP35RYsekfYnUeGJK8Dqmu0ndV1BfY6dO6_9DsKJYfg2PfeFRq0wm-N2YHJvgtZGQd9w_kIjsUYSYTeOnHGgJbHQB1LEH_9PMz47V1lipiDTjUs8Xz_3zR7NVBrkBEhLAM1Khj-oAmP3xPnnYZIbJ99OG64qz6LFzv2Rqt1KypAwI4S45KgxZBLJ9PCjBA0rSEa6IsEfJIZV2rifLw1tUVDsMeABIkG5Sx-tUq_6hRRX7uwehIaVuurjOeyJRGqbTYEYWSwnzVQAW5Q.HNuYS-Ha0rxXsWDg3SDdVtczuTLOd_i_HZW9Xt4bETI",
  "username": "csoftkazan\\damir",
  "expires_in": 43260,
  "refresh_token": "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwidHlwIjoiSldUIn0..SGnm7bKoI4L6JKiGBdMYsw.eDaVvrFdsg-ZOgUWd881GeWyekHb4z02DDjjMHtGZBieGvM82n7IfF8HfGEWYX_40IWSA8JPqeJUP2cic6XYgSBWnqTkcYugW59VV_FO8O5keN_4Xke6KvFCYDeKNdP-bKXIZniq0Fx1pGsUcu0ZpMalrgGDr2pfq5PfZG4USMLYHNBQH8G4ONEBsGxcIxYcJkSKuZLxBv4nCyYJgAFMpz4IKGjRNWPFy4C54dBUqM7pOfl9HqGSFdLtjoQ95rWUH5nHL7P6zVedHK0nGmvLszkVKuClrY5NCKCvNe4clggfnEq_tneGg8q5XA2w_FEWinYEOks4kCFttb1eUaIdg6JHkVyM9o1Yg4DgsKmJdZbMppDZ8MrWO62zcVVSgZXeSfAtCwiZUwW6c3nkp0DMZuHf6XtK_wirI8-YYwKa5lXacwMGg2OPhkL0lhXih-yx0B1awfjEyR4ZZ4fMCABwLnzCk-clzSCS7npqgQbsTRdT3P8TxfstEU58mrX8662CovBdvlJAc2bLE2bDCicZwzICT98mCTYfFCOa78k1mRC7SuzJuGh1yUqOYK_mZUxuKKNgbkXHnHf9qtyhVT7LBord_HWp9hv4VRTsGLz99g0WlDiCiyWud4gdYYdzA4vHu47-HIanNwY-xlB7UsHN-8Lww5ljzjrTXTYd0OgBH0s.XtcfCzORH3kzzY5ugbsVf410fWQq27xpltB4-MZjOfk"
}
```

### Настройка CORS
Настройка CORS осуществляется в файле конфигруации сервера - tdms.config (в папке установки TDMS Application Server).
Возможность разрешить CORS-запрос на /token (TokenAllowAll - разрешить всё, TokenCorsHeaders - отдельные заголовки, TokenCorsOrigins - отдельные Origins) 

Если указана опция `TokenAllowAll = false`, но при этом `TokenCorsHeaders` или `TokenCorsOrigins` непустые, то `TokenCorsHeaders`, если непустое - то разрешает указанные headers, если непустое TokenCorsOrigins, то разрешает указанные origins

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <fileServer minFreeSpace="5000000000" serverType="storage" maxFilesInDirectory="10000">
    <devices>
      <add Path="c:\TFSS\Persistent" DeviceId="1" ReadOnly="False" DeviceType="persistent" />
      <add Path="c:\TFSS\Cache" DeviceId="2" ReadOnly="False" DeviceType="cache" />
      <add Path="c:\TFSS\Temp" DeviceId="3" ReadOnly="False" DeviceType="temp" />
      <add Path="c:\TFSS\Preview" DeviceId="4" ReadOnly="False" DeviceType="preview" />
    </devices>
  </fileServer>
  <server redirectHttpToHttps="True" tokenAllowAll="True" tokenCorsAllowCredentials="True" tokenCorsHeaders="*" AccessTokenExpireMinutes="721" workFolder="c:\TFSS" serverId="" https="False" certificateFile="" RefreshTokenExpireMinutes="4321" ResourcesExpireMinutes="720" host="" secureKey="" connectionString="Data Source=TDMS-SRV-VIRT\TDMSSERVER;Initial Catalog=gpp6;User ID=sa;Password=123q321;" certificatePassword="" port="444">
    <newblo />
    <modules>
      <add Enabled="True" Name="AppServer" Assembly="Tdms.Server.dll" />
      <add Enabled="True" Name="FileServer" Assembly="Tdms.FileServer.dll" />
      <add Enabled="True" Name="WebServer" Assembly="Tdms.Web.Server.dll" />
      <add Enabled="True" Name="Scripting" Assembly="Tdms.Scripting.dll" />
      <add Enabled="True" Name="Preview" Assembly="extensions\Tdms.Preview\Tdms.Preview.dll" />
      <add Enabled="True" Name="TDMSAPI" Assembly="Extensions\TDMSAPI\TDMSAPI.dll" />
    </modules>
  </server>
  <log level="Debug" MaxLogFileSize="512000000" />
</configuration>
```

## Построение тела запроса

### Request body
Ниже представлено тело `POST` запроса. В параметре `Mode` необходимо указать имя вызываемого метода

```json
{
    "Mode": "",
    "TObject": {},
    "TObjects": [],
    "TConditions": [],
    "TUser": {},
    "Submode": "",
    "Parameters": [],
    "APIEventFilters": [],
    "TFlag": ""
}
```

|Parameter      |Type                               |Description
|-              |-                                  |-               
|Mode           |string                             |Вызываемый метод API
|TObject        |[TObject](#TObject)                |Описание объекта TDMS
|TObjects       |[[TObject](#TObject)]              |Коллекция объектов TDMS
|TConditions    |[[TObject](#TObject)]              |Коллекция условия в выборке
|TUser          |[TUser](#TUser)                    |Описание пользователя TDMS
|Submode        |string                             |Задает именованные параметры Submode
|Parameters     |[JParam](#JParam)                  |Задает имя пользовательского метода в конфигурации, который необходимо выпонить
|APIEventFilters|[JAPIEventFilter](#JAPIEventFilter)|Задает фильтры для получения специальных событий

### `TObject`
Иллюстрирует информационный объект TDMS. С его помощью передаются свойства, атрибуты, версии и состояния последнего. Также используется в ответах на запросы

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
    "TAttributes":[
        {
            "SysName": "A_Int_DocVersion",
            "Value":2,
            "Type": "tdmString"
        },
        {
            "SysName": "A_Real_SheetLength",
            "Value":1.22,
            "Type": "tdmReal"
        },
        {
            "SysName": "A_User_Author",
            "Value":"USER_ABA3440A_0B0C_40AD_A329_D85B5FBABFFE",
            "Type": "tdmUserLink"
        },
        {
            "SysName": "A_Str_Answer",
            "Value":"Some answer", 
            "Type": "tdmString"
        },
        {
            "SysName": "A_Str_Designation",
            "Value":"777",
            "Type": "tdmString"
        },
        {
            "SysName": "A_Ref_Project",
            "Value":"{F164E1BB-A204-4996-9A2D-799B65B0D15E}",
            "Type": "tdmObjectLink"
        }
    ],
     "TVersions": [
        {
          "VersionDescription": "По замечаниям экспертизы",
          "VersionName": "10",
          "GUID": "{48A0871D-1BD2-4F72-B04B-C6185818A411}"
        },
        {
          "VersionDescription": "РИ \u2116254-21 от 12.10.2021",
          "VersionName": "Изм.8",
          "GUID": "{1A0DAEC2-60C5-4409-89F9-7D81381BB423}"
        },
        {
          "VersionDescription": "Согласно требованиям от 29.08",
          "VersionName": "1",
          "GUID": "{23DEDAC8-A351-438D-A02A-FB5CF2B50C03}"
        }
    ]
}
```

|Parameter         |Type                       |Read only|Description
|-                 |-                          |-        |-               
|GUID              |string                     |true     |Глобально уникальный идентификатор объекта (GUID)
|ObjectGuid        |string                     |true     |Единый guid на все версии одного объекта. В случае классической версионности он совпадает с GUID для активной версии. В случае новой версионности он совпадает с первой версией
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
|TAttributes       |[[TAttribute](#TAttribute)]|true     |Коллеция атрибутов объекта TDMS
|TVersions         |[[TVersion](#TAttribute)]  |true     |Коллеция версий объекта TDMS
|TContent          |[[TVersion](#TAttribute)]  |true     |Состав объекта TDMS

### `TObjectDef`
Описание типа объекта TDMS

```json
{
  "FileDefs": [
    {
      "Description": "Текстовый документ",
      "Sysname": "FILE_DOC",
      "Extensions": "*.doc, *.docx, *.rtf, *.odt, *.txt"
    },
    {
      "Description": "Электронная таблица",
      "Sysname": "FILE_XLS",
      "Extensions": "*.xls, *.xlsx, *.csv, *.ods"
    },
    {
      "Description": "Публикация",
      "Sysname": "FILE_PDF_PUBL",
      "Extensions": "*.pdf"
    },
    {
      "Description": "Документ Adobe Acrobat",
      "Sysname": "FILE_AA_PDF",
      "Extensions": "*.pdf"
    },
    {
      "Description": "Подлинник",
      "Sysname": "FILE_PDF_ORIG",
      "Extensions": "*.pdf, tif, *.tiff"
    },
    {
      "Description": "Исходные данные для расчета",
      "Sysname": "FILE_DLG",
      "Extensions": "*.dlg, *.dlf"
    },
    {
      "Description": "Файл ЭЦП",
      "Sysname": "FILE_SIG",
      "Extensions": "*.sig, *.sgn"
    },
    {
      "Description": "Чертеж CAD",
      "Sysname": "FILE_DWG",
      "Extensions": "*.dwg, *.dxf, *.dwf, *.dgn"
    }
  ],
  "Statuses": [
    {
      "Description": "Недействующий (Документ)",
      "SysName": "S_Doc_Cancel"
    },
    {
      "Description": "В разработке (Документ)",
      "SysName": "S_Doc_Developing"
    },
    {
      "Description": "Разработан (Документ)",
      "SysName": "S_Doc_Developed"
    },
    {
      "Description": "На согласовании (Документ)",
      "SysName": "S_Doc_Agreement"
    },
    {
      "Description": "Проверка в архиве (Документ)",
      "SysName": "S_Doc_ArchCheck"
    },
    {
      "Description": "Действующий (Документ)",
      "SysName": "S_Doc_Archived"
    },
    {
      "Description": "Действующий на изменении (Документ)",
      "SysName": "S_Doc_Changing"
    },
    {
      "Description": "Аннулирован (Документ)",
      "SysName": "S_Doc_Annulled"
    }
  ]
}
```

|Parameter|Type                   |Description
|-        |-                      |-               
|Statuses |[[TStatus](#TStatus)]  |Коллекция возможных статусов объектов данного типа
|FileDefs |[[TFileDef](#TFileDef)]|Типы файлов, разрешенные к добавлению к объектам этого типа

### `TStatus`
Схема описания статуса объекта

```json
{
    "Description": "Недействующий (Документ)",
    "SysName": "S_Doc_Cancel"
}
```

|Parameter  |Type  |Description
|-          |-     |-               
|Description|string|Описание статуса
|SysName    |string|Системное имя статуса

### `TFileDef`
Тип файла TDMS

```json
{
    "Description": "Текстовый документ",
    "Sysname": "FILE_DOC",
    "Extensions": "*.doc, *.docx, *.rtf, *.odt, *.txt"
}
```

|Parameter  |Type  |Description
|-          |-     |-               
|Description|string|Описание типа файла
|SysName    |string|Системное имя типа файла
|Extensions |string|Возможные расширения файлов данного типа

### `TFile`
Описание файла TDMS

```json
{
    "FileDefName": "FILE_PDF_PUBL",
    "FileName": "Отчет Модуль TDMS API.pdf"
}
```

|Parameter  |Type  |Description
|-          |-     |-               
|FileDefName|string|Описание типа файла
|FileName    |string|Системное имя типа файла

### `TAttribute`
Атрибут объекта TDMS. Как правило входит в коллекцию атрибутов `TAttributes`

```json
{
    "Sysname": "ATTR_NAME",
    "Value": "Сооружение жилого типа",
    "Type": "tdmString"
}
```

|Parameter|Type  |Description
|-        |-     |-               
|Sysname  |string|Cистемное имя атрибута
|Value    |string|Значение атрибута
|Type     |string|Тип атрибута

### `TShortObject`
Короткое описание объекта TDMS. Используется, как правило, для передачи коллекции объектов

```json
{
    "Description": "Структура комплекса",
    "GUID": "{28ECF8F2-0124-41EA-B37F-CED9190146CC}",
    "ObjectDefName": "O_Folder_Complex_Structure",
    "StatusNme": "S_Doc_Annulled"
}
```

|Parameter    |Type  |Description
|-            |-     |-               
|Description  |string|Описание объекта
|GUID         |string|Идентификатор объекта
|ObjectDefName|string|Тип объекта
|StatusName   |string|Системное имя текущего статуса

### `TCondition`
Условие поиска (применяется в методе поиска объектов [Findobjects](#Findobjects))

```json
[
    {
        "Type": "ObjectDef",
        "Value": "'O_Building' or 'O_Part'"
    },
    {
        "Type": "Attribute",
        "SysName": "A_Str_Designation",
        "Value": "'0203.КТО.001.4701- 4799.007' or '0203.КТО.001'"
    }
]
```

|Parameter|Type  |Description
|-        |-     |-               
|Type     |string|Тип условия принимает значения: ObjectDef - для указания типа объекта, Attribute - для указания атрибута объекта
|SysName  |string|Системное имя атрибута
|Value    |string|Значение условия поиска

### `TVersion`
Версия объекта TDMS. Как правило входит в коллекцию версий `TVersions`

```json
{
    "VersionDescription": "Согласно замечаниям экспертизы",
    "VersionName": 2",
    "GUID": "{91C40073-340A-43B4-9EC1-43A3A9BB8512}"
}
```

|Parameter         |Type  |Description
|-                 |-     |-               
|VersionDescription|string|Cистемное имя атрибута
|VersionName       |string|Значение атрибута
|GUID              |string|Идентификатор версии. При условии, что на типе объекта включено свойство "Ссылки указывают на версию". В этом случае у всех версий существует уникальный для каждой версии GUID наряду с общим GUID

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

## Методы

### Getobject `POST`
Получение информации об объекте TDMS по GUID. Возвращаются свойства, атрибуты, описание файлового состава объекта

#### Request:

```json
{
    "Mode": "Getobject",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|Mode        |string             |Getobject - вызываемый метод API
|TObject     |[TObject](#TObject)|Объект TDMS
|TObject.GUID|string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "GUID": "{491544D8-8491-4098-A399-5FA4ABC27F22}",
  "ObjectGuid": "{297FCA5B-79B4-4240-9C54-9D22654C7EAE}",
  "ObjectDefName": "O_Set",
  "Description": "0203.001.Р.1/2.0001.ГГРС.001.1102.12-ЭС изм.5",
  "Parent": "{83CF7B6D-E11A-47D9-A4B7-5B316AF4033C}",
  "StatusName": "S_Volume_Annulled",
  "ModifyTime": "03.10.2022 10:04:27",
  "ModifyUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": "Сергей",
    "LastName": "Капаров",
    "MiddleName": "Михайлович",
    "Login": "sysadmin",
    "Password": null,
    "Phone": "",
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "ActiveVersion": true,
  "VersionDescription": "",
  "VersionName": "1",
  "VersionCreateTime": "22.02.2022 14:21:17",
  "VersionCreateUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": "Сергей",
    "LastName": "Капаров",
    "MiddleName": "Михайлович",
    "Login": "sysadmin",
    "Password": null,
    "Phone": "",
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "TAttributes": [
    {
      "SysName": "A_Str_Designation",
      "Value": "0203.001.Р.1/2.0001.ГГРС.001.1102.12-ЭС",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Name",
      "Value": "ЮЕГ. РАСШИРЕНИЕ ЕСГ ДЛЯ ОБЕСПЕЧЕНИЯ ПОДАЧИ ГАЗА В ГАЗОПРОВОД «ЮЖНЫЙ ПОТОК». Газопроводы газораспределительных систем. Газораспределительная станция. Электроснабжение",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_User_Author",
      "Value": "USER_GMV",
      "Type": "tdmUserLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Note",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Complex",
      "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_PagesCount",
      "Value": 10,
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_A4Count",
      "Value": "",
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_ShFormats",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Table_Formats",
      "Value": null,
      "Type": "tdmTable",
      "TTableRows": []
    },
    {
      "SysName": "A_Ref_Parent",
      "Value": "{83CF7B6D-E11A-47D9-A4B7-5B316AF4033C}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_OrderChange",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_ChangeNum",
      "Value": 5,
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_List_Change",
      "Value": "",
      "Type": "tdmList",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_AcceptEA",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_AcceptTA",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_Begin",
      "Value": "01.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_End",
      "Value": "21.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Barcode",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Cls_DSP",
      "Value": "",
      "Type": "tdmList",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_InvNum",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_InvNum_SubPodr",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_SubDesignation",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Part",
      "Value": "{83C58C7B-6553-4678-976E-A51EDBC2F7AE}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Build",
      "Value": "{8BD91B25-748A-4CDF-A3FE-A1D840A97E83}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Organization",
      "Value": "{E17BC391-8108-4DCD-A06C-6E3F6406CF2A}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Contract",
      "Value": "{696D7F3D-8B71-441C-921C-5C2E629819B9}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_User_GIP",
      "Value": "USER_F6798DB1_3AB3_471A_A9E8_C6CEC2B49748",
      "Type": "tdmUserLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_SubContract",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Table_Predecessors",
      "Value": null,
      "Type": "tdmTable",
      "TTableRows": []
    },
    {
      "SysName": "A_Ref_Mark",
      "Value": "{D1019109-4AFB-4B03-81DC-9EE68E97C0E9}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_MarkNum",
      "Value": "",
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Phase",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Bool_Sync",
      "Value": "",
      "Type": "tdmBool",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Project",
      "Value": "{DEBDC3FF-E9E0-4F29-A948-E0F6FB7306AC}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Building_Stage",
      "Value": "1",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Building_SubStage",
      "Value": "2",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_sCodeMark",
      "Value": "ЭС",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Stage",
      "Value": "{D3B27616-5A99-44E9-8AF6-CFC9E1C7BA41}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Dept",
      "Value": "{A93A7BCE-A69C-48C9-ACD6-7797EA225132}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Dat_NTB_DocImplDate",
      "Value": "22.08.2022 14:30:11",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Designation_Customer",
      "Value": "ЙЙЙЙ",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_Begin_Fact",
      "Value": "01.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_End_Fact",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Package_Unload",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    }
  ],
  "TVersions": [
    {
      "VersionDescription": "",
      "VersionName": "1",
      "GUID": "{491544D8-8491-4098-A399-5FA4ABC27F22}"
    },
    {
      "VersionDescription": "РИ №246-21 от 12.10.2021",
      "VersionName": "Изм.4",
      "GUID": "{816C6166-B562-4EEC-BDFF-38ABAC81EB79}"
    }
  ],
  "TContent": null,
  "TFiles": null
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getobjectactiveversion `POST`
Возвращает активную версию объекта TDMS по GUID любой прежней версии. Возвращаются свойства, атрибуты, описание файлового состава объекта

#### Request:

```json
{
    "Mode": "Getobjectactiveversion",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|Mode        |string             |Getobject - вызываемый метод API
|TObject     |[TObject](#TObject)|Объект TDMS
|TObject.GUID|string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "GUID": "{491544D8-8491-4098-A399-5FA4ABC27F22}",
  "ObjectGuid": "{297FCA5B-79B4-4240-9C54-9D22654C7EAE}",
  "ObjectDefName": "O_Set",
  "Description": "0203.001.Р.1/2.0001.ГГРС.001.1102.12-ЭС изм.5",
  "Parent": "{83CF7B6D-E11A-47D9-A4B7-5B316AF4033C}",
  "StatusName": "S_Volume_Annulled",
  "ModifyTime": "03.10.2022 10:04:27",
  "ModifyUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": "Сергей",
    "LastName": "Капаров",
    "MiddleName": "Михайлович",
    "Login": "sysadmin",
    "Password": null,
    "Phone": "",
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "ActiveVersion": true,
  "VersionDescription": "",
  "VersionName": "1",
  "VersionCreateTime": "22.02.2022 14:21:17",
  "VersionCreateUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": "Сергей",
    "LastName": "Капаров",
    "MiddleName": "Михайлович",
    "Login": "sysadmin",
    "Password": null,
    "Phone": "",
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "TAttributes": [
    {
      "SysName": "A_Str_Designation",
      "Value": "0203.001.Р.1/2.0001.ГГРС.001.1102.12-ЭС",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Name",
      "Value": "ЮЕГ. РАСШИРЕНИЕ ЕСГ ДЛЯ ОБЕСПЕЧЕНИЯ ПОДАЧИ ГАЗА В ГАЗОПРОВОД «ЮЖНЫЙ ПОТОК». Газопроводы газораспределительных систем. Газораспределительная станция. Электроснабжение",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_User_Author",
      "Value": "USER_GMV",
      "Type": "tdmUserLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Note",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Complex",
      "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_PagesCount",
      "Value": 10,
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_A4Count",
      "Value": "",
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_ShFormats",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Table_Formats",
      "Value": null,
      "Type": "tdmTable",
      "TTableRows": []
    },
    {
      "SysName": "A_Ref_Parent",
      "Value": "{83CF7B6D-E11A-47D9-A4B7-5B316AF4033C}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_OrderChange",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_ChangeNum",
      "Value": 5,
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_List_Change",
      "Value": "",
      "Type": "tdmList",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_AcceptEA",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_AcceptTA",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_Begin",
      "Value": "01.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_End",
      "Value": "21.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Barcode",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Cls_DSP",
      "Value": "",
      "Type": "tdmList",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_InvNum",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_InvNum_SubPodr",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_SubDesignation",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Part",
      "Value": "{83C58C7B-6553-4678-976E-A51EDBC2F7AE}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Build",
      "Value": "{8BD91B25-748A-4CDF-A3FE-A1D840A97E83}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Organization",
      "Value": "{E17BC391-8108-4DCD-A06C-6E3F6406CF2A}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Contract",
      "Value": "{696D7F3D-8B71-441C-921C-5C2E629819B9}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_User_GIP",
      "Value": "USER_F6798DB1_3AB3_471A_A9E8_C6CEC2B49748",
      "Type": "tdmUserLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_SubContract",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Table_Predecessors",
      "Value": null,
      "Type": "tdmTable",
      "TTableRows": []
    },
    {
      "SysName": "A_Ref_Mark",
      "Value": "{D1019109-4AFB-4B03-81DC-9EE68E97C0E9}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_MarkNum",
      "Value": "",
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Phase",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Bool_Sync",
      "Value": "",
      "Type": "tdmBool",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Project",
      "Value": "{DEBDC3FF-E9E0-4F29-A948-E0F6FB7306AC}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Building_Stage",
      "Value": "1",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Building_SubStage",
      "Value": "2",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_sCodeMark",
      "Value": "ЭС",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Stage",
      "Value": "{D3B27616-5A99-44E9-8AF6-CFC9E1C7BA41}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Dept",
      "Value": "{A93A7BCE-A69C-48C9-ACD6-7797EA225132}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Dat_NTB_DocImplDate",
      "Value": "22.08.2022 14:30:11",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Designation_Customer",
      "Value": "ЙЙЙЙ",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_Begin_Fact",
      "Value": "01.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_End_Fact",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Package_Unload",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    }
  ],
  "TVersions": [
    {
      "VersionDescription": "",
      "VersionName": "1",
      "GUID": "{491544D8-8491-4098-A399-5FA4ABC27F22}"
    },
    {
      "VersionDescription": "РИ №246-21 от 12.10.2021",
      "VersionName": "Изм.4",
      "GUID": "{816C6166-B562-4EEC-BDFF-38ABAC81EB79}"
    }
  ],
  "TContent": null,
  "TFiles": null
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Createobject `POST`
Создание объекта TDMS. Объект может быть создан как в составе указанного родителя `parent`, так и не имея родителя. Не все свойства объекта TDMS могут быть изменены

#### Request:
```
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
        "TAttributes":[
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

#### Response

```
Status: 200
Content-Type: application/json
```

Тело ответа:

```json
{
  "GUID": "{586C55CA-DDAB-4575-91E3-ADCE5B86D03C}",
  "ObjectGuid": "{586C55CA-DDAB-4575-91E3-ADCE5B86D03C}",
  "ObjectDefName": "O_PDoc",
  "Description": "Hello",
  "Parent": "{48A0871D-1BD2-4F72-B04B-C6185818A411}",
  "StatusName": "S_Doc_Developing",
  "ModifyTime": "21.09.2022 15:37:26",
  "ModifyUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": null,
    "LastName": null,
    "MiddleName": null,
    "Login": "SYSADMIN",
    "Password": null,
    "Phone": null,
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "ActiveVersion": true,
  "VersionDescription": "Начальная версия",
  "VersionName": "0",
  "VersionCreateTime": "21.09.2022 15:37:26",
  "VersionCreateUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": null,
    "LastName": null,
    "MiddleName": null,
    "Login": "SYSADMIN",
    "Password": null,
    "Phone": null,
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "TAttributes": [
    {
      "SysName": "A_Int_AttachDocNum",
      "Value": "",
      "Type": "tdmInteger"
    },
    {
      "SysName": "A_Cls_TypeDoc",
      "Value": "",
      "Type": "tdmClassifier"
    },
    {
      "SysName": "A_Int_ChangeNum",
      "Value": 13,
      "Type": "tdmInteger"
    },
    {
      "SysName": "A_List_Change",
      "Value": "",
      "Type": "tdmList"
    },
    {
      "SysName": "A_Date_AcceptEA",
      "Value": "",
      "Type": "tdmDate"
    },
    {
      "SysName": "A_Date_AcceptTA",
      "Value": "",
      "Type": "tdmDate"
    },
    {
      "SysName": "A_Date_Begin",
      "Value": "",
      "Type": "tdmDate"
    },
    {
      "SysName": "A_Date_End",
      "Value": "",
      "Type": "tdmDate"
    },
    {
      "SysName": "A_Ref_TypeAttachDoc",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Str_Designation",
      "Value": "",
      "Type": "tdmString"
    },
    {
      "SysName": "A_Str_Name",
      "Value": "Test object",
      "Type": "tdmString"
    },
    {
      "SysName": "A_User_Author",
      "Value": "",
      "Type": "tdmUserLink"
    },
    {
      "SysName": "A_Str_Note",
      "Value": "A_Str_Note answersss",
      "Type": "tdmString"
    },
    {
      "SysName": "A_Str_PagesNum",
      "Value": "",
      "Type": "tdmString"
    },
    {
      "SysName": "A_Int_PagesCount",
      "Value": "",
      "Type": "tdmInteger"
    },
    {
      "SysName": "A_Int_A4Count",
      "Value": "",
      "Type": "tdmInteger"
    },
    {
      "SysName": "A_Table_Formats",
      "Value": "",
      "Type": "tdmTable"
    },
    {
      "SysName": "A_Ref_Project",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Table_OrderChanges",
      "Value": "",
      "Type": "tdmTable"
    },
    {
      "SysName": "A_Ref_Changes_Set",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Ref_OrderChange",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Ref_Parent",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Str_Barcode",
      "Value": "",
      "Type": "tdmString"
    },
    {
      "SysName": "A_Ref_Organization",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Str_ShFormats",
      "Value": "",
      "Type": "tdmString"
    },
    {
      "SysName": "A_Ref_Dept",
      "Value": "",
      "Type": "tdmObjectLink"
    },
    {
      "SysName": "A_Str_Designation_Customer",
      "Value": "",
      "Type": "tdmString"
    },
    {
      "SysName": "A_Date_Begin_Fact",
      "Value": "",
      "Type": "tdmDate"
    },
    {
      "SysName": "A_Date_End_Fact",
      "Value": "",
      "Type": "tdmDate"
    }
  ],
  "TVersions": [
    {
      "VersionDescription": "Начальная версия",
      "VersionName": "0",
      "GUID": "{586C55CA-DDAB-4575-91E3-ADCE5B86D03C}"
    }
  ],
  "TContent": null
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Editobject `POST`
Изменение объекта TDMS. Могут быть изменены свойства, атрибуты и состояния объекта TDMS. Перечень изменяемых параметров описыватся объектом [TObject](#TObject)

#### Request:

```json
{
    "mode": "Editobject",
    "TObject": {
        "ObjectDefName": "O_DocClaim",
        "Description": "Новое описание",
        "GUID": "{5689EAE5-41EF-4D5A-9BF7-487640B5E873}",
        "StatusName": "S_DocClaim_Actual",
        "TAttributes":[
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
|TObject     |[TObject](#TObject)|Объект TDMS
|TObject.GUID|string             |Идентификатор объекта TDMS

#### Response

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

### Deleteobject `POST`
Удаление объекта. Для удаления объекта из состава в параметре `TFlag` необходимо указать `TFlag = true`, для удаления из системы необходимо указать `TFlag = false` либо не указывать парамеетр TFlag совсем. 

Для удаления объекта из состава родительского объекта последний должен быть указан в [TObject](#TObject)

#### Request:

```json
{
    "Mode": "Deleteobject",
    "TFlag": false,
    "TObject": {
        "GUID": "{9BD08FB0-E1A5-4DA6-9C5B-BCD8C195ADCA}",
        "Parent": "{3299618F-4B50-4F98-AAA4-7B7523100666}"
    }
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS
|TFlag         |bool               |Дополнительный параметр, указующий на способ удаления "из системы" или "из состава"
|TObject.GUID  |string             |Идентификатор объекта TDMS
|TObject.Parent|string             |Родительский объект TDMS, из состава которого необходимо удалить объект

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

```
Объект успешно удален из системы
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getobjectcontent `POST`
Получение состава объекта TDMS по GUID указанном в [TObject](#TObject).
При опциональном указании параметра `TFlag = true` можно получить расширенное описание объектов [TObject](#TObject) или короткое [TShortObject](#TShortObject) при опциональном указании параметра `TFlag = false` или его отсутствии

#### Request:

```json
{
    "Mode": "Getobjectcontent",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    },
    "TFlag":true
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|Mode        |string             |Вызываемый метод API
|TObject     |[TObject](#TObject)|Объект TDMS
|TObject.GUID|string             |Идентификатор объекта TDMS
|TFlag       |boolean            |Ключ определяющий подробный или сжатый формат результата

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "TContent": [
    {
      "Description": "Структура комплекса",
      "GUID": "{28ECF8F2-0124-41EA-B37F-CED9190146CC}",
      "ObjectDefName": "O_Folder_Complex_Structure"
    },
    {
      "Description": "Общая информация",
      "GUID": "{B05A7F23-5597-45DD-A7E8-C8CAE3B28149}",
      "ObjectDefName": "O_Folder_Doc"
    },
    {
      "Description": "0203.001.001 - 1-Й ЭТАП (ВОСТОЧНЫЙ КОРИДОР), ДЛЯ ОБЕСПЕЧЕНИЯ ПОДАЧИ ГАЗА В ОБЪЕМЕ ДО 63 МЛРД.М3/ГОД Этап 2.1. Линейная часть. Участок «Починки-Анапа», км 0 –км 347,5 (км 0 – км 181, км 181 – км 295,7, км 295,7 – км 347,5)",
      "GUID": "{DEBDC3FF-E9E0-4F29-A948-E0F6FB7306AC}",
      "ObjectDefName": "O_Project"
    }
  ]
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getquerysheet `POST`
Получение результатов статичной выборки с параметрами [Parameters](#Parameters) в виде таблицы, возвращаемой выборкой

#### Request:
```json
{
    "mode": "Getquerysheet",
    "submode": "Q_1",
    "Parameters": [
        {
            "Name": "objDefName",
            "Value": "O_Volume"
        },
        {
            "Name": "refCompelx",
            "Value": "{A6A3A016-F2EE-46C9-BC77-5070E8B52185}"
        }
    ]
}
```

Обязательные параметры:

|Parameter  |Type                     |Description
|-          |-                        |-               
|Mode       |string                   |Вызываемый метод API
|submode    |string                   |Идентификатор вызываемой выборки
|Parameters |[Parameters](#Parameters)|Объект TDMS
|Param.Name |string                   |Ключ представляет собой наименование колонки в результирующей таблице выборки
|Param.Value|string                   |Значение ячейки результирующей таблицы выборки

#### Response

```
Status: 200
Content-Type: application/json
```

```json
[
    {
        "Описание": "Рабочая документация. Этап 1/1",
        "Проект": "0203.012 - Выполнение сбора исходных данных и комплексных инженерных изысканий по объекту \"Расширение ЕСГ для обеспечения подачи газа в газопровод \"Южный поток\" Этап 2.5",
        "Дата создания": "03.02.2022 6:53:12",
        "Код проекта": "0203.012"
    },
    {
        "Описание": "Рабочая документация. Этап 0/0",
        "Проект": "0203.008 - Разработка рабочей документации по объекту \"Расширение ЕСГ для обеспечения подачи газа в газопровод \"Южный поток\"",
        "Дата создания": "02.03.2022 7:50:38",
        "Код проекта": "0203.008"
    },
    {
        "Описание": "Рабочая документация. Этап 1/2 (2125.001.Р.1/2.0001)",
        "Проект": "2125.001 - Выполнение проектно-изыскательских работ по объекту «Реконструкция склада ГСМ Пурпейской БМТС»",
        "Дата создания": "17.05.2022 11:10:48",
        "Код проекта": "2125.001"
    },
    {
        "Описание": "Рабочая документация",
        "Проект": "0203.009 - Выполнение проектно-изыскательских работ по объекту \"Расширение ЕСГ для обеспечения подачи газа в газопровод \"Южный поток\"",
        "Дата создания": "30.06.2022 9:26:15",
        "Код проекта": "0203.009"
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

### Getqueryobjects `POST`
Получение результатов статичной выборки с параметрами [Parameters](#Parameters) в виде списка объектов TDMS

#### Request:

```json
{
    "mode": "Getqueryobjects",
    "submode": "Q_1",
    "tflag": false,
    "Parameters": [
        {
            "Name": "objDefName",
            "Value": "O_Volume"
        },
        {
            "Name": "refCompelx",
            "Value": "{A6A3A016-F2EE-46C9-BC77-5070E8B52185}"
        }
    ]
}
```

Обязательные параметры:

|Parameter  |Type                     |Description
|-          |-                        |-               
|Mode       |string                   |Вызываемый метод API
|submode    |string                   |Идентификатор вызываемой выборки
|TFlag      |boolean                  |Ключ определяющий подробный или сжатый формат результата
|Parameters |[Parameters](#Parameters)|Объект TDMS
|Param.Name |string                   |Ключ представляет собой наименование колонки в результирующей таблице выборки
|Param.Value|string                   |Значение ячейки результирующей таблицы выборки

#### Response

```
Status: 200
Content-Type: application/json
```

```json
[
    {
        "GUID": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}",
        "ObjectGuid": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}",
        "ObjectDefName": "O_Folder_Doc",
        "Description": "КСГ",
        "Parent": "{E6237962-1447-4524-ACBB-01A58502C780}",
        "StatusName": "S_Created",
        "ModifyTime": "25.05.2023 11:15:13",
        "ModifyUser": {
            "SysName": "USER_AdminGPP_SPb",
            "Description": "Администратор СПб",
            "FirstName": "Санкт-Питербург",
            "LastName": "Администратор",
            "MiddleName": "",
            "Login": "AdminGPP_SPb",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": {
                "Code": "0001.009",
                "Description": "Служба бухгалтерского учета и отчетности",
                "SysName": "N_X_104E1346-437C-11E6-4A88-001A4A160153"
            },
            "PositionClassifier": null
        },
        "ActiveVersion": true,
        "VersionDescription": "Начальная версия",
        "VersionName": "1",
        "VersionCreateTime": "02.02.2022 8:14:36",
        "VersionCreateUser": {
            "SysName": "USER_GMV",
            "Description": "Гуляев Максим Вячеславович",
            "FirstName": "Максим",
            "LastName": "Гуляев",
            "MiddleName": "Вячеславович",
            "Login": "gmv",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": null,
            "PositionClassifier": null
        },
        "TAttributes": [
            {
                "SysName": "A_Str_Name",
                "Value": "КСГ",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Str_Note",
                "Value": "",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Complex",
                "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Parent",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Cls_TypeFolder",
                "Value": "N_TYPE_FOLDER_PRODUCT_KSG",
                "Type": "tdmClassifier",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Project",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            }
        ],
        "TVersions": [
            {
                "VersionDescription": "Начальная версия",
                "VersionName": "1",
                "GUID": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}"
            }
        ],
        "TContent": null,
        "TFiles": null
    },
    {
        "GUID": "{18AAC5A8-1BE5-4A34-9FC2-4BEBE24A5D61}",
        "ObjectGuid": "{18AAC5A8-1BE5-4A34-9FC2-4BEBE24A5D61}",
        "ObjectDefName": "O_Folder_Doc",
        "Description": "Дело проекта",
        "Parent": "{E6237962-1447-4524-ACBB-01A58502C780}",
        "StatusName": "S_Created",
        "ModifyTime": "25.05.2023 11:15:13",
        "ModifyUser": {
            "SysName": "USER_AdminGPP_SPb",
            "Description": "Администратор СПб",
            "FirstName": "Санкт-Питербург",
            "LastName": "Администратор",
            "MiddleName": "",
            "Login": "AdminGPP_SPb",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": {
                "Code": "0001.009",
                "Description": "Служба бухгалтерского учета и отчетности",
                "SysName": "N_X_104E1346-437C-11E6-4A88-001A4A160153"
            },
            "PositionClassifier": null
        },
        "ActiveVersion": true,
        "VersionDescription": "Начальная версия",
        "VersionName": "1",
        "VersionCreateTime": "02.02.2022 8:14:36",
        "VersionCreateUser": {
            "SysName": "USER_GMV",
            "Description": "Гуляев Максим Вячеславович",
            "FirstName": "Максим",
            "LastName": "Гуляев",
            "MiddleName": "Вячеславович",
            "Login": "gmv",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": null,
            "PositionClassifier": null
        },
        "TAttributes": [
            {
                "SysName": "A_Str_Name",
                "Value": "Дело проекта",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Str_Note",
                "Value": "",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Complex",
                "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Parent",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Cls_TypeFolder",
                "Value": "N_TYPE_FOLDER_PRODUCT_ProjectCase",
                "Type": "tdmClassifier",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Project",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            }
        ],
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Getobjectcontentall `POST`
Получение полного (сквозного) состава объекта TDMS по GUID указанном в [TObject](#TObject).
При опциональном указании параметра `TFlag = true` можно получить расширенное описание объектов [TObject](#TObject) или короткое [TShortObject](#TShortObject) при опциональном указании параметра `TFlag = false` или его отсутствии

#### Request:

```json
{
    "mode": "Getobjectcontentall",
    "TObject": {
        "GUID": "{E6237962-1447-4524-ACBB-01A58502C780}"
    },
    "TFlag":true
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|Mode        |string             |Вызываемый метод API
|TObject     |[TObject](#TObject)|Объект TDMS
|TObject.GUID|string             |Идентификатор объекта TDMS
|TFlag       |boolean            |Ключ определяющий подробный или сжатый формат результата

#### Response

```
Status: 200
Content-Type: application/json
```

При указании расширенного формата возвращаемой коллекции:

```json
[
    {
        "GUID": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}",
        "ObjectGuid": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}",
        "ObjectDefName": "O_Folder_Doc",
        "Description": "КСГ",
        "Parent": "{E6237962-1447-4524-ACBB-01A58502C780}",
        "StatusName": "S_Created",
        "ModifyTime": "25.05.2023 11:15:13",
        "ModifyUser": {
            "SysName": "USER_AdminGPP_SPb",
            "Description": "Администратор СПб",
            "FirstName": "Санкт-Питербург",
            "LastName": "Администратор",
            "MiddleName": "",
            "Login": "AdminGPP_SPb",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": {
                "Code": "0001.009",
                "Description": "Служба бухгалтерского учета и отчетности",
                "SysName": "N_X_104E1346-437C-11E6-4A88-001A4A160153"
            },
            "PositionClassifier": null
        },
        "ActiveVersion": true,
        "VersionDescription": "Начальная версия",
        "VersionName": "1",
        "VersionCreateTime": "02.02.2022 8:14:36",
        "VersionCreateUser": {
            "SysName": "USER_GMV",
            "Description": "Гуляев Максим Вячеславович",
            "FirstName": "Максим",
            "LastName": "Гуляев",
            "MiddleName": "Вячеславович",
            "Login": "gmv",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": null,
            "PositionClassifier": null
        },
        "TAttributes": [
            {
                "SysName": "A_Str_Name",
                "Value": "КСГ",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Str_Note",
                "Value": "",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Complex",
                "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Parent",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Cls_TypeFolder",
                "Value": "N_TYPE_FOLDER_PRODUCT_KSG",
                "Type": "tdmClassifier",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Project",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            }
        ],
        "TVersions": [
            {
                "VersionDescription": "Начальная версия",
                "VersionName": "1",
                "GUID": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}"
            }
        ],
        "TContent": null,
        "TFiles": null
    },
    {
        "GUID": "{18AAC5A8-1BE5-4A34-9FC2-4BEBE24A5D61}",
        "ObjectGuid": "{18AAC5A8-1BE5-4A34-9FC2-4BEBE24A5D61}",
        "ObjectDefName": "O_Folder_Doc",
        "Description": "Дело проекта",
        "Parent": "{E6237962-1447-4524-ACBB-01A58502C780}",
        "StatusName": "S_Created",
        "ModifyTime": "25.05.2023 11:15:13",
        "ModifyUser": {
            "SysName": "USER_AdminGPP_SPb",
            "Description": "Администратор СПб",
            "FirstName": "Санкт-Питербург",
            "LastName": "Администратор",
            "MiddleName": "",
            "Login": "AdminGPP_SPb",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": {
                "Code": "0001.009",
                "Description": "Служба бухгалтерского учета и отчетности",
                "SysName": "N_X_104E1346-437C-11E6-4A88-001A4A160153"
            },
            "PositionClassifier": null
        },
        "ActiveVersion": true,
        "VersionDescription": "Начальная версия",
        "VersionName": "1",
        "VersionCreateTime": "02.02.2022 8:14:36",
        "VersionCreateUser": {
            "SysName": "USER_GMV",
            "Description": "Гуляев Максим Вячеславович",
            "FirstName": "Максим",
            "LastName": "Гуляев",
            "MiddleName": "Вячеславович",
            "Login": "gmv",
            "Password": null,
            "Phone": "",
            "Mail": null,
            "Position": null,
            "DepartmentClassifier": null,
            "PositionClassifier": null
        },
        "TAttributes": [
            {
                "SysName": "A_Str_Name",
                "Value": "Дело проекта",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Str_Note",
                "Value": "",
                "Type": "tdmString",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Complex",
                "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Parent",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            },
            {
                "SysName": "A_Cls_TypeFolder",
                "Value": "N_TYPE_FOLDER_PRODUCT_ProjectCase",
                "Type": "tdmClassifier",
                "TTableRows": null
            },
            {
                "SysName": "A_Ref_Project",
                "Value": "{E6237962-1447-4524-ACBB-01A58502C780}",
                "Type": "tdmObjectLink",
                "TTableRows": null
            }
        ],
        "TVersions": [
            {
                "VersionDescription": "Начальная версия",
                "VersionName": "1",
                "GUID": "{18AAC5A8-1BE5-4A34-9FC2-4BEBE24A5D61}"
            }
        ],
        "TContent": null,
        "TFiles": null
    },
  ...
]  
```

В случае указания сжатого формата возвращаемой коллекции объектов:

```json
[
  {
    "Description": "КСГ",
    "GUID": "{F98C1C1E-C39A-4C64-9EA7-87203FB2AF26}",
    "ObjectDefName": "O_Folder_Doc",
    "ActiveVersion": true,
    "StatusName": "S_Created",
    "Primary": false
  },
  {
    "Description": "Дело проекта",
    "GUID": "{18AAC5A8-1BE5-4A34-9FC2-4BEBE24A5D61}",
    "ObjectDefName": "O_Folder_Doc",
    "ActiveVersion": true,
    "StatusName": "S_Created",
    "Primary": false
  },
  {
    "Description": "Внешние согласования",
    "GUID": "{3541F204-3F50-41DB-819B-6B8EC5DC6B0E}",
    "ObjectDefName": "O_Folder_Doc",
    "ActiveVersion": true,
    "StatusName": "S_Created",
    "Primary": false
  },
...
]
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getreferencedby `POST`
Возвращает все объекты ссылающиеся на указанный в методе посредством связи через прямой атрибут типа "ссылка" и табличный атрибут в составе котрого атрибут типа "ссылка"

#### Request:

```json
{
    "Mode": "Getreferencedby",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}
```

Обязательные параметры:

|Parameter   |Type               |Description
|-           |-                  |-               
|Mode        |string             |Вызываемый метод API
|TObject     |[TObject](#TObject)|Объект TDMS
|TObject.GUID|string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "TContent": [
    {
      "Description": "0001-XXX1-19 от 14.10.2019 (Санкт-Петербургcкий филиал)",
      "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}",
      "ObjectDefName": "O_Bill",
      "ActiveVersion": true,
      "StatusName": "S_Bill_Annulled"
    },
    {
      "Description": "123 от  (Санкт-Петербургcкий филиал)",
      "GUID": "{BE202BE8-53C3-4FAF-AFAA-365719A5F601}",
      "ObjectDefName": "O_Bill",
      "ActiveVersion": true,
      "StatusName": "S_Bill_Closed"
    }
  ]
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getobjectdef `POST`
Получение информации о типе объекта TDMS по идентификатору, указанному в [TObject](#TObject)

#### Request:

```json
{
    "mode": "Getobjectdef",
    "TObject": {
        "ObjectDefName": "O_PDoc"
    }
} 
```

Обязательные параметры:

|Parameter            |Type               |Description
|-                    |-                  |-               
|Mode                 |string             |Вызываемый метод API
|TObject              |[TObject](#TObject)|Объект TDMS
|TObject.ObjectDefName|string             |Идентификатор типа объекта

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "FileDefs": [
    {
      "Description": "Текстовый документ",
      "Sysname": "FILE_DOC",
      "Extensions": "*.doc, *.docx, *.rtf, *.odt, *.txt"
    },
    {
      "Description": "Электронная таблица",
      "Sysname": "FILE_XLS",
      "Extensions": "*.xls, *.xlsx, *.csv, *.ods"
    },
    {
      "Description": "Публикация",
      "Sysname": "FILE_PDF_PUBL",
      "Extensions": "*.pdf"
    },
    {
      "Description": "Документ Adobe Acrobat",
      "Sysname": "FILE_AA_PDF",
      "Extensions": "*.pdf"
    },
    {
      "Description": "Подлинник",
      "Sysname": "FILE_PDF_ORIG",
      "Extensions": "*.pdf, tif, *.tiff"
    },
    {
      "Description": "Исходные данные для расчета",
      "Sysname": "FILE_DLG",
      "Extensions": "*.dlg, *.dlf"
    },
    {
      "Description": "Файл ЭЦП",
      "Sysname": "FILE_SIG",
      "Extensions": "*.sig, *.sgn"
    },
    {
      "Description": "Чертеж CAD",
      "Sysname": "FILE_DWG",
      "Extensions": "*.dwg, *.dxf, *.dwf, *.dgn"
    }
  ],
  "Statuses": [
    {
      "Description": "Недействующий (Документ)",
      "SysName": "S_Doc_Cancel"
    },
    {
      "Description": "В разработке (Документ)",
      "SysName": "S_Doc_Developing"
    },
    {
      "Description": "Разработан (Документ)",
      "SysName": "S_Doc_Developed"
    },
    {
      "Description": "На согласовании (Документ)",
      "SysName": "S_Doc_Agreement"
    },
    {
      "Description": "Проверка в архиве (Документ)",
      "SysName": "S_Doc_ArchCheck"
    },
    {
      "Description": "Действующий (Документ)",
      "SysName": "S_Doc_Archived"
    },
    {
      "Description": "Действующий на изменении (Документ)",
      "SysName": "S_Doc_Changing"
    },
    {
      "Description": "Аннулирован (Документ)",
      "SysName": "S_Doc_Annulled"
    }
  ]
}
```
Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getversionbydescription `POST`
Получение версий объекта TDMS по описанию версии объекта [TObject](#TObject)

#### Request:

```json
{
    "Mode": "Getversionbydescription",
    "TObject": {
        "GUID": "{91C40073-340A-43B4-9EC1-43A3A9BB8512}",
        "VersionDescription": "hello"
    }
}
```

Обязательные параметры:

|Parameter                 |Type               |Description
|-                         |-                  |-               
|Mode                      |string             |Getversionbydescription - вызываемый метод API
|TObject                   |[TObject](#TObject)|Объект TDMS
|TObject.GUID              |string             |Идентификатор объекта TDMS, вреди версий которого осуществляется поиск
|TObject.VersionDescription|string             |Описание версии объекта TDMS

#### Response

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

### Findobjects `POST`
Поиск TDMS объектов по типу объекта и значениям атрибутов. Возвращается коллекция объектов [TShortObject](#TShortObject), соотвутствующих кретериям поиска.
Поиск осуществляется по атрибутам и свойствам объекта.
Указание типов объектов:

```json
{
    "Type": "ObjectDef",
    "Value": "'O_Building' or 'O_Part'"
}
```

Поиск по атрибутам объекта:

```json
{
    "Type": "Attribute",
    "SysName": "A_Str_Designation",
    "Value": "'0203.КТО.001.4701- 4799.007' or '0203.КТО.001'"
}
```

Поиск по cвойствам объекта:

```json
{
    "Type": "Property",
    "Value": ">='23.10.2023'",
    "SysName": "ModifyTime"
},
{
    "Type": "Property",
    "Value": "='U_X_8B57FC54-6CFA-11E5-6289-5254005756B4'",
    "SysName": "ModifyUser"
}
```
Перечень доступных свойств:  
```
ObjectDef
Description
Status
Administrator
CreateTime
ModifyTime
StatusModifiedTime
CreateUser
ModifyUser
StatusModifiedUser
Handle
ObjectGuid
```

#### Request

```json
{
    "Mode": "Findobjects",
    "TConditions": [
        {
            "Type": "ObjectDef",
            "Value": "'O_Building' or 'O_Part'"
        },
        {
            "Type": "Attribute",
            "SysName": "A_Str_Designation",
            "Value": "'0203.КТО.001.4701- 4799.007' or '0203.КТО.001'"
        },
        {
            "Type": "Property",
            "Value": ">='23.10.2023'",
            "SysName": "ModifyTime"
        },
        {
            "Type": "Property",
            "Value": "='U_X_8B57FC54-6CFA-11E5-6289-5254005756B4'",
            "SysName": "ModifyUser"
        }
    ]
}
```

Обязательные параметры:

|Parameter            |Type                     |Description
|-                    |-                        |-               
|Mode                 |string                   |Вызываемый метод API
|TConditions          |[TCondition](#TCondition)|Коллекция условий поиска

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "TContent": [
    {
      "Description": "0203.КТО.001 - Комплекс термического обезвреживания отходов",
      "GUID": "{E57103AF-E7ED-4410-B596-2D9648FE8793}",
      "ObjectDefName": "O_Part",
      "ActiveVersion": true
    },
    {
      "Description": "0203.КТО.001.4701- 4799.007 - Линии электропередач воздушные и электротехнические коммуникации",
      "GUID": "{D7683875-4730-46F9-933E-66D5E99A4D1A}",
      "ObjectDefName": "O_Building",
      "ActiveVersion": true
    }
  ]
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Findusers `POST`
Поиск пользователей TDMS [TUser](#TUser). Осуществляется по свойствам

#### Request

```json
{
    "mode": "Findusers",
    "tuser": {
        "LastName": "Иванов",
        "PositionClassifier": {
            "Description": "Главный геодезист",
        }
    }
}
```

Обязательные параметры:

|Parameter            |Type           |Description
|-                    |-              |-               
|Mode                 |string         |Вызываемый метод API
|TUser                |[TUser](#TUser)|Свойства пользователя задаются как критерии поиска

#### Response

```
Status: 200
Content-Type: application/json
```

```json
[
  {
    "SysName": "U_X_7BC1E68C-5B40-4BFA-A928-60025D4A29BD",
    "Description": "Иванов Петр Олегович",
    "FirstName": "Петр",
    "LastName": "Иванов",
    "MiddleName": "Олегович",
    "Login": "ivanov",
    "Password": null,
    "Phone": "(772) 51127",
    "Mail": null,
    "Position": null,
    "DepartmentClassifier": {
      "Code": "0007.850.853",
      "Description": "Лаборатория тампонажных растворов",
      "SysName": "N_X_3ED7AA91-F36B-11EA-80CB-A2F1AEE68492"
    },
    "PositionClassifier": {
      "Code": "",
      "Description": "Инженер II категории",
      "SysName": "A142D8F4_03AF_46CE_BA22_33D74EEE7E51"
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
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Createversion `POST`
Создание версии объекта с указанием имени и описания версии

#### Request

```json
{
    "Mode": "Createversion",
    "TObject": {
        "GUID": "{491544D8-8491-4098-A399-5FA4ABC27F22}",
        "VersionName": "13 Hi"
    }
}
```

Обязательные параметры:

|Parameter                    |Type               |Description
|-                            |-                  |-               
|Mode                         |string             |Вызываемый метод API
|TObject                      |[TObject](#TObject)|Объект TDMS
|TObject.GUID                 |string             |Идентификатор объекта TDMS
|TObject.VersonName           |string             |Имя версии
|TObject.VersonDescriptionName|string             |Описание версии

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "GUID": "{CE1DD719-ACB0-4456-A530-EACF89ADE2EA}",
  "ObjectGuid": "{297FCA5B-79B4-4240-9C54-9D22654C7EAE}",
  "ObjectDefName": "O_Set",
  "Description": "0203.001.Р.1/2.0001.ГГРС.001.1102.12-ЭС изм.5",
  "Parent": "{83CF7B6D-E11A-47D9-A4B7-5B316AF4033C}",
  "StatusName": "S_Volume_Annulled",
  "ModifyTime": "08.11.2022 9:25:56",
  "ModifyUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": "Сергей",
    "LastName": "Капаров",
    "MiddleName": "Михайлович",
    "Login": "sysadmin",
    "Password": null,
    "Phone": "",
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "ActiveVersion": true,
  "VersionDescription": null,
  "VersionName": "13 Hi",
  "VersionCreateTime": "08.11.2022 9:25:56",
  "VersionCreateUser": {
    "SysName": "SYSADMIN",
    "Description": "SYSADMIN",
    "FirstName": "Сергей",
    "LastName": "Капаров",
    "MiddleName": "Михайлович",
    "Login": "sysadmin",
    "Password": null,
    "Phone": "",
    "Mail": null,
    "Department": null,
    "Position": null
  },
  "TAttributes": [
    {
      "SysName": "A_Str_Designation",
      "Value": "0203.001.Р.1/2.0001.ГГРС.001.1102.12-ЭС",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Name",
      "Value": "ЮЕГ. РАСШИРЕНИЕ ЕСГ ДЛЯ ОБЕСПЕЧЕНИЯ ПОДАЧИ ГАЗА В ГАЗОПРОВОД «ЮЖНЫЙ ПОТОК». Газопроводы газораспределительных систем. Газораспределительная станция. Электроснабжение",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_User_Author",
      "Value": "USER_GMV",
      "Type": "tdmUserLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Note",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Complex",
      "Value": "{2B1C6ED4-17B0-4553-8756-B631F640BB9A}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_PagesCount",
      "Value": 10,
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_A4Count",
      "Value": "",
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_ShFormats",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Table_Formats",
      "Value": null,
      "Type": "tdmTable",
      "TTableRows": []
    },
    {
      "SysName": "A_Ref_Parent",
      "Value": "{83CF7B6D-E11A-47D9-A4B7-5B316AF4033C}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_OrderChange",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_ChangeNum",
      "Value": 5,
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_List_Change",
      "Value": "",
      "Type": "tdmList",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_AcceptEA",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_AcceptTA",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_Begin",
      "Value": "01.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_End",
      "Value": "21.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Barcode",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Cls_DSP",
      "Value": "",
      "Type": "tdmList",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_InvNum",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_InvNum_SubPodr",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_SubDesignation",
      "Value": "",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Part",
      "Value": "{83C58C7B-6553-4678-976E-A51EDBC2F7AE}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Build",
      "Value": "{8BD91B25-748A-4CDF-A3FE-A1D840A97E83}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Organization",
      "Value": "{E17BC391-8108-4DCD-A06C-6E3F6406CF2A}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Contract",
      "Value": "{696D7F3D-8B71-441C-921C-5C2E629819B9}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_User_GIP",
      "Value": "USER_F6798DB1_3AB3_471A_A9E8_C6CEC2B49748",
      "Type": "tdmUserLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_SubContract",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Table_Predecessors",
      "Value": null,
      "Type": "tdmTable",
      "TTableRows": []
    },
    {
      "SysName": "A_Ref_Mark",
      "Value": "{D1019109-4AFB-4B03-81DC-9EE68E97C0E9}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Int_MarkNum",
      "Value": "",
      "Type": "tdmInteger",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Phase",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Bool_Sync",
      "Value": "",
      "Type": "tdmBool",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Project",
      "Value": "{DEBDC3FF-E9E0-4F29-A948-E0F6FB7306AC}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Building_Stage",
      "Value": "1",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Building_SubStage",
      "Value": "2",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_sCodeMark",
      "Value": "ЭС",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Stage",
      "Value": "{D3B27616-5A99-44E9-8AF6-CFC9E1C7BA41}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Dept",
      "Value": "{A93A7BCE-A69C-48C9-ACD6-7797EA225132}",
      "Type": "tdmObjectLink",
      "TTableRows": null
    },
    {
      "SysName": "A_Dat_NTB_DocImplDate",
      "Value": "22.08.2022 14:30:11",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Str_Designation_Customer",
      "Value": "ЙЙЙЙ",
      "Type": "tdmString",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_Begin_Fact",
      "Value": "01.10.2021 0:00:00",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Date_End_Fact",
      "Value": "",
      "Type": "tdmDate",
      "TTableRows": null
    },
    {
      "SysName": "A_Ref_Package_Unload",
      "Value": "",
      "Type": "tdmObjectLink",
      "TTableRows": null
    }
  ],
  "TVersions": [
    {
      "VersionDescription": null,
      "VersionName": "13 Hi",
      "GUID": "{CE1DD719-ACB0-4456-A530-EACF89ADE2EA}"
    },
    {
      "VersionDescription": "РИ №246-21 от 12.10.2021",
      "VersionName": "Изм.4",
      "GUID": "{816C6166-B562-4EEC-BDFF-38ABAC81EB79}"
    },
    {
      "VersionDescription": "",
      "VersionName": "1",
      "GUID": "{491544D8-8491-4098-A399-5FA4ABC27F22}"
    }
  ],
  "TContent": null,
  "TFiles": null
}
```

### Deletefiles `POST`
Удаление файлов из файлового состава объекта

#### Request

```json
{
    "Mode": "Deletefiles",
    "TObject": {
        "GUID": "{586C55CA-DDAB-4575-91E3-ADCE5B86D03C}",
        "TFiles": [
            {
                "FileDefName": "FILE_PDF_PUBL", "FileName": "Отчет Модуль TDMS API.pdf"
            }
        ]
    }
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS
|TObject.GUID  |string             |Идентификатор объекта TDMS
|TObject.TFiles|[[TFile](#TFile)]  |Коллекция описаний файлов или типов файло, которые необходимо удалить

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

```
Файл(ы) успешно удалены
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Createlink `POST`
Создание ссылки на объект в составе другого объекта

#### Request

```json
{
    "Mode": "Createlink",
    "TObject": {
        "GUID": "{0F0401D7-EC6A-4C03-9AAD-83F2F110620A}",
        "Parent": "{F72A14E4-722E-4C3D-8EEB-601187557C37}"
    }
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS
|TObject.GUID  |string             |Идентификатор объекта TDMS
|TObject.Parent|string             |Объект, в состав которого необходимо поместить ссылку

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

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

### Moveobject `POST`
Перемещение объекта в состав нового родителя

#### Request

```json
{
    "Mode": "Moveobject",
    "TObject": {
        "GUID": "{D119A8C0-39BF-46D4-9FCF-5D1F84593199}",
        "Parent": "{D54E0B39-D7E1-48DF-BD86-736599F1CA7E}"
    }
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS
|TObject.GUID  |string             |Идентификатор объекта TDMS
|TObject.Parent|string             |Новый родительский объект

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

```
OK
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Lockobject `POST`
Блокировка объекта от имени пользователя выполняющего запрос. Существует 2 типа блокировки:
Edit - 2 - редактированеи карточки объекта, Files - 3  - редактирвоание файлов объекта. Соответсвующие типы необходимо указать в теле запроса

#### Request

```json
{
    "Mode": "Lockobject",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    },
    "Locktypes": [1,2]
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS
|Locktypes     |[]                 |Коллекция типов блокировки

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

```
OK
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Unlockobject `POST`
Разблокировка объекта от имени пользователя выполняющего запрос. Существует 2 типа блокировки:
Edit - 2 - редактированеи карточки объекта, Files - 3  - редактирвоание файлов объекта. Соответсвующие типы необходимо указать в теле запроса

#### Request

```json
{
    "Mode": "Unlockobject",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    },
    "Locktypes": [1,2]
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS
|Locktypes     |[]                 |Коллекция типов блокировки

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

```
OK
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getobjectpermissions `POST`
Информация о правах доступа пользователя на объекте. Если пользователь, чьи права необходимо получить, не указан, запрос будет выполнен для текущего пользователя

#### Request:

```json
{
    "mode": "Getobjectpermissions",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    },
    "TUser": {
        "SysName": "U_X_8D00A152-8788-11E5-DE87-5254005756B4"
    }
} 
```

Обязательные параметры:

|Parameter    |Type               |Description
|-            |-                  |-               
|Mode         |string             |Вызываемый метод API
|TObject      |[TObject](#TObject)|Объект TDMS
|TObject.GUID |string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
[
  {
    "SysName": "View",
    "Value": "tdmAllow"
  },
  {
    "SysName": "Edit",
    "Value": "tdmNone"
  },
  {
    "SysName": "EditFiles",
    "Value": "tdmAllow"
  },
  {
    "SysName": "EditContent",
    "Value": "tdmNone"
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

### Getobjectroles `POST`
Информация о ролях объекта. Для получения ролей уонкретного пользователя его необходимо указать в параметре TUser.SySname. При указании параметра TUser.Sysname = null, вернутся роли текущего пользователя.
Для получения всех ролей на объекте необходио отправлять запрос без указания параметра TUser

#### Request:

```json
{
    "mode": "Getobjectroles",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    },
    "TUser": {
        "SysName": "U_X_8D00A152-8788-11E5-DE87-5254005756B4"
    }
}  
```

Параметры:

|Parameter    |Required|Type               |Description
|-            |-       |-                  |-               
|Mode         |true    |string             |Вызываемый метод API
|TObject      |true    |[TObject](#TObject)|Объект TDMS
|TObject.GUID |true    |string             |Идентификатор объекта TDMS
|TUser        |false   |[TUser](#TUser)    |Пользователь
|TUser.SySname|false   |string             |Идентификатор пользователя TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
[
  {
    "SysName": "ROLE_DEVELOPER",
    "Description": "Разработчик",
    "User": "SYSADMIN",
    "Group": null
  },
  {
    "SysName": "r_View",
    "Description": "Просмотр",
    "User": null,
    "Group": "ALL_USERS"
  },
  {
    "SysName": "R_PRC_Owner",
    "Description": "Владелец",
    "User": "U_X_8D00A152-8788-11E5-DE87-5254005756B4",
    "Group": null
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

### Getobjectfiles `POST`
Возвращает информацию о файловом составе объекта

#### Request:

```json
{
    "mode": "Getobjectfiles",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}   
```

Параметры:

|Parameter    |Required|Type               |Description
|-            |-       |-                  |-               
|Mode         |true    |string             |Вызываемый метод API
|TObject      |true    |[TObject](#TObject)|Объект TDMS
|TObject.GUID |true    |string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
[
    {
        "FileDefName": "FILE_IMAGE",
        "FileName": "download.jpg",
        "CreateTime": "30.11.2021 9:48:21",
        "Handle": "TH71B10752B1180000000000",
        "ModifyTime": "30.11.2021 9:48:21",
        "UploadUser": "SYSADMIN",
        "UploadTime": "01.12.2021 13:52:32",
        "WorkFileName": "c:\\TFSS\\Temp\\THFFE415E5E4050000000000\\{5E9FD474-EF02-4D56-B3E9-37751DD056DE}\\download.jpg"
    },
    {
        "FileDefName": "FILE_IMAGE",
        "FileName": "modnye-bluzki-1.jpg",
        "CreateTime": "30.11.2021 9:50:13",
        "Handle": "TH70B10751B1180000000000",
        "ModifyTime": "30.11.2021 9:50:13",
        "UploadUser": "SYSADMIN",
        "UploadTime": "01.12.2021 13:52:32",
        "WorkFileName": "c:\\TFSS\\Temp\\THFFE415E5E4050000000000\\{5E9FD474-EF02-4D56-B3E9-37751DD056DE}\\modnye-bluzki-1.jpg"
    },
    {
        "FileDefName": "FILE_DOC",
        "FileName": "Text.txt",
        "CreateTime": "14.12.2022 18:01:12",
        "Handle": "TH7C8C075A8C1B0000000000",
        "ModifyTime": "14.12.2022 18:01:12",
        "UploadUser": "SYSADMIN",
        "UploadTime": "14.12.2022 18:01:12",
        "WorkFileName": "c:\\TFSS\\Temp\\THFFE415E5E4050000000000\\{5E9FD474-EF02-4D56-B3E9-37751DD056DE}\\Text.txt"
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

### Getobjectfilescontent `POST`
Возвращает содержимое файлов объекта. Если в запросе не указаны имена искомых файлов, метод вернет архив с полным файловым составом

#### Request:

```json
{
    "mode": "Getobjectfilescontent",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}",
        "TFiles": [
            {
                "FileName": "Инструкция.pdf"
            },
            {
                "FileName": "download.jpg"
            }
        ]
    }
}    
```

Параметры:

|Parameter     |Required|Type               |Description
|-             |-       |-                  |-               
|Mode          |true    |string             |Вызываемый метод API
|TObject       |true    |[TObject](#TObject)|Объект TDMS
|TObject.GUID  |true    |string             |Идентификатор объекта TDMS
|TObject.TFiles|        |[TFile]            |Описание файла объекта TDMS

#### Response

```
Status: 200
Content-Type: application/octet-stream
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Addfiles `POST`
Помещает файл извне в файловый состав объекта. в URL необходимо прописать `/api/files`. Пример: `адрес_сервер/api/files`.
Ниже представлен пример отправки запроса из Postman:
![download repository!](./assets/003.png)

Запрос отправляется в формате `form-data`. В ключе с именем `json` и типом `Text` необходимо поместить схему запроса в формате `json`. В остальные ключи с типом `File` (не `Text`) помещаются сами файлы

```json
{
    "mode": "Addfiles",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}    
```

Параметры:

|Parameter     |Required|Type               |Description
|-             |-       |-                  |-               
|Mode          |true    |string             |Вызываемый метод API
|TObject       |true    |[TObject](#TObject)|Объект TDMS
|TObject.GUID  |true    |string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Getobjectlockstatus `POST`
Информация о блокировке объекта. Возвращает факт блокировки, пользователя заблокировавшего объект, а также время блокировки

#### Request:

```json
{
    "Mode": "Getobjectlockstatus",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}
```

Обязательные параметры:

|Parameter    |Type               |Description
|-            |-                  |-               
|Mode         |string             |Вызываемый метод API
|TObject      |[TObject](#TObject)|Объект TDMS
|TObject.GUID |string             |Идентификатор объекта TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
    "Locked": true,
    "LockUser": "SYSADMIN",
    "LockTime": "16.12.2022 17:48:27"
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Geturlobject `POST`
Получить URL на указанный объект. URL ведет на карточку объекта в веб-клиенте TDMS. В ответе будет получен URL вида:
```
http://your_host/client/#locate/{handle}
```
где {handle} - это Handle объекта TDMS

#### Request

```json
{
    "Mode": "Geturlobject",
    "TObject": {
        "GUID": "{5E9FD474-EF02-4D56-B3E9-37751DD056DE}"
    }
}
```

Обязательные параметры:

|Parameter     |Type               |Description
|-             |-                  |-               
|Mode          |string             |Вызываемый метод API
|TObject       |[TObject](#TObject)|Объект TDMS

#### Response

```
Status: 200
Content-Type: text/plain; charset=UTF-8
```

Тело ответа:

```
http://tdms-srv-virt:444/client/#locate/THB772039C72180000000000
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}
|404 NotFound  |В системе не найден объект с GUID = '{GUID}'

### Getuser `POST`
Получение информации о пользователе

#### Request:

```json
{
    "Mode": "Getuser",
    "TUser": {
        "Description": "Абдрахманова Гузель Флуровна",
        "Sysname": "U_X_8D00A152-8788-11E5-DE87-5254005756B4"
    }
}
```

Обязательные параметры:

|Parameter    |Type           |Description
|-            |-              |-               
|mode         |string         |Вызываемый метод API
|TUser        |[TUser](#TUser)|Пользователь TDMS
|TUser.Sysname|string         |Идентификатор пользователя TDMS

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "SysName": "U_X_8D00A152-8788-11E5-DE87-5254005756B4",
  "Description": "Абдрашитова Олеся Нагимовна",
  "FirstName": "Олеся",
  "LastName": "Абдрашитова",
  "MiddleName": "Нагимовна",
  "Login": "oabdrashitova",
  "Password": null,
  "Phone": "(770) 58309ddvdvdv",
  "Mail": null,
  "Department": {
    "Code": "",
    "Description": "Отдел компрессорных станций",
    "SysName": "NODE_B21A1E89_B7D8_4A56_BB54_99E1F6C84283"
  },
  "Position": {
    "Code": "",
    "Description": "Архитектор 2 категории",
    "SysName": "NODE00026770"
  }
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Getcurrentuser `POST`
Получение информации о текущем пользователе, от имени которого отправлен запрос

#### Request:

```json
{
    "Mode": "Getcurrentuser",
}
```

Обязательные параметры:

|Parameter    |Type           |Description
|-            |-              |-               
|Mode         |string         |Вызываемый метод API

#### Response

```
Status: 200
Content-Type: application/json
```

```json
{
  "SysName": "U_X_8D00A152-8788-11E5-DE87-5254005756B4",
  "Description": "Абдрашитова Олеся Нагимовна",
  "FirstName": "Олеся",
  "LastName": "Абдрашитова",
  "MiddleName": "Нагимовна",
  "Login": "oabdrashitova",
  "Password": null,
  "Phone": "(770) 58309ddvdvdv",
  "Mail": null,
  "Department": {
    "Code": "",
    "Description": "Отдел компрессорных станций",
    "SysName": "NODE_B21A1E89_B7D8_4A56_BB54_99E1F6C84283"
  },
  "Position": {
    "Code": "",
    "Description": "Архитектор 2 категории",
    "SysName": "NODE00026770"
  }
}
```

Ошибки:

|Error code    |Description
|-             |-
|400 BadRequest|Any error
|404 NotFound  |Убедитесь, что параметр 'mode' задан и он не пустой
|404 NotFound  |Указанный метод в параметре 'mode' не найден
|404 NotFound  |В запросе не найден параметр {параметр}

### Createuser `POST`
Создание пользователя TDMS. Возвращает системное имя пользователя. Логин пользователя генерится автоматически по правилу:
`{Lastname}{Firstname}`

#### Request:

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

#### Response
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

### EditUser `POST`
Updates attributes for existing TDMS User

#### Request:
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

#### Response
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

### ExecuteFunc `POST`
Метод позволяет выполнить специальную функцию, определенную в конфигурации (на vbs) или в специальном расширении сервера. Функция должна быть помечена в скрипте как `extern`.

Функция должна возвращать только примитивный тип данных (string, date, bool, int, float).

Запускается функция с правами авторизовавшегося пользователя, но внутри функции возможно повышение привилегий (через `SysadminPermissions` или `SysadminModeOn`).

Исключения, возникающие при вызове, генерируют ошибку HTTP. Превышение времени ожидания генерирует ошибку HTTP. 

#### Request:
```
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

#### Response
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

### GetAPIEvents `POST`
Позволяет получить список определенных событий, произошедших в TDMS за определенный период времени. Можно получить специальные события API, а также некоторые стандартные события TDMS связанных с объектами TDMS определенного типа.

Специальные события регистрируется при вызове функции внутри vbs-конфигурации `L_Misc.APIEvent`. В текущей версии используется стандартный журнал событий TDMS, но в последующих версиях реализация может быть изменена.
Для получения стандартных событий необходимо указать в фильтре идентификатор типа в формате `Тип стандартного события`@`Тип объекта`, где "тип стандартного события" соответствует одному из имени в перечислении TDMSEventType.
Каждое специальное событие имеет тип, идентифицируемый строкой длинной от 3 до 16 символов без пробелов, время наступления, необязательное краткое сообщение (до 1000 символов) и необязательный один прикрепленный объект TDMS
Для каждого стандартного события возвращается тип, идентифицируемый типом стандартного события, время наступления, описание события и обязательный один прикрепленный объект TDMS заданного типа

#### Request:
```
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

#### Response
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
