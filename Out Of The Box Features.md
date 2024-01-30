# get longitude and latitude of current user.

```
 handleCurrentLocation() {           // js function
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          const currentLocation = {
            latitude: position.coords.latitude,
            longitude: position.coords.longitude
          };

          this.Storelatitude = currentLocation.latitude;
          this.Storelongitude = currentLocation.longitude;

          console.log('Current Location:', this.Storelatitude);
          console.log('Current Location:', this.Storelongitude);
        },
        (error) => {
          console.error('Error getting current position:', error.message);
        }
      );
    } else {
      console.error('Geolocation is not supported by this browser.');
    }
  }
```



# To get current user ip address.
```
// Make a GET request to the ipify API
fetch('https://api.ipify.org?format=json')
  .then(response => response.json())
  .then(data => {
    // Log the user's IP address to the console
    console.log('Your IP address is ' + data.ip);
  })
  .catch(error => console.error('Error fetching IP address:', error));

```

# To get Location From Current User IP.

```
https://api.api-ninjas.com/v1/iplookup?address=182.69.215.112
```

# To get Current user city based on lattitude and longitude

```
callReverseGeocodeAPI(latitude, longitude) {
        const apiKey = 'VX6oGXMy9DXqliejI9vJ9g==W1qQt9Xmw5IOIoAE';
        const apiEndpoint = 'https://api.api-ninjas.com/v1/reversegeocoding';

        const apiUrl = `${apiEndpoint}?lat=${latitude}&lon=${longitude}`;

        fetch(apiUrl, {
            method: 'GET',
            headers: {
                'X-Api-Key': apiKey
            }
        })
            .then(response => {
                if (!response.ok) {
                    throw new Error(`Error: ${response.status} - ${response.statusText}`);
                }
                return response.text();
            })
            .then(data => {
                // console.log('API Response:', JSON.parse(JSON.stringify(data.name)));
                const response = JSON.parse(data);
                 this.StoreCurrentCityOfUser = response[0].name;
                console.log('City Name:', this.StoreCurrentCityOfUser);
            })
            .catch(error => {
                console.error('Error calling API:', error.message);
            });
    }

```
