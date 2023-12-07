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
