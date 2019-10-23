# Train the model
The program was completely written by us.
## Importing libaries
```
import tensorflow as tf
from tensorflow import keras
import os
import numpy as np
import pandas as pd
import random
```

## Import dataset
Here all .csv files are read into a Panda DataFrame.
The dataset differs between different types of falls and ADLs (Activities of daily living).

![](https://raw.githubusercontent.com/MatheLi/BWKI/master/res/Bild_datensatz_types_adl_falls.png?raw=true)

This picture shows an overview of the falls and ADls.

There will be 3 attempts each of 67
subjects read. It also checks if there is the path at all because there are always missing files.
    
The function returns Panda's data frame - sorted by folder names.


```
"""
DATASET: G. Vavoulas, M. Pediaditis, C. Chatzaki, E. G. Spanakis, M. Tsiknakis, The MobiFall Dataset: Fall Detection and Classification with a Smartphone, invited publication for the International Journal of Monitoring and Surveillance Technologies Research, pp 44-56, 2014, DOI:10.4018/ijmstr.201401010
"""

def einlesen(): 

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

    #Path to MobiAct_Dataset_v2
    pfad="\\MobiAct_Dataset_v2"
    #FALLS
   
    #FOL    
    FOL=[pd.read_csv(pfad+"\Annotated Data\FOL\FOL_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
    for a in range(1,4)
        for i in range(1,68)
            if os.path.exists(pfad+"\Annotated Data\FOL\FOL_{0}_{1}_annotated.csv".format(i,a))
            ]
            
    [the same with all other types of falls and ADLs..]
    return FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL
```

## Adding labels to the file
We add the column "Label" to each file. We still know at this point, whether
if it is a fall or an ADL. First of all, we only want to predict whether the person has fallen and not the exact fall style.   
That's why we have two labels:

1 = fall 0 = ADL

The function expects as parameters the Pandas DataFrames and their label. It returns the data (still as a DataFrame), with the extra column "Label" which is completely filled with "1" or "0".
 ```
def label(data, is_fall):
        # Falls (1)
   if is_fall == 1:
       for i in range(len(data)):
           data[i]["Label"]=1  
        #ADLs (0)        
   else: 
       for i in range(len(data)):
           data[i]["Label"]=0

   return data
```
## Create a data vector
Since we now also have the label in the data frames, we can detach ourselves from the folder structure and mix everything   together.
Here all files from all folders are added to an array. 

Expects as parameters the DataFrames with the labels and returns a data_vector.
```
def erstelle_daten_vektor(list): 
    daten_vektor=[]
    for item in list: 
        for i in range(len(item)): 
            daten_vektor.append(item[i])
    
    #Shuffle array
    random.shuffle(daten_vektor)
    return daten_vektor
 ```
## Create Matrix
Here we create a data matrix. At position 0, the sensor data was inserted at position 1, the labels were inserted.
The matrix looks something like this:

Position:      0            1
             DataFrames    Label
             
 ```
def erstelle_daten_matrix(daten_vektor):
    daten_matrix=([],[])
    for i in range(len(daten_vektor)):
        daten_matrix[0].append(daten_vektor[i][["acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"]])
        daten_matrix[1].append(daten_vektor[i].at[0,"Label"]) # Aus der ganzen Spalte wird nur das Label rausgeholt 
        
    return daten_matrix
 ```
## Reconstruct the data structure
We have to reconstruct our data structure so that our model can learn with the data. 
 ```
def daten_umwandeln():
    test=[],[]
    train=[],[]
    test = daten_matrix[0][:400],daten_matrix[1][:400] # Test and traindata split
    train = daten_matrix[0][400:],daten_matrix[1][400:] 
    
    print("Aufteilen Erfolgreich! Anzahl der Trainingsdaten:{0} und der Testdaten:{1} ".format(len(train[0]),len(test[0]))) 

    train_data=[]
    labels=[]
    test_data=[]
    test_labels=[]

    for i in range(len(train[0])):
        train_data.append(train[0][i].values)


    for i in range(len(train[1])):
        labels.append(train[1][i])
    labels=np.array(labels)
    for i in range(len(test[0])):
        test_data.append(test[0][i].values) 
    for i in range(len(test[1])): 
        test_labels.append(test[1][i])
    test_labels=np.array(test_labels)
    return train_data,labels,test_data, test_labels
 ```
## Construct the model

 ```
def train_model(train_data,labels,test_data, test_labels):

    model = keras.Sequential([     
    keras.layers.Flatten(), 
    keras.layers.Masking(mask_value=0.0),
    keras.layers.Dense(109, activation=tf.nn.softsign), 
    keras.layers.Dense(50, activation=tf.nn.softmax),
    ]) 
    optimizer=keras.optimizers.Nadam(lr=0.001, beta_1=0.9, beta_2=0.999, epsilon=None, schedule_decay=0.003)
    model.compile(optimizer=optimizer, loss='sparse_categorical_crossentropy', metrics=['accuracy'])
    
    #We had to bring every array to the same length
    train_data=keras.preprocessing.sequence.pad_sequences(train_data, maxlen=None, dtype='int32', padding='pre', truncating='pre', value=0.0)
    train_data=np.array(train_data)
    test_data=keras.preprocessing.sequence.pad_sequences(test_data, maxlen=train_data.shape[1], dtype='int32', padding='pre', truncating='pre', value=0.0)
    test_data=np.array(test_data) 
    history=model.fit(train_data,labels,validation_split=0.15,batch_size=100, epochs=5500 ) 
    
    
    model.summary()  
    print(test_labels.shape,labels.shape)
    print(test_data.shape,train_data.shape)
    # Test Model
    test_loss, test_acc = model.evaluate(test_data, test_labels) 
    print('Test accuracy:', test_acc) 
    print('Test loss:', test_loss)
    visualization(history)
    #Save it
    model.save('simple_mlp5500.pb') 
 ```
## Visualization
Plotting a graph, in order that we can rate our model.
 ```
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
  ```   
# Run all functions
 ```
print("GPU Available: ", tf.test.is_gpu_available()) #GPU rendering speeds up the process
FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL=einlesen()
#Label all the data

FOL=label(FOL,1)   
FKL=label(FKL,1)
BSC=label(BSC,1)
SDL=label(SDL,1)
CHU=label(CHU,0)
SCH=label(SCH,0)
STU=label(STU,0)
STN=label(STN,0)
CSI=label(CSI,0)
CSO=label(CSO,0)
JOG=label(JOG,0)
JUM=label(JUM,0)
SIT=label(SIT,0)
STD=label(STD,0)
WAL=label(WAL,0)

daten_vektor=erstelle_daten_vektor([FOL,FKL,BSC,SDL,CHU,SCH,STU,STN,CSI,CSO,JOG,JUM,SIT,STD,WAL])
daten_matrix=erstelle_daten_matrix(daten_vektor)

train_data, labels,test_data, test_labels=daten_umwandeln()
train_model(train_data,labels, test_data, test_labels)


```

| [Go back to homepage](https://matheli.github.io/Fall_Detection_App_AI/.) | [More project details](https://matheli.github.io/Fall_Detection_App_AI/posts/More%20details.html) | [Code of the evaluation program ](https://matheli.github.io/Fall_Detection_App_AI/posts/Second_model.html) | [The app](https://matheli.github.io/Fall_Detection_App_AI/posts/The_app_code.html) | [Accuracy](https://matheli.github.io/Fall_Detection_App_AI/posts/Accuracy.html) | [The dataset](https://matheli.github.io/Fall_Detection_App_AI/posts/The_dataset.html) | [The team](https://matheli.github.io/Fall_Detection_App_AI/posts/The_team/The_team.html) |

| [Data protection](https://matheli.github.io/Fall_Detection_App_AI/posts/Datenschutzerkl%C3%A4rung) | [Imprint disclaimer](https://matheli.github.io/Fall_Detection_App_AI/posts/Impressum_Haftungsauschluss) |
