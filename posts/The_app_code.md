# The app
The app has different tasks. It records the gyroscope, accelerometer and orientation data. In addition, it writes a CSV-file and sends it to a FTP-Server. This overview is greatly reduced for better clarity. The full code has about 450 lines. You can download it  [here](https://github.com/MatheLi/BWKI/releases/tag/v1).

## Pictures
![](https://raw.githubusercontent.com/MatheLi/BWKI/master/res/Screenshot_app%20.png?raw=true)
![](https://raw.githubusercontent.com/MatheLi/BWKI/master/res/Screenshot_icon.png?raw=true)

## Create Files
The program was completely written by us.
In this part, we create a CSV-file. Furthermore, we name the different columns columns.
```
        File sdCard = Environment.getExternalStorageDirectory();
        File dir = new File(sdCard.getAbsolutePath() + "/sean");
        file = new File(dir, "output"+unixTimename+".csv");
   
        String entry = "timestamp,rel_time,acc_x,acc_y,acc_z,gyro_x,gyro_y,gyro_z,azimuth,pitch,roll\n";
        try {
            FileOutputStream f = new FileOutputStream(file, true);
            try {
                f.write(entry.getBytes());
                f.flush();
                f.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        
```
## Sensors
Here we get the sensor data and save it in an array.
```
        sensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
        accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        gyroscope=sensorManager.getDefaultSensor(Sensor.TYPE_GYROSCOPE);
        orientation = sensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION);
        
        public void onSensorChanged(SensorEvent event) {
            if (event.sensor.getType() == Sensor.TYPE_ACCELEROMETER)
                accelerometer_data = event.values;
            if (event.sensor.getType() == Sensor.TYPE_ORIENTATION)
                orientation_data = event.values;
            if (event.sensor.getType() == Sensor.TYPE_GYROSCOPE)
                gyroscope_data = event.values;
    }
```
## Buttons
We have to create a function for each button which registers/unregisters the sensors. As well we play a sound and start an extra thread.

```
public void stop(View v){
    off=1;
    sensorManager.unregisterListener(MainActivity.this, accelerometer);
    sensorManager.unregisterListener(MainActivity.this, orientation);
    sensorManager.unregisterListener(MainActivity.this, gyroscope);
}
public void start(View v) throws InterruptedException {
    off=0;
    final MediaPlayer mp = MediaPlayer.create(this, R.raw.startton);
    mp.start();
    TimeUnit.SECONDS.sleep(5);
    mp.start();
    sensorManager.registerListener(MainActivity.this, accelerometer, SensorManager.SENSOR_DELAY_FASTEST);
    sensorManager.registerListener(MainActivity.this, orientation, SensorManager.SENSOR_DELAY_FASTEST);
    sensorManager.registerListener(MainActivity.this, gyroscope, SensorManager.SENSOR_DELAY_FASTEST);
    Timer thread1 = new Timer(this);
    thread1.start();
}
```  
## Creating CSV-file    
In this part, we create an input String including Timestamp, time since the start of the recording and of course the data of the sensors. 
Directly after creating the String, we write it in the CSV-file.
```
        while (true) {
                try {
                    sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                timer_1 = (float) (timer_1 + 0.0005);

                float[] gyroscope_data = MainActivity.gyroscope_data;
                float[] accelerometer_data = MainActivity.accelerometer_data;
                float[] orientation_data = MainActivity.orientation_data;

                if (accelerometer_data != null && orientation_data != null && gyroscope_data != null) {
                    Log.d(TAG, "Accelometer X:  " + accelerometer_data[0] + "    Y: " + accelerometer_data[1] + "   Z:   " + accelerometer_data[2]);
                    Log.d(TAG, "Orientation  Azimuth :  " + orientation_data[0] + "    Pitch: " + orientation_data[1] + "   Roll:   " + orientation_data[2]);
                    Log.d(TAG, "Gyroscope X:  " + gyroscope_data[0] + "    Y: " + gyroscope_data[1] + "   Z:   " + gyroscope_data[2]);
                    final long unixTime = System.currentTimeMillis() / 1000L;

                    String entry = unixTime + "," + timer_2 + "," + accelerometer_data[0] + "," + accelerometer_data[1] + "," + accelerometer_data[2] + "," + gyroscope_data[0] + "," + gyroscope_data[1] + "," + gyroscope_data[2] + "," + orientation_data[0] + "," + orientation_data[1] + "," + orientation_data[2] + "\n";
                    
                    
              
try {
    FileOutputStream f = new FileOutputStream(file, true);
    try {

        f.write(entry.getBytes());
        f.flush();
        f.close();

    } catch (IOException e) {
        e.printStackTrace();
    }


                       
```
## Sending the CSV-file to a FTP-server
We reimport the written CSV-file from the storage again and save it as a string. We send the string to the server and clear the old CSV-file.

                               
```
StringBuilder sb = new StringBuilder();

String ftpUrl = "ftp://%s:%s@%s/%s";
String host = "IP-ADRESS";
String user = "USERNAME";
String pass = "PASSWORD";

String uploadPath = "test.csv";

ftpUrl = String.format(ftpUrl, user, pass, host, uploadPath);
System.out.println("Upload URL: " + ftpUrl);

FileReader fr = null;

try {
fr = new FileReader("/storage/emulated/0/sean/output"+unixTimename+".csv");
} catch (FileNotFoundException e) {
e.printStackTrace();
}
BufferedReader br = new BufferedReader(fr);
String zeile = "";

do {
try {
    zeile = br.readLine();
    if (zeile == null) {
        break;
    }
} catch (IOException e) {
    e.printStackTrace();
}

sb.append( zeile + "\n");



}
while (zeile != null);


try {

br.close();
} catch (IOException e) {
e.printStackTrace();
}


try {
    URL url = new URL(ftpUrl);
    URLConnection conn = url.openConnection();
    conn.setDoOutput(true);
    //Output muss in append mode
    OutputStream out = new BufferedOutputStream(conn.getOutputStream());
    BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(out, Charset.forName("UTF-8")));

    writer.write(String.valueOf(sb));
    writer.flush();
    writer.close();
    out.close();

    System.out.println("File uploaded");
    sb.setLength(0);
} catch (IOException ex) {

    ex.printStackTrace();
}
try {

    FileOutputStream f2 = new FileOutputStream("/storage/emulated/0/sean/output" + unixTimename + ".csv", false);
    try {

        f2.write(("timestamp,rel_time,acc_x,acc_y,acc_z,gyro_x,gyro_y,gyro_z,azimuth,pitch,roll\n").getBytes());
        f2.flush();
        f2.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
}

```
## Performance
It is important for our app to save battery because it runs all day long.

![](https://raw.githubusercontent.com/MatheLi/BWKI/master/res/Leistung_app.png?raw=true)
You can see that the CPU usage is low. In addition the memory utilization raises and falls because we read-in frequently the CSV-files.


![](https://raw.githubusercontent.com/MatheLi/BWKI/master/res/Abstand_sendungen.png?raw=true)

You can also see this peak in the network graph. It is in 10 seconds interval.
To put it in a nutshell our application doesn't waste system resources. 



| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) |  [Code of the model](https://matheli.github.io/BWKI/posts/First_model.html) | [Code of the evaluation program ](https://matheli.github.io/BWKI/posts/Second_model.html)  | [The dataset](https://matheli.github.io/BWKI/posts/The_dataset.html) | [Accuracy](https://matheli.github.io/BWKI/posts/Accuracy.html) | [The team](https://matheli.github.io/BWKI/posts/The_team/The_team.html) |

