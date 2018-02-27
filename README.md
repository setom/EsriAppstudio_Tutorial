# Toggling Basemaps in Esri AppStudio Apps
This tutorial will take you through creating a ver basic ESRI appstudio app that allows a user to toggle the basemap of an ESRI AppStudio app. 
Only rudimentary coding skills are needed to complete this tutorial. All source code can be found in this repository. 

## Getting Started
In order to complete this tutorial you must have downloaded and installed ESRI Appstudio. Applicable ESRI licenses may be required, contact your local IT department if you require licenses. 
Download Appstudio [HERE]( https://doc.arcgis.com/en/appstudio/download/) and select the appropriate operating system.  
The screenshots in this tutorial are from a Windows 10 system, Mac users may have different UI. 
## Hello World!
Now that you have AppStudio installed and running, it’s time to start it up and make our first app! Open Appstudio and select New App > Starter > Hello World (Runtime)
In the upper right, name your app Basemap Tutorial
Click Create. 
HINT: A secret of programming is that setting up the development environment is often the hardest part! Unfortunately, every computer is different, and it’s impossible to write a single set of instructions for every problem that you might encounter. So don’t get down if you have a hard time getting it set up. Talk to a friend or IT department if you get really stuck. It happens to everyone!

[Hello World](EsriAppstudio_Tutorial/Images/HelloWorld.PNG)

Once your app has been created, you can run it by double clicking it. The emulator should open your app! You should see a map, with a few buttons, and the name of your app at the top. Congratulations! You’ve just made your first app!
Feel free to play with your app, the Home and Locator button functionality comes built into the Hello World template. You can zoom in and out or you can slip the map around to view different areas!
Close the emulator when you are finished. 

## What About the Code?!
The Hello World app is a great place to start, but let’s dig into the source code to see what’s happening.  Select your app in AppStudio, and then click on Edit on the right-hand side. This will open your app in QT Creator. 
When QT Creator opens, you should see the source code for your app in the pane. 
DON’T FREAK OUT! Coding can be intimidating, but we’re going to walk through and see exactly what’s going on.  
At the very top of your file you’ll see the imported libraries. These are the specialized code libraries that let your app interact with ESRI and the mobile device. To get the basemap to toggle, we’ll need to import a few more libraries. Add the following libraries to the import list:

```
import QtQuick.Controls.Styles 1.4
import QtQuick.Controls.Material 2.1
import QtGraphicalEffects 1.0
```

Now let’s continue exploring your app!
The next section of the code describes what the app will look like and do when it’s running, for example, this describes what the size of the app is when it first opens.

```
    width: 414
    height: 736
```

As we continue to scroll down the app, we see some different components of the app. Remember the title bar from our app? Here’s what it looks like in QT code: 

```
//header bar
    Rectangle {
        id: titleRect

        anchors {
            left: parent.left
            right: parent.right
            top: parent.top
        }

        height: 50 * AppFramework.displayScaleFactor
        color: app.info.propertyValue("titleBackgroundColor", "purple")

        Text {
            id: titleText

            anchors.centerIn: parent

            text: app.info.title
            color: app.info.propertyValue("titleTextColor", "white")
            font {
                pointSize: 18
            }
            wrapMode: Text.WrapAtWordBoundaryOrAnywhere
            maximumLineCount: 2
            elide: Text.ElideRight
            horizontalAlignment: Text.AlignHCenter
        }
    }
```

That’s a lot of text for a simple header bar, but there’s a lot going on there! You can mess around with that if you like. Try changing the text color to black!
HINT: find the color attribute of the text, and change its value!
Once you have made the change you want, test your change. Save your code (Ctrl+s), and then go to AppStudio and double click your app. If everything goes well your app will start up with the new text color! If there’s a problem AppStudio will give you a notification that says what the problem is, and which line of code you need to look at to fix it. 

## Let’s Start Mapping!
That’s all fine and good, but these are ESRI apps, so lets add maps!
Not surprisingly, the code for the Map is in the section called “MapView” Keep scrolling down until you find the “Map”

```
// add a basemap
        Map{
            id:map

            BasemapTopographic{}
            initialViewpoint: ViewpointCenter {
                id:initialViewpoint
                center: Point {
                    x: -11e6
                    y: 6e6
                    spatialReference: SpatialReference {wkid: 102100}
                }
                targetScale: 9e7
            }
        }
```
Hopefully you can see some of the different properties of a map. The spatial reference is wkid: 102100, which is code for Web Mercator. You can see different projection codes [HERE]( https://developers.arcgis.com/rest/services-reference/projected-coordinate-systems.htm). 
If we keep going through the code we see the map control buttons. Let’s take a closer look at one of them so we can see how they work. 
```
            Button{
                id:locationButton
                Image{
                    source:"./images/location.png"
                    height: 30 * scaleFactor
                    width: height
                    anchors.centerIn: parent
                }
                onHoveredChanged: hovered ? locationButton.opacity = 1 : locationButton.opacity = .5;
                height: 40 * scaleFactor
                width : height
                opacity: .5

                onClicked: {
                    if (!mapView.locationDisplay.started) {
                        mapView.locationDisplay.start()
                        mapView.locationDisplay.autoPanMode = Enums.LocationDisplayAutoPanModeRecenter
                    } else {
                        mapView.locationDisplay.stop()
                    }
                }

            }
```

The code above is the Locator button. The IMAGE section tells the program which picture to use for the button, and how big the button should be. This button also has a property called onHoveredChange, which describes what happens to the button when the mouse hovers over it. In this case the opacity changes. 
Below that is the onClicked section. This section tells the program what to do when the button is clicked. This button toggles the location on and off. So first the button figures out if the location beacon is already on or not. If it is NOT already on, it finds the location, then pans the map to that spot! If the location is already running, it turns the beacon off. Using pseudocode, it works like this:

```
onClicked: {
	if (the beacon is off): {
		find my location
		Zoom the map to that location
	}
	Else the beacon must be on: {
		Turn the beacon off
	}
} 
```

## Changing the Basemap
Sometimes users want to see different basemaps in their apps! So let’s give them the opportunity to change it! The first thing we’ll need is a dropdown menu for the user to select the basemap from. These dropdowns are also called Combo Boxes. 
At the bottom of your code find the last curly brace }
Directly above it, paste the following code: 

```
ComboBox {
        id: comboBoxBasemap
        anchors {
            left: parent.left
            top: parent.top
            margins: 15 * scaleFactor
        }
        width: 140 * scaleFactor
        height: 30 * scaleFactor
        Material.accent:"#8f499c"
        background: Rectangle {
            radius: 6 * scaleFactor
            border.color: "darkgrey"
            width: 140 * scaleFactor
            height: 30 * scaleFactor
        }

        model: ["Topographic","Streets","Imagery","Oceans"]
        onCurrentTextChanged: {
            // Call this JavaScript function when the current selection changes
            if (map.loadStatus === Enums.LoadStatusLoaded)
                changeBasemap();
        }
}
```

This should look very similar to the button code we just looked at! We can see where the combo box should be placed, what it’s background is and other properties. The MODEL property is the different choices that will be available to the user. 
Finally, the onCurrentTextChanged property acts just like the onClicked function from the button. Whenever the text in the Combo box is changed, this part of the code runs. For this combo box, it will run the changeBasemap() function. 

### Where’s the changeBasemap() function?!
We haven’t written it yet! Let’s add it now. 
At the bottom of the code find the last two curly braces. 
Directly above the last two braces, paste the following code: 

```
function changeBasemap() {
            // Determine the selected basemap, create that type, and set the Map's basemap
            switch (comboBoxBasemap.currentText) {
            case "Topographic":
                map.basemap = ArcGISRuntimeEnvironment.createObject("BasemapTopographic");
                break;
            case "Streets":
                map.basemap = ArcGISRuntimeEnvironment.createObject("BasemapStreets");
                break;
            case "Imagery":
                map.basemap = ArcGISRuntimeEnvironment.createObject("BasemapImagery");
                break;
            case "Oceans":
                map.basemap = ArcGISRuntimeEnvironment.createObject("BasemapOceans");
                break;
            default:
                map.basemap = ArcGISRuntimeEnvironment.createObject("BasemapTopographic");
                break;
            }
        }
```

This code tells the combobox what to do whenever the text is changed. It checks what the new selection is, and then changes the basemap to the selected choice!
Remember the MODEL property of the comboBox? Each of those choices is listed here, and each one has a slightly different function, can you see the difference between the Streets and Imagery choices?
Save your code (Ctrl + S) and then go to AppStudio. Run your app by double-clicking it in the list. If there are no problems, the app should open. If there are any issues, AppStudio will pop a notification and tell you which line you need to look at in your code. 

## What Happened?!
Uh oh. 
The app looks pretty good, except my comboBox is in the wrong spot!
{SCREENSHOT]
This is because we told the combo box it was part of the app, but we actually wanted it to be part of the map. Remember how the MAPVIEW had the buttons inside it? We need to do the same thing for the combo box. 
Go back to your code and select the entire ComboBox block of code (roughly line 165-209). Move that entire block so that it is INSIDE the MapView. You can put it right above the COLUMN section at roughly line 106. 
Once that code is moved, save your code again and try running the app. 

HINT: If you keep getting errors when you try to run it, the first step is to make sure that all of your curly braces match up properly. Remember, for every `{` there should be a matching `}` somewhere below it. 

## Hooray!
If everything worked you should be looking at a fancy app that lets the user select their basemap from a combo box!
Try changing the basemap a few times in your app to make sure everything works properly!

This tutorial was generated using the starter templates from ESRI AppStudio, changes have been made for simplicity and code readability. 











