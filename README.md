# Call Api Through Apex:

```
If we Call Api through Apex : Step 1
if We call through Javascript: Step 1 & Step 2
Step 1: Remote Site Setting : http://api.weatherstack.com
Step 2: Trusted Url : http://api.weatherstack.com
```


## Apex Code:

```
public class ApiCall {
    
    private static final String RECIPE_API = 'http://api.weatherstack.com';
    private static final String API_KEY = '5353014c571e6f581faccf6881cdaefd';

    
    @AuraEnabled
    public static String getRandomRecipe(String City) {
        Http http = new Http();
        HttpRequest request = new HttpRequest();
        request.setEndpoint(RECIPE_API + '/current?access_key=' + API_KEY+'&query='+'%'+City+'%');
        request.setMethod('GET');

        HttpResponse response = http.send(request);

        if (response.getStatusCode() == 200) {
            System.debug(response.getBody());
            return response.getBody();
        } 
         return '';
    }   
}
```


## Html Code:

```
<template>
    <lightning-input type="text" value={entercity} onchange={onchangecity} label="Enter City"></lightning-input>

    <lightning-button variant="base" label="Button Label" onclick={handleClickonbtn}></lightning-button>

    <template if:true={storeresult} for:each={storeresult} for:item="con" for:index="index">
        <li key={con.Id}>
            {con.location.name}
        </li>
    </template>

</template>
```


## JS Code:

```
import { LightningElement, track } from 'lwc';
import getApexData from '@salesforce/apex/ApiCall.getRandomRecipe';
export default class ApicallPage extends LightningElement {



    @track entercity;
    @track storeresult;

      onchangecity(event) {
        this.entercity = event.target.value; 
      }
    
  
    @track cityName;

    getdata() {
        getApexData({ City: this.entercity })
        .then((result) => {
            this.storeresult =JSON.parse(result)
          
            console.log(this.storeresult);
        }).catch((error) => {
            
        });
}

        handleClickonbtn(){
            this.getdata();
   }
      
}
```


# Call Api Without Apex Through JS

## Html Code: 
```
<template>
    <div>
        <h1>Weather Information</h1>
        <template if:true={weatherData}>
            <div>
                <p>Temperature: {weatherData.current.temperature}</p>
                <!-- Add more properties as needed -->
            </div>
        </template>
        <template if:false={weatherData}>
            <p>Loading...</p>
        </template>
    </div>
</template>
```

## JS Code:

```
import { LightningElement, track } from 'lwc';

export default class WeatherComponent extends LightningElement {
    @track weatherData;

    connectedCallback() {
        const endpoint = 'http://api.weatherstack.com/current?access_key=5353014c571e6f581faccf6881cdaefd&query=Amravati';

        fetch(endpoint)
            .then(response => {
                if (!response.ok) {
                    throw new Error('Network response was not ok');
                }
                return response.json();
            })
            .then(data => {
                this.weatherData = data;
            })
            .catch(error => {
                console.error('Error fetching weather data:', error);
            });
    }
}
```






-----------------------------------------------------------------------------------------------------------------------------------------

# Extra Http Class for Reference

```
/*
https://<your org url>/services/apexrest/RESTAPI

Request :

{
    "data": {
        "Name": "YT Hospital 2",
        "Address": "Delhi, India",
        "isActive": true,
        "NumOfEmployees": 140,
        "Turnover": 5000,
        "contactNumber": "9632555666"
    },
    "action": "insert"
}

*/
@RestResource(urlMapping='/RESTAPI/*')
global with sharing class RestAPIv3 {
    
    @HttpPost
    global static void doPost(){
        string jsonRequestStr = RestContext.request.requestBody.toString();
        string responseStr;
        boolean status;
        string response;
        RequestWrapper wrapper = RequestWrapper.parse(jsonRequestStr);
        //Create Record (you can change this hospital object to any object your org has)
        if(!string.isBlank(wrapper.action) && wrapper.action.equalsIgnoreCase('insert')){
            Hospital__c hospital = new Hospital__c();
            hospital.Name = wrapper.data.Name;
            hospital.Address__c = wrapper.data.Address;
            hospital.Contact_Number__c = wrapper.data.contactNumber;
            hospital.isActive__c = wrapper.data.isActive;
            hospital.Number_of_Employees__c = wrapper.data.NumOfEmployees;
            hospital.Turnover__c= wrapper.data.Turnover;
            insert hospital;
            responseStr = 'Your Record is Created with Record Id --> '+hospital.Id;
            status = true;
        }
        else{
            responseStr = 'Error --> Please make sure the action is insert only.';
            status = false;
        }
        //Get Response
        response = getResponse(status,responseStr);
        //Send Response
        RestContext.response.addHeader('Content-Type', 'application/json');
        RestContext.response.responseBody = blob.valueOf(response);
    }
    
    //Request Wrapper
    public class RequestWrapper {
        public cls_data data;
        public String action;	//update
    }
    //Inner Request Wrapper
    public class cls_data {
        public String Name;	//City Hospital 2
        public String Address;	//Pune, India
        public boolean isActive;
        public Decimal NumOfEmployees;	//4153
        public Decimal Turnover;	//94123
        public String contactNumber;	//9856412311
    }
    //Parse - Deseriliaze Request
    public static RequestWrapper parse(String json){
        return (RequestWrapper) System.JSON.deserialize(json, RequestWrapper.class);
    }
    //Response Wrapper
    public class ResponseWrapper {
        public boolean status;
        public String message;	//update
    }
    //Create Response
    public static string getResponse(boolean status, string msg){
        ResponseWrapper respWrapper = new ResponseWrapper();
        respWrapper.status = status;
        respWrapper.message = msg;
        return JSON.serialize(respWrapper);
    } 
}
```
