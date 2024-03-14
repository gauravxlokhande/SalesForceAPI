```
public class CallAnimalApi {
    
    private static final String API_KEY = 'VX6oGXMy9DXqliejI9vJ9g==W1qQt9Xmw5IOIoAE';
    
    // Method to make a GET request with API key
    public static void makeGetRequestWithApiKey(String name) {
        HttpRequest request = new HttpRequest();
        request.setEndpoint('https://api.api-ninjas.com/v1/animals?name=' + EncodingUtil.urlEncode(name, 'UTF-8'));
        request.setMethod('GET');
        request.setHeader('X-Api-Key', API_KEY);
        
        Http http = new Http();
        HttpResponse resp = http.send(request);
        System.debug(resp.getBody());
    }
}
```
