---

# 🌤️ SOAP Weather API Integration with Apex (Salesforce)

This example demonstrates how to make a **SOAP callout to a weather API** from Apex, how to **construct the SOAP XML using `escapeXml()`**, and how to **parse the XML response** using `XmlStreamReader`.

---

## 📡 API Used

**WebServiceX Weather by ZIP** (Legacy example)

> ⚠️ *Note: This is for learning purposes. WebServiceX APIs are now deprecated — replace with a working SOAP weather API or mock it in tests.*

---

## 📨 SOAP Request Structure

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:web="http://www.webserviceX.NET">
   <soapenv:Header/>
   <soapenv:Body>
      <web:GetCityWeatherByZIP>
         <web:ZIP>10001</web:ZIP>
      </web:GetCityWeatherByZIP>
   </soapenv:Body>
</soapenv:Envelope>
```

---

## 🧪 Apex Implementation

### 📦 `WeatherResult` Class

```apex
public class WeatherResult {
    public String City;
    public String State;
    public String Temperature;
    public String Description;
    public String Success;
}
```

---

### ⚙️ `SoapWeatherService` Apex Class

```apex
public class SoapWeatherService {
    
    public static WeatherResult getWeatherByZip(String zipCode) {
        // Safely escape any XML characters in ZIP (e.g. &, <, >)
        String safeZip = zipCode.escapeXml();

        // Construct the SOAP XML body with escaped input
        String soapBody = '<?xml version="1.0" encoding="utf-8"?>' +
            '<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" ' +
            'xmlns:web="http://www.webserviceX.NET">' +
            '<soapenv:Header/>' +
            '<soapenv:Body>' +
            '<web:GetCityWeatherByZIP>' +
            '<web:ZIP>' + safeZip + '</web:ZIP>' +
            '</web:GetCityWeatherByZIP>' +
            '</soapenv:Body>' +
            '</soapenv:Envelope>';

        // Prepare HTTP request
        HttpRequest req = new HttpRequest();
        req.setEndpoint('http://www.webservicex.net/uszip.asmx');
        req.setMethod('POST');
        req.setHeader('Content-Type', 'text/xml; charset=utf-8');
        req.setHeader('SOAPAction', 'http://www.webserviceX.NET/GetCityWeatherByZIP');
        req.setBody(soapBody);

        // Send HTTP callout
        Http http = new Http();
        HttpResponse res = http.send(req);

        // Handle response
        if(res.getStatusCode() == 200) {
            return parseWeatherResponse(res.getBody());
        } else {
            throw new CalloutException('SOAP call failed: ' + res.getStatus());
        }
    }

    // Parse XML response into Apex class
    private static WeatherResult parseWeatherResponse(String responseXml) {
        WeatherResult result = new WeatherResult();
        XmlStreamReader reader = new XmlStreamReader(responseXml);

        while(reader.hasNext()) {
            if(reader.getEventType() == XmlTag.START_ELEMENT) {
                String tag = reader.getLocalName();
                reader.next();

                if(reader.getEventType() == XmlTag.CHARACTERS) {
                    String value = reader.getText();
                    if(tag == 'Success') result.Success = value;
                    else if(tag == 'City') result.City = value;
                    else if(tag == 'State') result.State = value;
                    else if(tag == 'Temperature') result.Temperature = value;
                    else if(tag == 'Description') result.Description = value;
                }
            }
            reader.next();
        }
        return result;
    }
}
```

---

##  Sample SOAP Response (Success)
```
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <GetCityWeatherByZIPResponse xmlns="http://www.webserviceX.NET">
         <GetCityWeatherByZIPResult>
            <Success>true</Success>
            <ResponseText>City Found</ResponseText>
            <State>NY</State>
            <City>New York</City>
            <WeatherStationCity>New York</WeatherStationCity>
            <WeatherID>1</WeatherID>
            <Description>Sunny</Description>
            <Temperature>75 F (24 C)</Temperature>
            <RelativeHumidity>50%</RelativeHumidity>
            <Wind>10 mph</Wind>
            <Pressure>1012 mb</Pressure>
            <Visibility>10 miles</Visibility>
            <WindChill />
            <Remarks>Clear sky with bright sun</Remarks>
         </GetCityWeatherByZIPResult>
      </GetCityWeatherByZIPResponse>
   </soap:Body>
