# First model
```
"""
DATASET: G. Vavoulas, M. Pediaditis, C. Chatzaki, E. G. Spanakis, M. Tsiknakis, The MobiFall Dataset: Fall Detection and Classification with a Smartphone, invited publication for the International Journal of Monitoring and Surveillance Technologies Research, pp 44-56, 2014, DOI:10.4018/ijmstr.201401010
"""
import tensorflow as tf
from tensorflow import keras
import os
import numpy as np
import pandas as pd
import random

print("GPU Available: ", tf.test.is_gpu_available()) #GPU rendering beschleunigt das Training 

def einlesen(): 
    """
    Hier werden alle .csv Dateien in ein Panda-Dataframe eingelesen. Es werden jeweils 3 Versuche von 67 
    Versuchspersonen eingelesen. Sortiert in den verschiedenen Ordnernamen. Außerdem wird geprüft ob,
    es den Pfad überhaupt gibt, denn es fehlen immer wieder einzelne Dateien.
    
    Die Funktion gibt Pandas Datenframe nach den Ordnern sotiert zurück.
    """
   
    #FALLS
    FOL=[]
    FKL=[]
    BSC=[]
    SDL=[]
    
    #ADL
    CHU=[]
    SCH=[]
    STU=[]
    STN=[]
    CSI=[]
    CSO=[]
    JOG=[]
    JUM=[]
    SIT=[]
    STD=[]
    WAL=[]

    #Pfad zu MobiAct_Dataset_v2
    pfad="C:\\Users\\mario\\Desktop\\BWKI_Github\\BWKI-Fall-Detection\\MobiAct_Dataset_v2"
    #FALLS
    print("reading")
    #FOL    
    FOL=[pd.read_csv(pfad+"\Annotated Data\FOL\FOL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
    for a in range(1,4)
        for i in range(1,68)
            if os.path.exists(pfad+"\Annotated Data\FOL\FOL_{0}_{1}_annotated.csv".format(i,a))
            ]
    print("reading")
    FKL=[pd.read_csv(pfad+"\Annotated Data\FKL\FKL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\FKL\FKL_{0}_{1}_annotated.csv".format(i,a))]
    print("reading")
    BSC=[pd.read_csv(pfad+"\Annotated Data\BSC\BSC_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\BSC\BSC_{0}_{1}_annotated.csv".format(i,a))]
    print("reading")
    SDL=[pd.read_csv(pfad+"\Annotated Data\SDL\SDL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\SDL\SDL_{0}_{1}_annotated.csv".format(i,a))]
    print("reading")
    #ADL
    CHU=[pd.read_csv(pfad+"\Annotated Data\CHU\CHU_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,5) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\CHU\CHU_{0}_{1}_annotated.csv".format(i,a))]
    SCH=[pd.read_csv(pfad+"\Annotated Data\SCH\SCH_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,5) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\SCH\SCH_{0}_{1}_annotated.csv".format(i,a))]
    STU=[pd.read_csv(pfad+"\Annotated Data\STU\STU_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,5) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\STU\STU_{0}_{1}_annotated.csv".format(i,a))]
    print("reading")
    STN=[pd.read_csv(pfad+"\Annotated Data\STN\STN_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,5) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\STN\STN_{0}_{1}_annotated.csv".format(i,a))]
    CSI=[pd.read_csv(pfad+"\Annotated Data\CSI\CSI_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,5) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\CSI\CSI_{0}_{1}_annotated.csv".format(i,a))]
    CSO=[pd.read_csv(pfad+"\Annotated Data\CSO\CSO_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,5) for i in range(1,68) if os.path.exists(pfad+"\Annotated Data\CSO\CSO_{0}_{1}_annotated.csv".format(i,a))]
    print("reading")
    JOG=[pd.read_csv(pfad+"\\10s_bearbeitet\\JOG\\JOG_{0}_{1}_10s.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(0,70) if os.path.exists(pfad+"\\10s_bearbeitet\\JOG\\JOG_{0}_{1}_10s.csv".format(i,a))]
    JUM=[pd.read_csv(pfad+"\\10s_bearbeitet\\JUM\\JUM_{0}_{1}_10s.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,4) for i in range(0,70) if os.path.exists(pfad+"\\10s_bearbeitet\\JUM\\JUM_{0}_{1}_10s.csv".format(i,a))]
    SIT=[pd.read_csv(pfad+"\\10s_bearbeitet\\SIT\\SIT_{0}_{1}_10s.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(1,7) for i in range(0,19) if os.path.exists(pfad+"\\10s_bearbeitet\\SIT\\SIT_{0}_{1}_10s.csv".format(i,a))]
    print("reading")
    STD=[pd.read_csv(pfad+"\\10s_bearbeitet\\STD\\STD_{0}_{1}_10s.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(0,10) for i in range(0,20) if os.path.exists(pfad+"\\10s_bearbeitet\\STD\\STD_{0}_{1}_10s.csv".format(i,a))]
    WAL=[pd.read_csv(pfad+"\\10s_bearbeitet\\WAL\\WAL_{0}_{1}_10s.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])for a in range(0,10) for i in range(0,20) if os.path.exists(pfad+"\\10s_bearbeitet\\WAL\\WAL_{0}_{1}_10s.csv".format(i,a))]
    
    return FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL


def label(FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL): 
    """
    Hier wird jeder Datei eine weitere Spalte "Label" angehängt. Da wir an dieser Stelle noch wissen, ob 
    es sich um ein Sturz oder ADL handelt können wir uns an den Ordnernamen orientieren. Da es uns ausreicht 
    erstmal nur vorherszusagen ob jmd. gestürzt ist und nicht die genau Sturzart zu erkennen, vereinfachen wir
    auf zwei Label: 1=Sturz 0=ADL
    
    Die Funktion erwartet als Parameter die Pandas Datenframes nach den Ordnern sortiert. Es gibt die Daten (immer noch als Datenframe)
    zurück, mit der extra Spalte "Label" welche komplett mit "1" oder "0" gefüllt ist.
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
    for i in range(len(STU)):
        STU[i]["Label"]=0
    for i in range(len(STN)):
        STN[i]["Label"]=0
    for i in range(len(CSI)):
        CSI[i]["Label"]=0   
    for i in range(len(CSO)):
        CSO[i]["Label"]=0
    for i in range(len(JOG)):
        JOG[i]["Label"]=0
    for i in range(len(JUM)):
        JUM[i]["Label"]=0
    for i in range(len(SIT)):
        SIT[i]["Label"]=0
    for i in range(len(STD)):
        STD[i]["Label"]=0
    for i in range(len(WAL)):
        WAL[i]["Label"]=0
    return FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL

def erstelle_daten_vektor(FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL): 
    """
    Da wir nun in den Datenframes auch das Label enthalten haben, können wir uns von der Ordnerstruktur lösen und alles zusammen mischen.
    Hier werden nun alle Dateien aus allen Ordner einem Array hinzugefügt. So ensteht ein großes Array mit
    allen Dateien.
    
    Erwartet als Parameter die Datenframes mit den Labels. Und gibt einen Daten_Vektor zurück (gelöst von der Ordnerstruktur)
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
    for i in range(len(STU)):
        daten_vektor.append(STU[i])
    for i in range(len(STN)):
        daten_vektor.append(STN[i])
    for i in range(len(CSI)):
        daten_vektor.append(CSI[i])
    for i in range(len(CSO)):
        daten_vektor.append(CSO[i])
    for i in range(len(JOG)):
        daten_vektor.append(JOG[i])
    for i in range(len(JUM)):
        daten_vektor.append(JUM[i])
    for i in range(len(SIT)):
        daten_vektor.append(SIT[i])
    for i in range(len(STD)):
        daten_vektor.append(STD[i])
    for i in range(len(WAL)):
        daten_vektor.append(WAL[i])
    #Hier wird das Array auch noch einmal gemischt, um Stürze und ADL nicht direkt hintereinander zu haben.
    random.shuffle(daten_vektor)
    return daten_vektor

def erstelle_daten_matrix(daten_vektor):
    """
    Hier wird eine Matrix erstellt. An Postition 0 werden die Sensordaten eingefügt (immer noch als Dataframe)
    An Position 1 wird das Label eingefügt.
    Die Matrix sieht ungefähr so aus:
    Position:      0            1
                 Datenframes   passendes Label bezogen auf die ganze Datei
    """
    daten_matrix=([],[])
    for i in range(len(daten_vektor)):
        daten_matrix[0].append(daten_vektor[i][["acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"]])
        daten_matrix[1].append(daten_vektor[i].at[0,"Label"]) # Aus der ganzen Spalte wird nur das Label rausgeholt 
        
    return daten_matrix

def daten_umwandeln():
    test=[],[]
    train=[],[]
    test = daten_matrix[0][:400],daten_matrix[1][:400] #Hier erfolgt der Split in Train und Test Data
    train = daten_matrix[0][400:],daten_matrix[1][400:] 
    
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
    """
    Hier wird ein Keras Model definiert. Die Hyperparameter habe wir durch ein selbstgeschriebenes Programm, welches
    diese zufällig erstellt und dann die Acc prüft, gefunden.
    """
    model = keras.Sequential([     
    keras.layers.Flatten(), 
    keras.layers.Masking(mask_value=0.0),
    keras.layers.Dense(109, activation=tf.nn.softsign), 
    keras.layers.Dense(50, activation=tf.nn.softmax),
    ]) 
    optimizer=keras.optimizers.Nadam(lr=0.001, beta_1=0.9, beta_2=0.999, epsilon=None, schedule_decay=0.003)
    model.compile(optimizer=optimizer, loss='sparse_categorical_crossentropy', metrics=['accuracy'])
    
    
    
    #Hier werden alle Daten auf eine Länge gebracht. Ansonsten kann das Model diese nicht verarbeiten
    train_data=keras.preprocessing.sequence.pad_sequences(train_data, maxlen=None, dtype='int32', padding='pre', truncating='pre', value=0.0)
    train_data=np.array(train_data)
    test_data=keras.preprocessing.sequence.pad_sequences(test_data, maxlen=train_data.shape[1], dtype='int32', padding='pre', truncating='pre', value=0.0)
    test_data=np.array(test_data) 
    history=model.fit(train_data,labels,validation_split=0.15,batch_size=100, epochs=5500 ) 
    
    
    model.summary()  
    print(test_labels.shape,labels.shape)
    print(test_data.shape,train_data.shape)
    #Model mit den Test-Daten testen
    test_loss, test_acc = model.evaluate(test_data, test_labels) 
    print('Test accuracy:', test_acc) 
    print('Test loss:', test_loss)
    visualization(history)
    model.save('simple_mlp5500.pb') 
    
def visualization(history):
    import matplotlib.pyplot as plt
    # Graphen plotten um das Model besser bewerten zu können
    plt.plot(history.history['acc'])
    plt.plot(history.history['val_acc'])
    plt.title('Model accuracy')
    plt.ylabel('Accuracy')
    plt.xlabel('Epoch')
    plt.legend(['Train', 'Test'], loc='upper left')
    plt.show()
    
    # Plot training & validation loss values
    plt.plot(history.history['loss'])
    plt.plot(history.history['val_loss'])
    plt.title('Model loss')
    plt.ylabel('Loss')
    plt.xlabel('Epoch')
    plt.legend(['Train', 'Test'], loc='upper left')
    plt.show()
    
# Alle Funktionen werden ausgeführt
FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL=einlesen()
   
FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL=label(FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL)

daten_vektor=erstelle_daten_vektor(FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL)

daten_matrix=erstelle_daten_matrix(daten_vektor)
train_data, labels,test_data, test_labels=daten_umwandeln()
train_model(train_data,labels, test_data, test_labels)

```

| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) | [Code of the second model](https://matheli.github.io/BWKI/posts/Second_model.html) | [The app](https://matheli.github.io/BWKI/posts/The_app_code.html) | [Accuracy](https://matheli.github.io/BWKI/posts/Accuracy.html) | [The dataset](https://matheli.github.io/BWKI/posts/The_dataset.html) | [The team](https://matheli.github.io/BWKI/posts/The_team/The_team.html) |

