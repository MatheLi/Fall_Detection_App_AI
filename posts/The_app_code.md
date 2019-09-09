# The App

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
import android.support.v4.app.ActivityCompat;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;

import android.widget.TextView;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.concurrent.TimeUnit;

import static android.content.ContentValues.TAG;


public class MainActivity extends AppCompatActivity implements SensorEventListener {
    public static int off=1;
    static float timer_1;
    long unixTimename = System.currentTimeMillis() / 1000L;
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

    public static void ende() {

    }

    public void gui_actualisieren() {
        runOnUiThread(new Runnable() {

            @Override
            public void run() {
                TextView accX = findViewById(R.id.textView4);
                TextView accY = findViewById(R.id.textView6);
                TextView accZ = findViewById(R.id.textView7);
                accX.setText("0.0");
                accY.setText("0.0");
                accZ.setText("0.0");

                accX.setText(String.valueOf(accelerometer_data[0]));
                accY.setText(String.valueOf(accelerometer_data[1]));
                accZ.setText(String.valueOf(accelerometer_data[2]));
            }
        });
    }
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
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
        String entry = "timestamp, rel time, acc_x, acc_y, acc_z, gyro_x, gyro_y, gyro_z, azimuth, pitch, roll\n";
        try {


            FileOutputStream f = new FileOutputStream(file, true);

            try {

                f.write(entry.getBytes());
                f.flush();
                f.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        Timer thread1 = new Timer(this);
        thread1.start();
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
        final MediaPlayer mp2 = MediaPlayer.create(mainActivity, R.raw.zwischenton);
        float timer_1 = MainActivity.timer_1;
        int off = MainActivity.off;
        try {
            sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        File file=MainActivity.file;
        while (true){
            off = MainActivity.off;

        if (off==0) {
            double d = Math.pow(10, 3); // Angabe Nachkommastellen
            double timer_2 =Math.round(timer_1*d)/d;
            try {
                sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            float[] gyroscope_data = MainActivity.gyroscope_data;
            float[] accelerometer_data = MainActivity.accelerometer_data;
            float[] orientation_data = MainActivity.orientation_data;
            if (accelerometer_data != null && orientation_data != null && gyroscope_data!=null) {
                Log.d(TAG, "Accelometer X:  " + accelerometer_data[0] + "    Y: " + accelerometer_data[1] + "   Z:   " + accelerometer_data[2]);
                Log.d(TAG, "Orientation  Azimuth :  " + orientation_data[0] + "    Pitch: " + orientation_data[1] + "   Roll:   " + orientation_data[2]);
                Log.d(TAG, "Gyroscope X:  " + gyroscope_data[0] + "    Y: " + gyroscope_data[1] + "   Z:   " + gyroscope_data[2]);
                long unixTime = System.currentTimeMillis() / 1000L;

                String entry = unixTime +","+timer_2 + "," + accelerometer_data[0] + "," + accelerometer_data[1] + "," + accelerometer_data[2] + ","+ gyroscope_data[0] + ","+ gyroscope_data[1] + ","+ gyroscope_data[2] + "," + orientation_data[0]+ "," + orientation_data[1]+ "," + orientation_data[2]+ "\n";
               try {


                    FileOutputStream f = new FileOutputStream(file, true);

                    try {
                        Log.e("SCHREiBT",String.valueOf(timer_2));
                        f.write(entry.getBytes());
                        f.flush();
                        f.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }


        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
                timer_1 = (float) (timer_1 + 0.005); //FLOAT RUNDEN 3 NACHKOMMASTELLEN


               if (timer_2==0.335){
                   mp2.start();
                   Log.e("erreicht","erreicht");
               }
               if (timer_2==0.67){
                    mp2.start();
                }
                if (timer_2==1.005){
                    MainActivity.off=1;
                    mainActivity.sensorManager.unregisterListener(mainActivity, mainActivity.accelerometer);
                    mainActivity.sensorManager.unregisterListener(mainActivity, mainActivity.orientation);
                    mainActivity.sensorManager.unregisterListener(mainActivity, mainActivity.gyroscope);
                    mp2.start();

                }
        mainActivity.gui_actualisieren();
        }
    }
    }
    }
}
```

| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) | [Code of the first model](./posts/First_model.md) | [Code of the second model](https://matheli.github.io/BWKI/posts/Second_model.html) | [The team](https://matheli.github.io/BWKI/posts/The_team/The_team.html) |

