# Salesforce Org A : Connected App Org A

## Basic Information 
```
Step 1: connect app name: hotel management system

Step 2: contact Email : info@hotel.com

```

## Api (Enable OAuth Settings)
```
Enable OAuth Settingd: Checked

CallBack URL: Step 1: localhost:3000 // gave dummy url till oauth setting created in org B
              Step 2: paste callback url from org B

Selected OAuth Scopes:
1. Perform requests on your behalf at any time (refresh_token etc..)
2. Access and Manage Your data (api)
3. full access (full)

```

## Save Connected App.


## Expose Data From Org A for get accessible in org b

```
@RestResource(UrlMapping='/DataAvailable/*')
Global class exposedata {

    @httpget
    global static List<Contact> getcontact(){
        List<Contact> conList =[Select Id, FirstName From Contact];
        return conList;
    }
}
```


# Salesforce Org B: access org A data in B

## Auth Provider
```
Provider Type: Salesforce
```

## Auth Provider Edit
```
Provider Type : Salesforce
Name: access data from org A
Consumer Key: // generate when connected app created. get from org A connected App
Consumer Secreat:  // generate when connected app created. get from org A connected App
Default Scopes: refresh_token full
```

## Save Auth Provider
```
after save we get Callback url
```

## create Named Credentials
<p>to create name credentials select "New Legacy"</p>

```
Create Named Creadential: New Legacy
Label : SalesforceorgAconnect
Url: my Domain
Identity Type: Named Principal
Authentication Protocol: OAuth 2.0
Authentication Provider: Salesforce Org A
Start Authentication flow on save: checked
```

## To Get Base Url OF Org A:
<p>Go in org A and go in my domain copy url like below.</p>

```
https://prepscart-dev-ed.develop.my.salesforce.com
```

##  Save
```
login by entering org A username and pass
```

## Done


 ## Consuming Data from org A and accessing in Org B:
 
```
public class calldataobjectapid {
    @AuraEnabled
    public static String getCases() {
        Http http = new Http();
        HttpRequest req = new HttpRequest();
        req.setEndpoint('callout:Prepscart_Named_Credentials/services/apexrest/DataAvailable');
        req.setMethod('GET');
            HttpResponse res = http.send(req);
            if (res.getStatusCode() == 200) {
                System.debug(res.getBody());
                return res.getBody();
            } else {
                System.debug('HTTP request failed with status code: ' + res.getStatusCode());
            }
        return null;     
    }
}
```

```
req.setEndpoint('callout:Prepscart_Named_Credentials/services/apexrest/DataAvailable'); // SalesforceorgAconnect is a named credentials
```

