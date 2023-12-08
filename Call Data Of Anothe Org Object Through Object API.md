# 1
# To get the data of perticular Object in POSTMAN: In Org A access data Through Session Id:
```
Step 1: System.debug(System.Url.getSalesforceBaseUrl()); // endpoind eg: https://www.salesforce.com --> set in remotesitesetting
         url/+services/apexrest/objectname

                                        OR

Step 1: My Domain : https:// + curious-goat-d8hj6v-dev-ed.trailblaze.my.salesforce.com + /services/apexrest/Cases
```
```    
Step 2: System.debug(System.UserInfo.getSessionId().SubString(15)); //Bearer token
```

</br>
</br>

```
OrgAApex.cls
@RestResource(urlMapping='/Cases/*')
global with sharing class CaseManager { 
    @HttpGet
    global static List<Case> getCases() {

        List<Case> result =  [SELECT CaseNumber,Subject,Status,Origin,Priority
                        FROM Case LIMIT 10];
        return result;
       }   
    }  
```

</br>
</br>

# 2
# To get the data of perticular Object in Apex:   In org B access data Through Session Id:
## by using org A session id we can access the data of Org A in  Org B but it valid only for this session and get expires after that session and we need new session id so we use connected app to overcome this issue.

```
OrgBApex.cls
public class calldataobjectapi {
    @AuraEnabled
    public static List<Account> getCases() {
        Http http = new Http();
        HttpRequest req = new HttpRequest();
        req.setEndpoint('https://creative-shark-2j535e-dev-ed.trailblaze.my.salesforce.com/services/apexrest/Accounts');
        req.setMethod('GET');
        req.setHeader('Authorization', 'Bearer ' + '!AQUAQMFzH8y6zRejKOoxbgOwXPT5ZpNNTSFvcrToaVRd5KyzbyC5ZDkajjPznFiVrzFMf1kuNddm.pAae5g3Zk5kxKDVe1NV');

            HttpResponse res = http.send(req);

            if (res.getStatusCode() == 200) {
                
                System.debug(res.getBody());
                
                return (List<Account>)JSON.deserialize(res.getbody(), List<Account>.Class);
            } else {
                System.debug('HTTP request failed with status code: ' + res.getStatusCode());
            }
        return null;     }
}
```
