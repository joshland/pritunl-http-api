# Pritunl HTTP API

[Pritunl](https://pritunl.com/) is an enterprise-level VPN solution based on the OpenVPN protocol. The free version can have a server creation, and the paid version has more features.

[Installation and Configuration](https://docs.pritunl.com/docs)

Since the open source version does not provide API calls, there is a set of HTTP API interfaces implemented through simulated login for this project

## Install and run

[Releases](https://github.com/chanyipiaomiao/pritunl-http-api/releases)(binarries)

## Configuration file

```bash
# The ${} in the configuration file is first obtained from the environment variables, and if it is not obtained, the default value will be used.

appname = pritunl-http-api
autorender = false
copyrequestbody = true
EnableDocs = false

# Run mode dev | prod
runmode = "${PRITUNL_HTTP_API_RUNMODE||dev}"

# Listen address
httpaddr = "${PRITUNL_HTTP_API_LISTEN_IP||127.0.0.1}"

# Listening port
httpport = "${PRITUNL_HTTP_API_PORT||30080}"

[log]
# Log path
logPath = "${PRITUNL_HTTP_API_LOGPATH||logs/pritunl.log}"

[security]
# Whether to enable token authentication, enabling will require adding a token header to the request header

# true | false
enableToken = false

# Name of token header in request header
tokenName = "${PRITUNL_HTTP_API_TOKEN_NAME||PRITUNL-HTTP-API-TOKEN}"

# token value
token = "${PRITUNL_HTTP_API_TOKEN||CyNINFvjdJTh4QTfqsPVJuNdRDUGvHnU}"


[common-vpn-wenba]
# common is a logo, which represents that server, which can be freely defined, for example, there are multiple pritunl servers, which will be used when adjusting the interface
# wenba is the name of an organization in pritunl, pritunl can contain multiple organizations, it will be used when adjusting the interface
# common-vpn-wenba The combination of the three refers to the organization that wants to operate that server

# url is the pritunl console address
url = "${PRITUNL_COMMON_URL||https://x.x.x.x}"

# Login username and password
username = "${PRITUNL_COMMON_USERNAME||wenba}"
password = "${PRITUNL_COMMON_PASSWORD||123456}"

# organization id is corresponding to Wenba above
organization = "${PRITUNL_COMMON_WENBA_ORG||123456}"

# debug for debugging
# true | false
debug = "${PRITUNL_COMMON_DEBUG||false}"
```

### How to get the name and id of organization

Log in to the pritunl web console first

Chrome can open the developer tools by pressing F12 or the right-click check menu to monitor the Network

Switch to the Users tab and find the response of the interface

```bash
/organization?page=0
```

There is the name and ID of the organization. These two values ​​will be used when calling the interface


## interface

### Add user

```bash
POST /pritunl?vpn-name=VPN-name&org-name=organization-name&username=username

vpn-name is the name configured in the configuration file, eg: common
The name of the org-name organization, eg: wenba
username username
email optional

Find the corresponding configuration in the configuration file through vpn-name and org-name

```

Returns

```bash
{
    "data": {
        "profileLink": "https://x.x.x.x/k/u3yUsEcL",
        "userId": "5d008e164ed924023806d388",
        "username": "test7777"
    },
    "entryType": "Pritunl OpenVPN Operation",
    "error": "",
    "statusCode": 0
}

profileLink is the URL for downloading configuration files and configuring 2-step verification
```

### Delete Users

```bash
DELETE /pritunl?vpn-name=VPN-name&org-name=organization-name&username=user-name
```

Returns

```bash
{
    "data": {
        "status": "deleted",
        "username": "test7777"
    },
    "entryType": "Pritunl OpenVPN Operation",
    "error": "",
    "statusCode": 0
}
```


### Disable user

```bash
PUT /pritunl?vpn-name=VPN-name&org-name=organization-name&username=username&status=disable

```

Returns

```bash
{
    "data": {
        "status": "disabled",
        "username": "test7777"
    },
    "entryType": "Pritunl OpenVPN Operation",
    "error": "",
    "statusCode": 0
}
```

### Enable user

```bash
PUT /pritunl?vpn-name=VPN name&org-name=organization name&username=user name&status=enable
```

Returns

```bash
{
    "data": {
        "status": "enabled",
        "username": "test7777"
    },
    "entryType": "Pritunl OpenVPN Operation",
    "error": "",
    "statusCode": 0
}
```

### Add users in bulk

```bash
POST /pritunl?vpn-name=VPN-name&org-name=organization-name&username=user-name&multi=yes
```

JSON Request Body

Heaeder: Content-Type  application/json

```bash
[
	{
		"name": "test5555",
		"email": null
	},
	{
		"name": "test6666",
		"email": null
	}
]
```

Returns

```bash
{
    "data": [
        {
            "profileLink": "https://x.x.x.x/k/aP25mrUn",
            "userId": "5cff8b174ed92402380625f7",
            "username": "test5555"
        },
        {
            "profileLink": "https://x.x.x.x/k/x47p83KG",
            "userId": "5cff8b974ed924023806264f",
            "username": "test6666"
        }
    ],
    "entryType": "Pritunl OpenVPN Operation",
    "error": "",
    "statusCode": 0
}
```
