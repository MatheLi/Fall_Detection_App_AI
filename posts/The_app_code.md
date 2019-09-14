# The app
The app has diffrent tasks. It records the gyroscope, accelerometer and orientation data. In addition it writes a CSV-file and sends it to a FTP-Server.

## Create Files
In this part, we create two files. Furthermore we name the diffrent columms columns.
```
        File sdCard = Environment.getExternalStorageDirectory();
        File dir = new File(sdCard.getAbsolutePath() + "/sean");
        file = new File(dir, "output"+unixTimename+".csv");
        file2 = new File(dir, "output2"+unixTimename+".csv");
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
        try {
            FileOutputStream f2 = new FileOutputStream(file2, true);
            try {
                f2.write(entry.getBytes());
                f2.flush();
                f2.close();
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
We have to create a function for each button, which register/unregister the sensors. As well we play a sound and start an extra thread.

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
    
```
class Timer extends Thread {
    MainActivity mainActivity;

    public Timer(MainActivity main) {
        mainActivity = main;
    }
    @Override
    public void run() {
        int datei=MainActivity.datei;
        float timer_1 = MainActivity.timer_1;
        double timer_2 = MainActivity.timer_2;
        int off = MainActivity.off;
        double zahler=MainActivity.zahler;
        String csv_Datei= MainActivity.csv_Datei;
        int check=MainActivity.check;
        File file = MainActivity.file;
        File file2 = MainActivity.file2;
        double a = Math.pow(10, 3); // Angabe Nachkommastellen
        double d = Math.pow(10, 4); // Angabe Nachkommastellen
        while (true) {
            off = MainActivity.off;
            MainActivity.timer_2=timer_2;
            MainActivity.zahler=zahler;
            MainActivity.datei=datei;
            MainActivity.check=check;
            if (off == 0) {
                timer_2 = round(timer_1 * d) / d;
                if (round(timer_2 * a) / a==zahler) {
                    zahler = zahler+1;
                    check=check+1;
                    MainActivity.check=check;
                   

                }
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

                    if (zahler%2==0) {

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
                    }
                    if (zahler%2==1) {
                        try {

                            FileOutputStream f2 = new FileOutputStream(file2, true);
                            try {
                  
                                f2.write(entry.getBytes());
                                f2.flush();
                                f2.close();

                            } catch (IOException e) {
                                e.printStackTrace();
                            }


                        } catch (FileNotFoundException e) {
                            e.printStackTrace();
                        }

                    }
                    if (timer_2==1){
                    new Thread(new Runnable(){
                        MainActivity mainActivity;



                        public void run() {
                            int test=1;
                            while (true) {
                                double zahler=MainActivity.zahler;
                                int check= MainActivity.check;
                                String csv_Datei = MainActivity.csv_Datei;

                                long unixTimename=mainActivity.unixTimename;
                        
                                if (check%2==1 && test==0) {
                                    test=1;
                                    MainActivity.zahler=zahler;
                                    String ftpUrl = "ftp://%s:%s@%s/%s";
                                    String host = "192.168.0.183";
                                    String user = "NUTZERNAME";
                                    String pass = "PASS";

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

                                        csv_Datei = csv_Datei + zeile + "\n";

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
                                        OutputStream out = new BufferedOutputStream(conn.getOutputStream());
                                        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(out, Charset.forName("UTF-               8")));

                                        writer.write(csv_Datei);
                                        writer.flush();
                                        writer.close();
                                        out.close();

                                        System.out.println("File uploaded");
                                        csv_Datei = "timestamp,rel_time,acc_x,acc_y,acc_z,gyro_x,gyro_y,gyro_z,azimuth,pitch,roll\n";
                                    } catch (IOException ex) {

                                        ex.printStackTrace();
                                    }
                                    try {

                                        FileOutputStream f2 = new FileOutputStream("/storage/emulated/0/sean/output" + unixTimename +".csv", false);
                                        try {

                                            f2.write(csv_Datei.getBytes());
                                            f2.flush();
                                            f2.close();
                                        } catch (IOException e) {
                                            e.printStackTrace();
                                        }
                                    } catch (FileNotFoundException e) {
                                        e.printStackTrace();
                                    }
                                }
                                if (check%2==0 && test == 1) {
                                    test=0;
                                 
                                    String ftpUrl = "ftp://%s:%s@%s/%s";
                                    String host = "192.168.0.183";
                                    String user = "NUTZERNAME";
                                    String pass = "PASS";
                                    String uploadPath = "test2.csv";
                                    ftpUrl = String.format(ftpUrl, user, pass, host, uploadPath);
                                    System.out.println("Upload URL: " + ftpUrl);
                                    FileReader fr = null;
                                    try {
                                        fr = new FileReader("/storage/emulated/0/sean/output2" + unixTimename +".csv");
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

                                        csv_Datei = csv_Datei + zeile + "\n";
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
                               
                                        OutputStream out = new BufferedOutputStream(conn.getOutputStream());
                                        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(out, Charset.forName("UTF-8")));
                                        writer.write(csv_Datei);
                                        writer.flush();
                                        writer.close();
                                        out.close();
                                        System.out.println("File uploaded");
                                        csv_Datei = "timestamp,rel_time,acc_x,acc_y,acc_z,gyro_x,gyro_y,gyro_z,azimuth,pitch,roll\n";
                                    } catch (IOException ex) {

                                        ex.printStackTrace();
                                    }
                                    try {
                                        FileOutputStream f2 = new FileOutputStream("/storage/emulated/0/sean/output2" + unixTimename +".csv", false);
                                        try {

                                            f2.write(csv_Datei.getBytes());
                                            f2.flush();
                                            f2.close();
                                        } catch (IOException e) {
                                            e.printStackTrace();
                                        }
                                    } catch (FileNotFoundException e) {
                                        e.printStackTrace();
                                    }}}}
                    }
                                    ).start();

                    }}

                    }

            }
        }}



```
| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) | [Code of the first model](https://matheli.github.io/BWKI/posts/First_model.html) | [Code of the second model](https://matheli.github.io/BWKI/posts/Second_model.html) | [Accuracy](https://matheli.github.io/BWKI/posts/Accuracy.html) | [The team](https://matheli.github.io/BWKI/posts/The_team/The_team.html) |

