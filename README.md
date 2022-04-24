## Wafer Fault Detection Prediction API

#### App URL :  https://wafer-fault-app-bob.herokuapp.com/
#### Docker Image to pull : docker pull abhishekmazudmar94/waferbob2694

#### API testing UI 
If the Default Prdiction Route is clicked, then the app picks up the location where the batch data is available for prediction.
The Display window also shows some of the predictions in the format "{<wafer>:<wafer ID>,<Prediction>:-1/+1}"
![](https://github.com/mazumdarabhishek/Wafer_Fault_Detection_App/blob/main/LLD/Screenshots/Screenshot%20at%202021-02-25%2011-52-44.png)
    

#### Pipeline Design Overview
This image explains the pipelines through wihich the data will flow incase of training as well as prediction. 
Based on this design, the code is then prepared in a modular fashion meeting the pipeline requirements.
![](https://github.com/mazumdarabhishek/Wafer_Fault_Detection_App/blob/main/LLD/Screenshots/system_design.jpg)

#### Problem Statement:
    
Wafer (In electronics), also called a slice or substrate, is a thin slice of semiconductor,
such as a crystalline silicon (c-Si), used for fabricationof integrated circuits and in photovoltaics,
to manufacture solar cells.

The Goals of this Project are:
- To reduce manpower depenedency for wafer health check as these units can be stationed at a very wide range of 
  locations which makes maintenance very cumbersome and expensive for the business 
- To accelerate fault detection and isolate manpower to only those units for mainenace. This improves preventive
  maintenance capabilities of the business

Labeled data is provided by the client having two classes +1 & -1 where:  
- +1: Means that the wafer is in a working condition and it doesn't need to be replaced.
- -1: Means that the wafer is faulty and it needa to be replaced.

Machine Learning approcah has been taken to make a binary classification by consuming the wafer sensor data produced 
in batches as per region. This project will act as an API for the client's Web enterprice where the location of the 
batch files will be provided as an input to the API and this API will run all the pre processing steps of Data Science
Life Cycle and produce Predictions as a csv file and save it in a specified folder for furhter usage of the maitenance team
    
#### Data Description
    
The client will send data in multiple sets of files in batches at a given location.
Data will contain Wafer names and 590 columns of different sensor values for each wafer.
The last column will have the "Good/Bad" value for each wafer.

Apart from training files, we laso require a "schema" file from the client, which contain all the
relevant information about the training files such as:

Name of the files, Length of Date value in FileName, Length of Time value in FileName, NUmber of Columnns, 
Name of Columns, and their dataype.
    
#### Data Validation

In This step, we perform different sets of validation on the given set of training files.

Name Validation: We validate the name of the files based on the given name in the schema file. We have 
created a regex patterg as per the name given in the schema fileto use for validation. After validating 
the pattern in the name, we check for the length of the date in the file name as well as the length of time 
in the file name. If all the values are as per requirements, we move such files to "Good_Data_Folder" else
we move such files to "Bad_Data_Folder."

Number of Columns: We validate the number of columns present in the files, and if it doesn't match with the
value given in the schema file, then the file id moves to "Bad_Data_Folder."

Name of Columns: The name of the columns is validated and should be the same as given in the schema file. 
If not, then the file is moved to "Bad_Data_Folder".

The datatype of columns: The datatype of columns is given in the schema file. This is validated when we insert
the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder."

Null values in columns: If any of the columns in a file have all the values as NULL or missing, we discard such
a file and move it to "Bad_Data_Folder".
    
#### Data Insertion in Database
     
 Database Creation and Connection: Create a database with the given name passed. If the database is already created,
 open the connection to the database.

 Table creation in the database: Table with name - "Good_Data", is created in the database for inserting the files 
 in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already
 present, then the new table is not created and new files are inserted in the already present table as we want 
 training to be done on new as well as old training files.

 Insertion of file in the table: All the files in the "Good_Data_Folder" are inserted in the above-created table. If
 any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to 
 "Bad_Data_Folder".
     
#### Model Training
    
Data Export from Db: The data in a stored database is exported as a CSV file to be used for model training.
Data Preprocessing: 
  Check for null values in the columns. If present, impute the null values using the KNN imputer.
  Check if any column has zero standard deviation, remove such columns as they don't give any information during 
  model training.      
  
Clustering: 
  KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters 
  is selected

Training: 
  An Automated training approach is used where the models that performed the best during initial observations
  phase are kept as a choice and the model having the highest AUC_ROC_Score will be selected for that specific
  cluster and then will be saved in a model directory to be used for model predicition. 

#### Prediction 


  Same pipeline as that of Training will be executed and then depending on the cluster number, sub_batches of 
  records will have their predictions by the best model saved for that specific cluster during the training phase.
  A .csv file will be then saved in a locaton from where it can be doenloaded and used for maintenance activity. 


#### Deployment

A dockerised has been followed and CI/CD pipeline is establised using circleci. The Aplication is hosted on Heroku. 




    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
       
    
   
    
