```
# -*- coding: utf-8 -*-
"""
Created on Sat Jul 13 08:48:29 2019
@author: mario
"""
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers
import os
import numpy as np
import pandas as pd
import random

def einlesen(): 
    """
    Hier werden alle .csv Dateien in ein Panda-Dataframe eingelesen. Es werden jeweils 3 Versuche von 67 
    Versuchspersonen eingelesen. Sortiert in den verschiedenen Ordnernamen. Außerdem wird geprüft ob,
    es den Pfad überhaupt gibt, denn es fehlen immer wieder einzelne Dateien.
    """
    
    #FALLS
    FOL=[]
    FKL=[]
    BSC=[]
    SDL=[]
    
    #ADL
    CHU=[]
    SCH=[]
    
    #weiterhin steht es aus WAL, JOG usw. einzulesen
    
    #FALLS
    #FOL
    
    FOL=[pd.read_csv("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\FOL\FOL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
    for a in range(1,4)
        for i in range(1,68)
            if os.path.exists("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\FOL\FOL_{0}_{1}_annotated.csv".format(i,a))
            ]
    FKL=[pd.read_csv("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\FKL\FKL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\FKL\FKL_{0}_{1}_annotated.csv".format(i,a))]
    BSC=[pd.read_csv("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\BSC\BSC_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\BSC\BSC_{0}_{1}_annotated.csv".format(i,a))]
    SDL=[pd.read_csv("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\SDL\SDL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\SDL\SDL_{0}_{1}_annotated.csv".format(i,a))]
    
    #ADL
    CHU=[pd.read_csv("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\CHU\CHU_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\CHU\CHU_{0}_{1}_annotated.csv".format(i,a))]
    SCH=[pd.read_csv("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\SCH\SCH_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\Annotated Data\SCH\SCH_{0}_{1}_annotated.csv".format(i,a))]
    
    return FOL,FKL,BSC,SDL,CHU,SCH


def label(FOL,FKL,BSC,SDL,CHU,SCH): 
    """
    Hier wird jeder Datei eine weitere Spalte "Label" angehängt. Da wir an dieser Stelle noch wissen, ob 
    es sich um ein Sturz oder ADL handelt können wir uns an den Ordnernamen orientieren. Da es uns ausreicht 
    erstmal nur vorherszusagen ob jmd. gestürzt ist und nicht die genau Sturzart zu erkennen, vereinfachen wir
    auf zwei Label: 1=Sturz 0=ADL
    """
# Falls (1)
    for i in range(len(FOL)):
        FOL[i]["Label"]=1
    for i in range(len(FKL)):
        FKL[i]["Label"]=1
    for i in range(len(BSC)):
        BSC[i]["Label"]=1
    for i in range(len(SDL)):
        SDL[i]["Label"]=1
    #ADLs (0)
    for i in range(len(CHU)):
        CHU[i]["Label"]=0
    for i in range(len(SCH)):
        SCH[i]["Label"]=0
    return FOL,FKL,BSC,SDL,CHU,SCH

def erstelle_daten_vektor(FOL,FKL,BSC,SDL,CHU,SCH): 
    """
    Hier werden nun alle Dateien aus allen Ordner einem Array hinzugefügt. So ensteht ein großes Array mit
    allen Dateien.
    """
    daten_vektor=[]
    for i in range(len(FOL)):
        daten_vektor.append(FOL[i])
    for i in range(len(FKL)):
        daten_vektor.append(FKL[i])
    for i in range(len(BSC)):
        daten_vektor.append(BSC[i])
    for i in range(len(SDL)):
        daten_vektor.append(SDL[i])
    for i in range(len(CHU)):
        daten_vektor.append(CHU[i])
    for i in range(len(SCH)):
        daten_vektor.append(SCH[i])
    #Hier wird das Array auch noch einmal gemischt, um Stürze und ADL nicht direkt hintereinander zu haben.
    random.shuffle(daten_vektor)
    return daten_vektor

def erstelle_daten_matrix(daten_vektor):
    """
    Hier wird eine Matrix erstellt. An Postition 0 werden die Sensordaten eingefügt (immer noch als Dataframe!)
    An Position 1 wird das Label eingefügt.
    """
    daten_matrix=([],[])
    for i in range(len(daten_vektor)):
        daten_matrix[0].append(daten_vektor[i][["acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"]])
        daten_matrix[1].append(daten_vektor[i].at[0,"Label"])
        
    return daten_matrix
def daten_umwandeln():
    test=[],[]
    train=[],[]
    test = daten_matrix[0][:100],daten_matrix[1][:100] #Hier erfolgt der Split in Train und Test Data
    train = daten_matrix[0][100:],daten_matrix[1][100:] 
    
    print("Aufteilen Erfolgreich! Anzahl der Trainingsdaten:{0} und der Testdaten:{1} ".format(len(train[0]),len(test[0]))) 
    """
    Dieser Teil ist etwas umständlich. Hier werden die Werte aus dem Dataframe wieder raushgeholt und in einem 
    Array gespeichert
    """
    train_data=[]
    labels=[]
    test_data=[]
    test_labels=[]

    for i in range(len(train[0])): #für alle Datenpunkte im Datenframe
        train_data.append(train[0][i].values) #Wird der Wert in das Array geschrieben


    for i in range(len(train[1])): # und genau das gleiche für die Labels
        labels.append(train[1][i])
    labels=np.array(labels)
    for i in range(len(test[0])): # und für die Testdaten
        test_data.append(test[0][i].values) 
    for i in range(len(test[1])): # und genau das gleiche für die Labels
        test_labels.append(test[1][i])
    test_labels=np.array(test_labels)
    return train_data,labels,test_data, test_labels
def train_model(train_data,labels,test_data, test_labels):
    
    model = keras.Sequential([     
    keras.layers.Flatten(#input_shape=(1,)
        ), 
    keras.layers.Masking(mask_value=0.0),
    
    keras.layers.Dense(613, activation=tf.nn.softmax),
    keras.layers.Dense(642, activation=tf.nn.softmax), 
    
    ]) 
    
    model.compile(optimizer='adam', loss='sparse_categorical_crossentropy', metrics=['accuracy'])
    
    
    train_data=keras.preprocessing.sequence.pad_sequences(train_data, maxlen=None, dtype='int32', padding='pre', truncating='pre', value=0.0)
    train_data=np.array(train_data)
    test_data=keras.preprocessing.sequence.pad_sequences(test_data, maxlen=train_data.shape[1], dtype='int32', padding='pre', truncating='pre', value=0.0)
    test_data=np.array(test_data)
    
    
    model.fit(train_data,labels,batch_size=128, epochs=6 )
    model.summary()  
    print(test_labels.shape,labels.shape)
    print(test_data.shape,train_data.shape)
    #Model mit eigenen Daten testen
    test_loss, test_acc = model.evaluate(test_data, test_labels) 
    print('Test accuracy:', test_acc) 
    print('Test loss:', test_loss)
    aufnahme_adl=[]
    aufnahme_adl.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/BSC/BSC_1_1_annotated.txt",dtype=float ,delimiter=",",skiprows=1))
    aufnahme_adl=keras.preprocessing.sequence.pad_sequences(aufnahme_adl, maxlen=train_data.shape[1], dtype='int32', padding='pre', truncating='pre', value=0.0)
    aufnahme_adl=np.array(aufnahme_adl)
    print(aufnahme_adl.shape)
    predictions=model.predict(aufnahme_adl)
    print("Vorhersage",np.argmax(predictions))
# Alle Funktionen werden ausgeführt
FOL,FKL,BSC,SDL,CHU,SCH=einlesen()
   
FOL,FKL,BSC,SDL,CHU,SCH=label(FOL,FKL,BSC,SDL,CHU,SCH)

daten_vektor=erstelle_daten_vektor(FOL,FKL,BSC,SDL,CHU,SCH)

daten_matrix=erstelle_daten_matrix(daten_vektor)
train_data, labels,test_data, test_labels=daten_umwandeln()
train_model(train_data,labels, test_data, test_labels)
```

| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) | [Code of the first Model](https://matheli.github.io/BWKI/posts/First_model.html) |
