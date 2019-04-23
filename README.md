# Gusher

[Pusher](https://pusher.com/docs) implment server-side by golang


## Deprecated

Please use new project [gusher.clsuter](https://github.com/syhlion/gusher.cluster)

## Requirements

* sqlite 3

## Usage

Install from source

```
$ go get github.com/syhlion/gusher
```

The first time you use this service, please execute this command:

```
$ gusher init
```

After generating config.json, please excute this command:

```
$ gusher start
```

## Command

* init

Follow instructions.

This command can auto generate config.json.

* start

Param | Type | Default|Dircetions
---|---|---|----
--config, -c | string |./default.json| config Optinal

## Config  


Default generate
```
{
        "auth_account":"account", //Super Admin 
        "auth_password":"password", //Bcrypt Encoding 
        "environment":"DEBUG", // DEBUG || DEVELOPMENT || PRODUCATION
        "logfile":"./", // "console" or "/homs/user/x.log"
        "listen":":8001",
        "api_listen":":8002",
        "sqlfile":"./appdata.sqlite",
        "max_wait_hook":10,
}
                        
```


## API

Only Private Api need http basic Auth, Super Admin can access all Private Api

### Private Api (use key of api_listen port)

#### Register:

`[POST] /api/register`  

* Param:  

Name|Type|Directions
---|---|---
app_key | string | a unique app_key. Require
auth_account | string | app admin basic auth account. Require
auth_password | string | app admin basic auth password. Require
connect_hook | string | You can take this verification [webhook](https://github.com/syhlion/gopusher/#web-hook). Optinal

* 200 status Response:  

```
{
    "app_key":"test",
    "auth_account":"app_admin",
    "auth_password":"password", //Bcrypt Encoding
    "connect_hook":"http://localhost/vaildlogin"
    "request_ip":"127.0.0.1:77777"
}
```

#### Unregister:  

`[DELETE] /api/{app_key}/unregister`  

* 200 status Response:

```
{
    "message":"Scuess"
}
```


#### List All App:  

`[GET] /api/app-list`  

* 200 status Response:

```
{
    "total":2,
    "data":
    [
        {
            "app_key":"db15759925b279b4b037d7a4e1f92b0f",
            "auth_account":"scott",
            "auth_password":"760804",
            "connect_hook":"http://localhost/vaildlogin"
            "request_ip":"127.0.0.1:50040",
            "date":"2015/08/25 11:39:12",
            "timestamp":"1440473952104"
        },
        {
            "app_key":"56f08a519be877060fb4a4ea2a75aad8",
            "auth_account":"scott",
            "auth_password":"760804",
            "connect_hook":"http://localhost/vaildlogin"
            "request_ip":"127.0.0.1:55567",
            "date":"2015/08/26 12:10:54",
            "timestamp":"1440562254686"
        }
    ]
}

```


#### Push:  

`[POST] /api/push/{app_key}`  

* Param:  

Name|Type|Dircetions
---|---|---
content| string | the message you send
user_tag | string | the message who will receive. Support Regex. Optinal

* 200 status Response:  

```
{
    "app_key":"abcdefghijklmnop",
    "content":"hello world",
    "user_tag":"A:1",
    "total":1
}
```

#### List Online User:  

`[GET] /api/{app_key}/listonlineuser`  

* 200 status Response:

```
{
    "app_key":"abcdefghijklmnop",
    "total":1,
    "user_tags":{"bbbb":null,"tttt":null}
}
```

### Public Api (use key of listen port)

#### Client Connect:  

If you have fill in Register api connect_hook, use this:  

`[GET] ws://localhost/ws/{app_key}/{user_tag}?token={token}`  


If it is not,use this:  

`[GET] ws://localhost/ws/{app_key}/{user_tag}`


## WEB hook

#### Connect Hook

If you use this, you need to prepare a hook api, to accept http request POST "token"  

If successful at http body output "user_tag" eqaul client connet user_tag    

* Param:  

Name|Type|Dircetions
---|---|---
token| string | Your server api  will get POST from GuPusher
