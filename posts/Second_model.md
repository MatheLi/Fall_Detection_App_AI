# Evaluating Program

```
"""
DATASET: G. Vavoulas, M. Pediaditis, C. Chatzaki, E. G. Spanakis, M. Tsiknakis, The MobiFall Dataset: Fall Detection and Classification with a Smartphone, invited publication for the International Journal of Monitoring and Surveillance Technologies Research, pp 44-56, 2014, DOI:10.4018/ijmstr.201401010
"""
import os
import pandas as pd
import numpy as np
import keras
from keras.models import load_model
from keras.utils import CustomObjectScope
from keras.initializers import glorot_uniform
import time

def daten_verarbeitung(last_data,last_data2):
    test_data=[]
    print(last_data.at[1,"rel_time"],last_data2.at[1,"rel_time"])
    if last_data.at[1,"rel_time"]<last_data2.at[1,"rel_time"]:
        last_data=pd.concat([last_data,last_data2])
        print(last_data["rel_time"].values[1995:2005])
        for i in range(1,2000):
            test_data.append(last_data2[["acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"]].values[i:2000+i])
    else:
       
        last_data2=pd.concat([last_data2,last_data])
        print(last_data2["rel_time"].values[1995:2005])
        for i in range(1,2000):
            test_data.append(last_data[["acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"]].values[i:2000+i])
    return test_data
        

def einlesen(model):
    pfad="C:\\Users\\mario\\Desktop\\Filezilla"
    last_data=pd.read_csv(pfad+"\\anlauf2.csv",usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
    last_data2=pd.read_csv(pfad+"\\anlauf.csv",usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
    while True:
        
        if os.path.exists(pfad+"\\test.csv"):
            
            try:
                data=pd.read_csv(pfad+"\\test.csv",usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
            except:
                continue
            
        if len(data)==2001 and data.at[1998,"acc_x"]!=last_data.at[1998,"acc_x"]:
            time.sleep(0.2)
            last_data=data
            print(last_data)
            test_data=daten_verarbeitung(last_data,last_data2)
            daten_prufen(model,test_data)
        
        if os.path.exists(pfad+"\\test2.csv"):
            
            try:    
                data2=pd.read_csv(pfad+"\\test2.csv",usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
            except:
                continue
       
        if len(data2)==2001 and data2.at[1998,"acc_x"]!=last_data2.at[1998,"acc_x"]:
                time.sleep(0.2)
                last_data2=data2
                print(last_data2)
                test_data=daten_verarbeitung(last_data,last_data2)
                daten_prufen(model,test_data)
        
def daten_prufen(model,test_data):
    labels=[]    
    test=[]
    #pfad="C:\\Users\\mario\\Desktop\\Filezilla\\dateien"
    for i in range(0,1998):
        test=[]
        test.append(test_data[i])
        test=keras.preprocessing.sequence.pad_sequences(test, maxlen=7999, dtype='int32', padding='pre', truncating='pre', value=0.0) #maxlen train_data.shape[1])
        test=np.array(test)
       # print(test.shape,i)
        predictions=model.predict(test)
      #  print("Vorhersage",np.argmax(predictions))    
        
        if np.argmax(predictions)==1:
            labels.append(1) 
            
        else:
            labels.append(0)         
    labels=np.array(labels)
    print(np.max(labels),np.min(labels))
    if np.mean(labels)>0.5:
        print("Abschließendes Label 1", np.mean(labels))
    else:
        print("Abschließendes Label 0", np.mean(labels))
with CustomObjectScope({'GlorotUniform': glorot_uniform()}):
    model =load_model('simple_mlp500.pb') 
einlesen(model)

```

| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) | [Code of the model](https://matheli.github.io/BWKI/posts/First_model.html) | [The app](https://matheli.github.io/BWKI/posts/The_app_code.html) | [The dataset](https://matheli.github.io/BWKI/posts/The_dataset.html) | [Accuracy](https://matheli.github.io/BWKI/posts/Accuracy.html) | [The team](https://matheli.github.io/BWKI/posts/The_team/The_team.html) |
