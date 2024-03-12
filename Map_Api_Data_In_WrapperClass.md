# Map Api data in an Wrapper Class

## Most Most Most Importent Note
 for below responce:

 

 
```
{        // if see curly braces then take Map<String, Object> mapofobj = (Map<String, Object>) casthere;
  "index_name": "6141ea17-a69d-4713-b600-0a43c8fd9a6c",
  "title": "Current daily price of various commodities from various markets (Mandi)",
  "records": [      // if see array containing multiple object take List<Object> listobj =(List<Object>) casthere;  now this array contains multiple object inside it. iterate over array and asign obj to map : for (Object obj: listobj) 

    {                    // so here we see curlybrace use map   Map<String , Object> firstobj = (Map<String, Object>)obj;  then we are able to access the values now.
      "state": "Bihar",
      "district": "Darbhanga",
      "market": "Bahadurpur (Ekmi Ghat)",
      "commodity": "Onion",
      "arrival_date": "17/12/2023",
      "min_price": 3500,
      "max_price": 3800
    },
    {
      "state": "Punjab",
      "district": "Amritsar",
      "market": "Amritsar (India Gate)",
      "commodity": "Potato",
      "arrival_date": "16/12/2023",
      "min_price": 3200,
      "max_price": 3500
    }
  ]
}
```




## Map Api data in an Wrapper Class to reduce the complexity of acceccing data while accessing in javascript.

## In below format we getting the data from api:
![image](https://github.com/gaurravlokhande/SalesForceAPI/assets/119065314/3b366c5c-c033-455f-acee-85249c0b7007)

## MApped above api data and reduce complexness.
```
public class WeatherDetailsClass {
    @AuraEnabled
    public static WeatherDetailsWrapper getWeatherDetails(String cityName){
        //Frame the Enpoint URL
        String apiKey = 'ab4a188555840adac4da0dcd68d4cf95';
        String endpoint = 'http://api.openweathermap.org/data/2.5/weather';
        endpoint += '?q='+cityName;
        endpoint += '&units=metric';
        endpoint += '&APPID='+apiKey;
        system.debug('endPoint URL=> '+endpoint);
        
        //Callout to Weather API
        Http http =new http();
        HttpRequest req = new HttpRequest();
        req.setEndpoint(endpoint);
        req.setMethod('GET');
        HttpResponse res = http.send(req);
        system.debug('response status=> '+res);
        system.debug('response body=> '+JSON.deserializeUntyped(res.getBody()));
        
        //return the weather details in wrapper form
        WeatherDetailsWrapper weatherDet = new WeatherDetailsWrapper();
        if(res.getStatusCode() == 200){
            Map<String,object> result = (Map<String,Object>)JSON.deserializeUntyped(res.getBody());
            weatherDet.city = String.valueOf(result.get('name'));
            Map<String,object> mainResult = (Map<String,object>)(result.get('main'));
            weatherDet.temperature = String.valueOf(mainResult.get('temp'));
            weatherDet.pressure = String.valueOf(mainResult.get('pressure'));
            weatherDet.humidity = String.valueOf(mainResult.get('humidity'));
            weatherDet.feelsLike = String.valueOf(mainResult.get('feels_like'));
            weatherDet.tempMin = String.valueOf(mainResult.get('temp_min'));
            weatherDet.tempMax = String.valueOf(mainResult.get('temp_max'));
        }
        system.debug('weather details to return=> '+weatherDet);
        return weatherDet;
        
    }
    
    //Wrapper class to store weather details in serial manner
    public class WeatherDetailsWrapper {
        @AuraEnabled public String city {get;set;}
        @AuraEnabled public String temperature {get;set;}
        @AuraEnabled public String pressure {get;set;}
        @AuraEnabled public String humidity {get;set;}
        @AuraEnabled public String feelsLike {get;set;}
        @AuraEnabled public String tempMin {get;set;}
        @AuraEnabled public String tempMax {get;set;}
    }
    
}
```


Note: the data provided by above method can directly accissable using data.city  through imparitive methods in javascript.


## Sample Data
```
{
  "index_name": "6141ea17-a69d-4713-b600-0a43c8fd9a6c",
  "title": "Current daily price of various commodities from various markets (Mandi)",
  "records": [
    {
      "state": "Bihar",
      "district": "Darbhanga",
      "market": "Bahadurpur (Ekmi Ghat)",
      "commodity": "Onion",
      "arrival_date": "17/12/2023",
      "min_price": 3500,
      "max_price": 3800
    },
    {
      "state": "Punjab",
      "district": "Amritsar",
      "market": "Amritsar (India Gate)",
      "commodity": "Potato",
      "arrival_date": "16/12/2023",
      "min_price": 3200,
      "max_price": 3500
    }
  ]
}
```

## Apex class for wrapping above data

```
public class WeatherDetailsClass {
    @AuraEnabled
    public static List<WeatherRecordWrapper> getWeatherDetails(String cityName){
        //Frame the Endpoint URL
        String apiKey = '579b464db66ec23bdd000001be46e8b8b04c4b746f8c908419d2c4e3';
        String endpoint = 'https://api.data.gov.in/catalog/6141ea17-a69d-4713-b600-0a43c8fd9a6c?';
        endpoint += 'api-key='+apiKey;
        endpoint += '&format=json&filters%5Bstate%5D='+cityName;
        system.debug('Endpoint URL=> '+endpoint);
        
        //Callout to Weather API
        Http http = new Http();
        HttpRequest req = new HttpRequest();
        req.setEndpoint(endpoint);
        req.setMethod('GET');
        HttpResponse res = http.send(req);
        system.debug('Response status=> '+res);
        system.debug('Response body=> '+res.getBody());

        // Parse and wrap the records
        List<WeatherRecordWrapper> recordsList = new List<WeatherRecordWrapper>();
        if(res.getStatusCode() == 200){
            Map<String, Object> responseMap = (Map<String, Object>) JSON.deserializeUntyped(res.getBody());
            List<Object> records = (List<Object>) responseMap.get('records');
            
            for (Object record : records) {
                Map<String, Object> recordMap = (Map<String, Object>) record;
                WeatherRecordWrapper recordWrapper = new WeatherRecordWrapper();
                recordWrapper.state = (String) recordMap.get('state');
                recordWrapper.district = (String) recordMap.get('district');
                recordWrapper.market = (String) recordMap.get('market');
                recordWrapper.commodity = (String) recordMap.get('commodity');
                recordWrapper.arrivalDate = (String) recordMap.get('arrival_date');
                recordWrapper.minPrice = (Decimal) recordMap.get('min_price');
                recordWrapper.maxPrice = (Decimal) recordMap.get('max_price');
                recordsList.add(recordWrapper);
            }
        }
        system.debug('Records to return=> '+recordsList);
        return recordsList;
    }
    
    // Wrapper class for weather record
    public class WeatherRecordWrapper {
        @AuraEnabled public String state {get;set;}
        @AuraEnabled public String district {get;set;}
        @AuraEnabled public String market {get;set;}
        @AuraEnabled public String commodity {get;set;}
        @AuraEnabled public String arrivalDate {get;set;}
        @AuraEnabled public Decimal minPrice {get;set;}
        @AuraEnabled public Decimal maxPrice {get;set;}
    }
}
```
