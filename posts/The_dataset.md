# The dataset 
We are using the MobiAct Dataset. It includes four different types of falls and eleven types of activities of daily living. 
The files were generate by 66 subjects. Their age, height, gender and weight are different.

All in all the set hast about 3300 CSV-files. We didn't imported all files in our model. The files contain data of the accerometer, gyroscope and orientation.  

## Insight in the dataset
![](https://raw.githubusercontent.com/MatheLi/BWKI/master/res/Verteilung_Sturz_ADL.png?raw=true)

In this picture you can see the distribution of the two labels. There are more activities of daily living (ADL) then falls.

![](https://github.com/MatheLi/BWKI/blob/master/res/sample_FOL.png?raw=true)

Here are some example values of a fall file. The column "label" was added by our program to process the data better:


![](https://github.com/MatheLi/BWKI/blob/master/res/describe_FOL_(3).png?raw=true)
This picture describe the data of a fall. 

## Data preprocessing
'''

for a in range(1,4): # 3 tries for everyone
for i in range(1,67): # 66 subjects
if os.path.exists(path+"_{0}_{1}_annotated.csv".format(i,a)): #check if path exist
data=pd.read_csv(path+"_{0}_{1}_annotated.csv".format(i,a),usecols=["rel_time","acc_x","acc_y","acc_z","gyro_x","gyro_y","gyro_z","azimuth","pitch","roll"])
 
'''
This code excerpt shows the read in of the 3300 files in a Pandas dataframe. In the further course we added a label to the dataframe and changed it in a Numpy Array.
To ensure that every file has the same length, we used this command:
'''
keras.preprocessing.sequence.pad_sequences(data, maxlen=train_data.shape[1], dtype='int32', padding='pre', truncating='pre', value=0.0) 
'''
In addition we wrote an extra script, which cuts all CSV-files, which were longer then 10 seconds.

##Ciation to use the dataset: 
[Website](https://bmi.teicrete.gr/en/home/)
[Dataset Download](https://drive.google.com/file/d/0B5VcW5yHhWhieWc5WHlIN0twcUE/edit)
G. Vavoulas, M. Pediaditis, E. Spanakis, M. Tsiknakis, The MobiFall Dataset:
An Initial Evaluation of Fall Detection Algorithms Using Smartphones, 
6th IEEE International Symposium on Monitoring & Surveillance Research (ISMSR): 
Healthcare-Bioinformatics, Chania, Greece, 2013, DOI:10.1109/BIBE.2013.6701629.