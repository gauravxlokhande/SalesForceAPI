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
Label : SalesforceorgAconnect
Url: my Domain
Identity Type: Named Principal
Authentication Protocol: OAuth 2.0
Authentication Provider: Salesforce Org A
Start Authentication flow on save: checked
```

## To Get Base Url OF Org A:
go in org a and refer
```
https://github.com/gaurravlokhande/SalesForceAPI/blob/main/Call%20Data%20Of%20Anothe%20Org%20Object%20Through%20Object%20API.md#to-get-the-data-of-perticular-object-in-postman-in-org-a-access-data-through-session-id
```
##  Save
```
login by entering org A username and pass
```
#Done

```
req.setEndpoint('callout:SalesforceorgAconnect/services/apexrest/Cases'); // SalesforceorgAconnect is a named credentials
```

