# Get Current User Location: Longitude and Latitude

## Apex
```
public class LocationController {
    @AuraEnabled
    public static String saveUserLocation(Double latitude, Double longitude) {
        // Perform logic to save the location to Salesforce records or objects
        // Example: Save to a custom object named UserLocation__c
        UserLocation__c locationRecord = new UserLocation__c(Latitude__c = latitude, Longitude__c = longitude);
        insert locationRecord;

        return 'Location saved successfully';
    }
}

```


## HTML

```
    <template if:true={getlocation}>
        <!-- getGeolocation.html -->

        <lightning-card title="User Location">
            <div class="slds-m-around_medium">
                <p>{locationMessage}</p>
            </div>
        </lightning-card>
    </template>
```


## JS
```
// getGeolocation.js
  @track getlocation = true;
    locationMessage = '';

    connectedCallback() {
        // Trigger the function to get user location on component initialization
        this.getLocation();
    }

    getLocation() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                (position) => {
                    const latitude = position.coords.latitude;
                    const longitude = position.coords.longitude;
                    this.locationMessage = `Latitude: ${latitude}, Longitude: ${longitude}`;
                },
                (error) => {
                    this.handleError(error);
                }
            );
        } else {
            this.locationMessage = 'Geolocation is not supported by this browser.';
        }
    }

    handleError(error) {
        switch (error.code) {
            case error.PERMISSION_DENIED:
                this.locationMessage = 'User denied the request for Geolocation.';
                break;
            case error.POSITION_UNAVAILABLE:
                this.locationMessage = 'Location information is unavailable.';
                break;
            case error.TIMEOUT:
                this.locationMessage = 'The request to get user location timed out.';
                break;
            case error.UNKNOWN_ERROR:
                this.locationMessage = 'An unknown error occurred.';
                break;
        }
    }


```
