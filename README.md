# java-example
Examples for OpenFin Java adapter

## Run the example of connecting to OpenFin and creating applications

1. Clone this repository

2. Go to release directory and start run.bat

3. Once the java app starts, click on Start button, which should start OpenFin Runtime.  The java app will wait and try to connect to OpenFin Runtime.

4. Once OpenFin Runtime is started and Java app connects successfully,  "Create Application" button is enabled.  You can click on the button to bring up a dialog for entering configuration of any HTML5 app.  By default, the dialog is pre-populated with configuration for Hello OpenFin demo app.

5. You can use buttons in Window Control section to move and re-size HTML5 window of Hello OpenFin app.

6. Click "Create Application" button, which should start a dialog with all the fields pre-populated for our Hello OpenFin demo HTML5 application.  Just click on "Create" button.

7. After Hello OpenFin starts, you can use the buttons under Window Control of Java app to control Hello OpenFin window.

## Source Code Review

Source code for the example is located in /src/main/java/com/openfin/desktop/demo/OpenFinDesktopDemo.java.  The followings overview of how it communicates with OpenFin Runtime with API calls supported by the Java adapter:

1. Create connection object:

            this.desktopConnection = new DesktopConnection("OpenFinDesktopDemo");

    This code just creates an instance of DesktopConnection and it does not try to connect to runtime.

2. Launch and connect to stable version of OpenFin runtime:

            // create an instance of RuntimeConfiguration and configure Runtime by setting properties in RuntimeConfiguration
            this.runtimeConfiguration = new RuntimeConfiguration();
            // launch and connect to OpenFin Runtime
            desktopConnection.connect(this.runtimeConfiguration, listener, 10000);

   listener is an instance of DesktopStateListener which provides callback on status of connections to runtime.

3. Create new application when clicking on Create App:

        Application app = new Application(options, desktopConnection, new AckListener() {
            @Override
            public void onSuccess(Ack ack) {
                Application application = (Application) ack.getSource();
                application.run();   // run the app
            }
            @Override
            public void onError(Ack ack) {
            }
        });

   options is an instance of ApplicationOptions, which is populated from App Create dialog.  AckListener interface provides callback for the operation.

   Once the application is created successfully, you can take actions on its window:

4.  Change opacity:

                WindowOptions options = new WindowOptions();
                options.setOpacity(newOpacityValue);
                application.getWindow().updateOptions(options, null);

5. Change Window size

                application.getWindow().resizeBy(10, 10, "top-left");


6. Publishes messages to a topic with InterApplicationBus

            org.json.JSONObject message = createSomeJsonMessage();
            desktopConnection.getInterApplicationBus().publish("someTopic", message);

7. Subscribes to a topic with InterApplicationBus

                            desktopConnection.getInterApplicationBus().subscribe("*", "someTopic", new BusListener() {
                                public void onMessageReceived(String sourceUuid, String topic, Object payload) {
                                    JSONObject message = (JSONObject) payload;
                                }
                            });

## Run the example of docking Java Swing window with HTML5 application

1. Clone this repository

2. Go to release directory and start docking.bat

3. Once the java app starts, click on "Launch OpenFin" button, which should start OpenFin Runtime and "Hello OpenFin" HTML5 demo app.  The java app will wait and try to connect to OpenFin Runtime.

4. After clicking "Dock to HTML5 app" button, you can move either window to see docking effect.

5. Click "Undock from HTML5 app" to undock 2 windows

## Source Code Review for docking windows

Source code for the example is located in /src/main/java/com/openfin/desktop/demo/OpenFinDockingDemo.java.  This example uses Snap&Dock library from https://github.com/openfin/java-snap-and-dock

1. Create connection object:

            this.desktopConnection = new DesktopConnection("OpenFinDockingDemo", "localhost", port);

    This code just creates an instance and it does not try to connect to runtime.

2. Launch and connect to stable version of OpenFin runtime:

            desktopConnection.connectToVersion("stable", listener, 60);

   listener is an instance of DesktopStateListener which provides callback on status of connections to runtime.

3. Once Runtime is running, an instance of DockingManager is create with

            this.dockingManager = new DockingManager(this.desktopConnection, javaParentAppUuid);

4. Any OpenFin window can be registered with DockingManager with

            dockingManager.registerWindow(openFinWindow);

5. Any Java window can be registered with DockingManager with

            dockingManager.registerJavaWindow(javaWindowName, jFrame, AckListener);
            
6. An application can receive dock and undock events from DockingManger with

            desktopConnection.getInterApplicationBus().subscribe("*", "window-docked", EventListener);
            desktopConnection.getInterApplicationBus().subscribe("*", "window-undocked", EventListener);

7. An application can request DockingManager to undock a window with:

            JSONObject msg = new JSONObject();
            msg.put("applicationUuid", javaParentAppUuid);
            msg.put("windowName", javaWindowName);
            desktopConnection.getInterApplicationBus().publish("undock-window", msg);


Once the demo is running, Windows snap while being draggted close to other windows.  Snapped windows dock on mounse release. 

## More Info

More information and API documentation can be found at https://openfin.co/java-api/

## Getting help

Please contact support@openfin.co
