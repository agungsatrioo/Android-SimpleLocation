# Android-SimpleLocation

Utility class for easy access to the device location on Android

## Requirements

 * Android 2.2+

## Installation

 * Add this library to your project
   * Declare the Gradle repository in your root `build.gradle`

     ```gradle
     allprojects {
         repositories {
             maven { url "https://jitpack.io" }
         }
     }
     ```

   * Declare the Gradle dependency in your app module's `build.gradle`

     ```gradle
     dependencies {
         compile 'com.github.delight-im:Android-SimpleLocation:v1.0.1'
     }
     ```

## Usage

### Decide for the required granularity

 * If you want to get the device's location with fine granularity (between 2m and 100m precision), GPS will be required. This consumes more battery but is most precise.
 * If you want to get the device's location with coarse granularity only (precise to several hundred meters), the location will be retrieved from the network (Wi-Fi and cell towers). This saves battery but is less precise.

### Add the required permissions

For fine location (GPS location), add the following permission in your `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

For coarse location (network location), add the following permission in your `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

### Retrieve the location from the device

```java
public class MyActivity extends Activity {

    private SimpleLocation location;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // ...

        // construct a new instance of SimpleLocation
        location = new SimpleLocation(this);

        // if we can't access the location yet
        if (!location.hasLocationEnabled()) {
            // ask the user to enable location access
            SimpleLocation.openSettings(this);
        }

        findViewById(R.id.someView).setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                final double latitude = location.getLatitude();
                final double longitude = location.getLongitude();

                // TODO
            }

        });
    }

    @Override
    protected void onResume() {
        super.onResume();

        // make the device update its location
        location.beginUpdates();

        // ...
    }

    @Override
    protected void onPause() {
        // stop location updates (saves battery)
        location.endUpdates();

        // ...

        super.onPause();
    }

}
```

### Getting notified about location updates

```java
location.setListener(new SimpleLocation.Listener() {

    public void onPositionChanged() {
        // new location data has been received and can be accessed
    }

});
```

### Extended constructor options

```java
Context context = this;
boolean requireFineGranularity = false;
new SimpleLocation(context, requireFineGranularity);

// or

Context context = this;
boolean requireFineGranularity = false;
boolean passiveMode = false;
new SimpleLocation(context, requireFineGranularity, passiveMode);

// or

Context context = this;
boolean requireFineGranularity = false;
boolean passiveMode = false;
long updateIntervalInMilliseconds = 10 * 60 * 1000;
new SimpleLocation(context, requireFineGranularity, passiveMode, updateIntervalInMilliseconds);

// or

Context context = this;
boolean requireFineGranularity = false;
boolean passiveMode = false;
long updateIntervalInMilliseconds = 10 * 60 * 1000;
boolean requireNewLocation = false;
new SimpleLocation(context, requireFineGranularity, passiveMode, updateIntervalInMilliseconds, requireNewLocation);
```

### Retrieving available data

```java
SimpleLocation.Point position = location.getPosition();
// double latitude = position.latitude;
// and
// double longitude = position.longitude;

// or

double latitude = location.getLatitude();
// and
double longitude = location.getLongitude();

// or

float speed = location.getSpeed();

// or

double altitude = location.getAltitude();
```

### Calculating the distance between two locations

```java
double startLatitude = 59.95;
double startLongitude = 30.3;
double endLatitude = 44.84;
double endLongitude = -0.58;
location.calculateDistance(startLatitude, startLongitude, endLatitude, endLongitude);

// or

Point startPoint = new SimpleLocation.Point(59.95, 30.3);
Point endPoint = new SimpleLocation.Point(44.84, -0.58);
location.calculateDistance(startPoint, endPoint);
```

### Blurring the location (for privacy reasons)

```java
// reduce the precision to 10,000m (for privacy reasons)
int blurRadiusInMeters = 10000;
location.setBlurRadius(blurRadiusInMeters);
```

## License

```
Copyright (c) delight.im <info@delight.im>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
