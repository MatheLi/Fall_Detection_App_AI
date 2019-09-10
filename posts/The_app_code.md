# The app

```
package com.example.bwki_test_accelometer;

import android.Manifest;

import android.content.pm.PackageManager;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.media.MediaPlayer;
import android.os.Environment;
import android.os.StrictMode;
import android.support.v4.app.ActivityCompat;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;

import android.widget.TextView;

import java.io.BufferedOutputStream;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.FileReader;
import java.io.IOException;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.net.URL;
import java.net.URLConnection;
import java.nio.charset.Charset;
import java.nio.charset.StandardCharsets;
import java.nio.file.StandardOpenOption;
import java.util.concurrent.TimeUnit;


import static android.content.ContentValues.TAG;
import static java.lang.Math.round;


public class MainActivity extends AppCompatActivity implements SensorEventListener {
    public static int off=1;
    public static int datei=1;
    public static int check=1;
    static float timer_1;
    static double timer_2;
    static double zahler=1;
    static String csv_Datei="";
    static long unixTimename = System.currentTimeMillis() / 1000L;
    public android.hardware.SensorManager sensorManager;
    public Sensor accelerometer;
    private static final int REQUEST_EXTERNAL_STORAGE = 1;
    private static String[] PERMISSIONS_STORAGE = {
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE};
    Sensor orientation;
    Sensor gyroscope;
    static float[] accelerometer_data;
    static float[] orientation_data;
    static float[] gyroscope_data;
    static File file;
    static File file2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        StrictMode.ThreadPolicy policy = new StrictMode.ThreadPolicy.Builder().permitAll().build();
        StrictMode.setThreadPolicy(policy);
        setContentView(R.layout.activity_main);
        int permission = ActivityCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE);
        if (permission != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(
                    this,
                    PERMISSIONS_STORAGE,
                    REQUEST_EXTERNAL_STORAGE
            );
        }
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
        sensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
        accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        gyroscope=sensorManager.getDefaultSensor(Sensor.TYPE_GYROSCOPE);
        orientation = sensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION);
    }
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
    public void onSensorChanged(SensorEvent event) {
        if (event.sensor.getType() == Sensor.TYPE_ACCELEROMETER)
            accelerometer_data = event.values;
        if (event.sensor.getType() == Sensor.TYPE_ORIENTATION)
            orientation_data = event.values;
        if (event.sensor.getType() == Sensor.TYPE_GYROSCOPE)
            gyroscope_data = event.values;
    }
    public void onAccuracyChanged(Sensor sensor, int accuracy) {

    }
}
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
                    Log.i("check",String.valueOf(check));

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
                  //  Log.d(TAG, "Accelometer X:  " + accelerometer_data[0] + "    Y: " + accelerometer_data[1] + "   Z:   " + accelerometer_data[2]);
                    //Log.d(TAG, "Orientation  Azimuth :  " + orientation_data[0] + "    Pitch: " + orientation_data[1] + "   Roll:   " + orientation_data[2]);
                    //Log.d(TAG, "Gyroscope X:  " + gyroscope_data[0] + "    Y: " + gyroscope_data[1] + "   Z:   " + gyroscope_data[2]);
                    final long unixTime = System.currentTimeMillis() / 1000L;

                    String entry = unixTime + "," + timer_2 + "," + accelerometer_data[0] + "," + accelerometer_data[1] + "," + accelerometer_data[2] + "," + gyroscope_data[0] + "," + gyroscope_data[1] + "," + gyroscope_data[2] + "," + orientation_data[0] + "," + orientation_data[1] + "," + orientation_data[2] + "\n";

                    if (zahler%2==0) {

                        try {
                            FileOutputStream f = new FileOutputStream(file, true);
                            try {
                               // Log.e("SCHREIBT", String.valueOf(timer_2));
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
                    //            Log.e("SCHREIBT2", String.valueOf(timer_2));
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
                            Log.e("senden","senden");
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

