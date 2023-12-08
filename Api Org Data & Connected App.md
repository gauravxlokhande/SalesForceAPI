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
public class SalesforceConnect {
    
    public static String getCases() {
        String endpoint = 'https://curious-goat-d8hj6v-dev-ed.trailblaze.my.salesforce.com/services/apexrest/Cases';  // org a my domain
        String token = '!AQcAQLVY0p631frI0pSSYsFMdPjRv0TuuKKh6lXKogcsA6hiXWqsGJNPWNIhHUwk.SSsgHGt3tRuYqIfnLvvymw.KNGQHXLG'; // org a acces token

        Http http = new Http();
        HttpRequest req = new HttpRequest();
        req.setEndpoint(endpoint);
        req.setMethod('GET');
        req.setHeader('Authorization', 'Bearer ' + token);

            HttpResponse res = http.send(req);

            if (res.getStatusCode() == 200) {
                System.debug(res.getBody());
                return res.getBody();
            } else {
                System.debug('HTTP request failed with status code: ' + res.getStatusCode());
            }
        return null;     }
}
```


# To Access the Data From Another Org Through Connected App.

## Salesforce org 1:
  Step1:  Connected app 
    

## Salesforce org 2:
  
   Step 1:  auth provider
   Step 2: named credential
   To get url of org 1 go to : My Domain

## from above steps we can access the data of org 1 from org 2 also we can manipulate the data of org1 if we have an access.
   
