```
# -*- coding: utf-8 -*-
"""
Created on Tue Jun 11 19:03:06 2019

@author: mario
"""

import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

import numpy as np

def einlesen():
    """
    liest alle Stürze ein und 2 ADL (CHU und SCH)
    Anschließend werden alle Textdateien in einer Liste gereiht zurückgegeben
    Nur die Ordner (also welcher Sturz oder welche ADL) wird getrennt
    """
    #FALLS
    FOL=[]
    FKL=[]
    BSC=[]
    SDL=[]
    
    #ADL
    CHU=[]
    SCH=[]
    
    #FALLS
    #FOL
    for a in range(1,4): # 3 Trials
        for i in range(1,68): #67 Subjects
            if i == 24 or i== 41 or i==50: #Fehlende Dateien (immer 24,41,50??)
                #do nothing
                i=i*1
            else:
                FOL.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/FOL/FOL_acc_{0}_{1}.txt".format(i,a),dtype=float ,delimiter=",",skiprows=16))
                # 16 Zeilen Übersprungen, da dort nur Infos, Immer an FOL angehängt
    #FKL
    for a in range(1,4):    
        for i in range(1,68): 
            if i == 24 or i==41 or i==50:           
                i=i*1
            else:
                FKL.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/FKL/FKL_acc_{0}_{1}.txt".format(i,a),dtype=float ,delimiter=",",skiprows=16))    
    #BSC
    for a in range(1,4): 
        for i in range(1,68): 
            if i == 24 or i== 41 or i==50 or i==3: 
                i=i*1
            else:
                BSC.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/BSC/BSC_acc_{0}_{1}.txt".format(i,a),dtype=float ,delimiter=",",skiprows=16))    
     #SDL
    for a in range(1,4): 
        for i in range(1,68): 
           if i == 24 or i== 41 or i==50: 
               i=i*1
           else:
               SDL.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/SDL/SDL_acc_{0}_{1}.txt".format(i,a),dtype=float ,delimiter=",",skiprows=16))    

    #ADL
    #CHU
    for a in range(1,6): #Ab hier 6 Trials!
        for i in range(1,68): 
            if i==1 or i==2 or i==3 or i==5 or i==6 or i==12 or i==20 or i==45 or i==53 or i==58 or i>57:
            # Warum fehlen hier so viele Daten? Achtung hier ist die If-Abfrage umgedreht!
                CHU.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/CHU/CHU_acc_{0}_{1}.txt".format(i,a),dtype=float ,delimiter=",",skiprows=16))
            else:
                i=i*1
    #SCH
    for a in range(1,6): 
        for i in range(1,68): 
            if i==13 or i==14 or i==15 or i==17 or i==30 or i==31 or i==21 or i==51:
                i=i*1
            else:
                 SCH.append(np.loadtxt("MobiAct_Dataset_v2/Raw Data/SCH/SCH_acc_{0}_{1}.txt".format(i,a),dtype=float ,delimiter=",",skiprows=16))
    #Kann man sich diesen Teil irgendwie sparen?? Umwandlung in np.arrays()
    #Falls
    FOL=np.array(FOL)
    FKL=np.array(FKL)
    BSC=np.array(BSC)
    SDL=np.array(SDL)
    #ADL
    CHU=np.array(CHU)
    SCH=np.array(SCH)
    return FOL,FKL,BSC,SDL,CHU,SCH #Zurück geben der eingelesenen Ordner

def differenz(Daten):
    """
    Bildet aus einem übergebenen Ordner(!) aus jeder .txt Datei eine Differenz 
    von dem höchten und niedrigsten Punkt, der X-Achse!
    """
    max=[]
    min=[]
    for a in range(Daten.shape[0]):
        min.append(np.min(Daten[a][:,1]))  #min den kleinsten Wert aus einer .txt-Datei von der ganzen X-Achse anhängen
        max.append(np.max(Daten[a][:,1]))
    max=np.array(max)  #Min und max zu Np.arrays umwandeln- Kann man sich das sparen?
    min=np.array(min)       
    differenz=max-min #Differenz bilden (5+differenz=10 -> differenz=10-5)
    return differenz #Zurückgabe von der Differenz -> pro Textdatei ein Eintrag

def daten_aufbereiten(FOL,FKL,BSC,SDL,CHU,SCH):
    """
    Kennzeichnet die Daten mit einem Label
    Außerdem wird mit der "differenz(Daten)" Funktion die Differenz der X-Achse 
    (zwischen dem höchsten und niedrigsten Punkt) gebildet
    1 steht hier für einen Sturz und steht vor der Differenz
    0 steht hier für eine ADL und steht ebenfalls vor der Differenz
    """
    daten_vektor=[] #Definieren einer Liste
    #Falls (1)
    for i in range(FOL.shape[0]): #Anzahl der .txt Dateien (also anzuhängenden Differenzen) ermitteln
        daten_vektor.append(1) #FOL ist ein Sturz, d.h 1
        daten_vektor.append(differenz(FOL)[i]) # und hinter die 1 noch die frisch gebildete Differenz schreiben
    for i in range(FKL.shape[0]):
        daten_vektor.append(1) # gleich zu FOL
        daten_vektor.append(differenz(FKL)[i])
    for i in range(BSC.shape[0]):
        daten_vektor.append(1)
        daten_vektor.append(differenz(BSC)[i])
    for i in range(SDL.shape[0]):
        daten_vektor.append(1)
        daten_vektor.append(differenz(SDL)[i])
    #ADL (0)
    for i in range(CHU.shape[0]):
        daten_vektor.append(0) #CHU ist ein Sturz, d.h 0
        daten_vektor.append(differenz(CHU)[i])# und hinter die 0 noch die frisch gebildete Differenz schreiben
    for i in range(SCH.shape[0]):
        daten_vektor.append(0)
        daten_vektor.append(differenz(SCH)[i])
    return daten_vektor 
def matrix_erstellen(daten_vektor):
    """
    Um die Daten mit Tensorflow weiter verarbeiten zu können, dürfen sie kein Vektor mehr sein.
    Deshalb werden sie in eine Matrix umgewandelt...
    
    Die daten_matrix sieht so aus (Beispielwerte!):
        0    1
        25   1
        23   1
        ...  ...
        15   0
        Wert Label
            -> daten_matrix[0] sind die Differenzen des höchsten und niedrigsten Punkt der einzelnen Textdateien (Array!)
            -> daten_matrix[1] sind die Labels (Array!)
            Um das passende Label einer Differenz zu finden, muss man nur die Selbe Zeile aus dem Array auswählen
            -> Passendes Label für den 0. Wert (daten_matrix[0,0]) ist  daten_matrix[1,0]
            """
    daten_matrix=([],[]) #Erstellen einer 2D Liste
    for i in range(1,len(daten_vektor),2): #Alle Differenzen an die 0. Stelle
        daten_matrix[0].append(daten_vektor[i])    
    for i in range(0,len(daten_vektor),2): # Und alle Labels an die 1. Stelle
        daten_matrix[1].append(daten_vektor[i]) 
    daten_matrix=np.array(daten_matrix) # Kann man sich diesen Teil sparen?
    print("Die Größe der Matrix ist:",daten_matrix.shape)
    return daten_matrix
FOL,FKL,BSC,SDL,CHU,SCH=einlesen() # Variabeln einlesen und mit Namen abspeichern

daten_vektor=daten_aufbereiten(FOL,FKL,BSC,SDL,CHU,SCH) #Daten werden für die Klassifizierung mit der X-Achse aufbereitet
daten_matrix=matrix_erstellen(daten_vektor)

print("Aufteilen der Matrix in Trainingsdaten (~92%) und Test Daten (~8%)")

test = daten_matrix[0,:100],daten_matrix[1,:100] #Die Testdaten sind die ersten 100 Werte
training= daten_matrix[0,100:],daten_matrix[1,100:] # und die Trainingsdaten die restlichen 1055 Daten
training=np.array(training) # Kann man sich diesen Teil sparen?
test=np.array(test) # Kann man sich diesen Teil sparen?
print("Aufteilen Erfolgreich! Anzahl der Trainingsdaten:{0} und der Testdaten:{1} ".format(training[1].shape,test[1].shape)) 


model = keras.Sequential([      #Hier wird das Modell erstellt
    keras.layers.Flatten(),     #Mit 3 Layern. 
    #Ich bin mir aktuell mit der "input_size" nicht im klaren. Aber es hat geholfen, nichts anzugeben...
    keras.layers.Dense(500, activation=tf.nn.relu),  #Layer
    keras.layers.Dense(10, activation=tf.nn.softmax) #Layer
    ])

model.compile(optimizer='adam', loss='sparse_categorical_crossentropy', metrics=['accuracy'])
# Lässt das Model kom­pi­lie­ren und weiterer Einstellungen (z.B Optimizer)

model.fit(training[0], training[1], epochs=9) # Trainieren des Model über 9 Epochen mit den Trainingsdaten
# ACC ~0.99 auf die Trainingsdaten

test_loss, test_acc = model.evaluate(test[0], test[1]) #Ermitteln von loss und acc an den Test Daten
#Die können sich von den Werten des Trainings unterscheiden, tun sie aber diesmal nicht...
print('Test accuracy:', test_acc) #Ebenfalls 0.99, ich schätze, dass liegt daran, weil das Model sehr einfach ist
print('Test loss:', test_loss) #Loss unterscheidet sich minimal (oder ist einfach nur gerundet...)

#Versuch ob das Model wirklich funktioniert
print("Versuch ob das Model wirklich funktioniert")
print("1000. Wert und Label:",training[0,1000],training[1,1000]) #Z.B 1000. Wert ist ~7.432 und ist ein ADL (0)
predictions=model.predict(training[0]) #Einmal den ganzen Trainingsvektor durchlaufen lassen
print("Vorhergesagtes Label:",np.argmax(predictions[1000])) # Und das 1000 Label ist auch eine 0
# Mit eigenem wert
print("Erfundener Wert: 0.8, sollte ein ADL sein...")
predictions=model.predict([0.8])
print("Und es stimmt auch:",np.argmax(predictions[0]))

print("Orginal Aufnahme:")
adl_aufnahme=[]
sturz_aufnahme=[]
adl_aufnahme_differenz=[]
for i in range(1,4):
    adl_aufnahme.append(np.loadtxt("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\eigene_aufnahmen\zadl\{0}.txt".format(i),dtype=float ,delimiter=","))
for i in range(1,4):
    sturz_aufnahme.append(np.loadtxt("D:\OneDrive\BWKI_Projekt_Arbeit\MobiAct_Dataset_v2\eigene_aufnahmen\sturz\{0}.txt".format(i),dtype=float ,delimiter=","))
max=[]
min=[]
#adl_aufnahme=sturz_aufnahme #unschöne art sich Code zu sparen
adl_aufnahme=np.array(adl_aufnahme)


for a in range(3):
    min.append(np.min(adl_aufnahme[a][:,1]))  #min den kleinsten Wert aus einer .txt-Datei von der ganzen X-Achse anhängen
    max.append(np.max(adl_aufnahme[a][:,1]))
print(max)
print(min)
    
max=np.array(max)  #Min und max zu Np.arrays umwandeln- Kann man sich das sparen?
min=np.array(min)       
differenz=max-min #Differenz bilden (5+differenz=10 -> differenz=10-5)
predictions=model.predict([differenz[:]])
print("Vorhersage",np.argmax(predictions[0]))
```
| [Go back to homepage](https://matheli.github.io/BWKI/.) | [More project details](https://matheli.github.io/BWKI/posts/More%20details.html) | [Code of the second model](https://matheli.github.io/BWKI/posts/Second_model.html)