</soap:Envelope>
```

## 🧪 Sample Usage

```apex
WeatherResult result = SoapWeatherService.getWeatherByZip('10001');
System.debug('City: ' + result.City);
System.debug('State: ' + result.State);
System.debug('Temperature: ' + result.Temperature);
System.debug('Conditions: ' + result.Description);
System.debug('Success: ' + result.Success);
```

---

## 🛡️ Why Use `escapeXml()`?

```apex
String safeZip = zip.escapeXml();
```

* Ensures that special characters (like `&`, `<`, `>`, `'`, `"`) do not break the SOAP structure.
* Prevents malformed XML and injection issues.

For example:

```apex
// Example: How escapeXml() protects XML from invalid characters

String original = '10001 & Co. <Weather> "New York"';
String escaped = original.escapeXml();

System.debug('Original: ' + original);
// Output: Original: 10001 & Co. <Weather> "New York"

System.debug('Escaped: ' + escaped);
// Output: Escaped: 10001 &amp; Co. &lt;Weather&gt; &quot;New York&quot;
```

---

## ✅ Output (Example)

```plaintext
City: New York
State: NY
Temperature: 75 F
Conditions: Sunny
Success: true
```

---

---

# 🧪 Salesforce Apex Test Class for SOAP Weather API

This guide shows how to write a **unit test for a SOAP callout** using `HttpCalloutMock` in Apex. It demonstrates mocking a SOAP response and verifying that the integration code correctly parses and handles the response.

---

## ✅ Why Use Mocks for SOAP?

* Apex unit tests **cannot make real callouts**.
* Salesforce provides the `HttpCalloutMock` interface to simulate external HTTP responses.
* This ensures your **logic can be tested with 100% coverage** without hitting a real SOAP API.

---

## 📄 Test Class: `SoapWeatherServiceTest`

```apex
@isTest
private class SoapWeatherServiceTest {

    /**
     * Custom mock class to simulate a SOAP weather API response.
     */
    private class MockHttpResponseGenerator implements HttpCalloutMock {
        public HTTPResponse respond(HTTPRequest req) {
            HttpResponse res = new HttpResponse();
            res.setHeader('Content-Type', 'text/xml');

            // Simulated SOAP response from weather API
            String soapResponse = 
                '<?xml version="1.0" encoding="utf-8"?>' +
                '<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">' +
                '  <soap:Body>' +
                '    <GetCityWeatherByZIPResponse xmlns="http://www.webserviceX.NET">' +
                '      <GetCityWeatherByZIPResult>' +
                '        <Success>true</Success>' +
                '        <City>New York</City>' +
                '        <State>NY</State>' +
                '        <Temperature>75 F</Temperature>' +
                '        <Description>Sunny</Description>' +
                '      </GetCityWeatherByZIPResult>' +
                '    </GetCityWeatherByZIPResponse>' +
                '  </soap:Body>' +
                '</soap:Envelope>';

            res.setBody(soapResponse);
            res.setStatusCode(200);
            return res;
        }
    }

    /**
     * Unit test to verify parsing logic from a SOAP response.
     */
    @isTest
    static void testWeatherByZip() {
        // Register mock
        Test.setMock(HttpCalloutMock.class, new MockHttpResponseGenerator());

        // Execute logic under test
        Test.startTest();
        SoapWeatherService.WeatherResult result = SoapWeatherService.getWeatherByZip('10001');
        Test.stopTest();

        // Assertions to validate response parsing
        System.assertEquals('true', result.Success);
        System.assertEquals('New York', result.City);
        System.assertEquals('NY', result.State);
        System.assertEquals('75 F', result.Temperature);
        System.assertEquals('Sunny', result.Description);
    }
}
```

---

## 📦 What This Covers

| ✅ Feature               | Description                                                    |
| ----------------------- | -------------------------------------------------------------- |
| `HttpCalloutMock` usage | Simulates external SOAP call                                   |
| Response structure      | Matches real SOAP XML structure with `<soap:Envelope>` wrapper |
| Unit test coverage      | Ensures response parsing logic is validated                    |
| Safe and reliable       | No real API call — works in all Salesforce orgs                |

---

## 🧠 Pro Tip: Escaping XML in Requests

When building your SOAP request body in Apex, always use `.escapeXml()` for dynamic values:

```apex
String zip = '10001 & Co.';
String escapedZip = zip.escapeXml(); // Results: "10001 &amp; Co."
```

This prevents XML injection and malformed payloads.

---

## 🧪 Output from Test Run (Debug Logs)

```plaintext
City: New York
State: NY
Temperature: 75 F
Conditions: Sunny
Success: true
```

---

