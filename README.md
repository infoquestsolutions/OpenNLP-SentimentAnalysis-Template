#OpenNLP Sentiment Analysis

## Overview
An engine template is an almost-complete implementation of an engine. In this Engine Template, we have integrated OpenNLP library.
We perform sentiment analysis based on a sentence provided.

You can customize it easily to fit your specific use case and needs.

We are going to show you how to create your own sentiment analysis engine for production use based on this template.

## Usage

### Event Data Requirements
By default, the template requires the following events to be collected (we can check this at TemplateFolder/data/import_eventserver.py ):

- user $set event, which set the attributes of the user

### Input Query
```
{"sentence": "This is a sentence . "}
```

### Output Predicted Result
- the predicted label 
```
{"sentiment": 2.0}
```

### Dataset 
We will be using a Rotten Tomatoes sample data set from  https://www.kaggle.com/c/sentiment-analysis-on-movie-reviews/data
The training sample events have the following format (Generated by data/import_eventserver.py):
```
client.create_event(
      event="$set",
      entity_type="user",
      entity_id=count, # use the count num as user ID
      properties= {
        "phrase" : data[0],
        "sentiment" : float(data[len(data) - 1])
      }
```
```
This is a sentence .  2
```

## Install and Run PredictionIO
First you need to [install PredictionIO 0.9.1](http://docs.prediction.io/install/) (if you haven't done it).
Let's say you have installed PredictionIO at /home/yourname/PredictionIO/. For convenience, add PredictionIO's binary command path to your PATH, i.e. /home/yourname/PredictionIO/bin
```
$ PATH=$PATH:/home/yourname/PredictionIO/bin; export PATH
```
Once you have completed the installation process, please make sure all the components (PredictionIO Event Server, Elasticsearch, and HBase) are up and running.

```
$ pio-start-all
```
For versions before 0.9.1, you need to individually get the PredictionIO Event Server, Elasticsearch, and HBase up and running.

You can check the status by running:
```
$ pio status
```
## Download Template
Clone the current repository by executing the following command in the directory where you want the code to reside:
    
```
git clone https://github.com/infoquestsolutions/OpenNLP-SentimentAnalysis-Template/ MySentiments
```
## Generate an App ID and Access Key
Let's assume you want to use this engine in an application named "MyApp1". You will need to collect some training data for machine learning modeling. You can generate an App ID and Access Key that represent "MyApp1" on the Event Server easily:
```
$ pio app new MyApp1

```
You should find the following in the console output:
```
...
[INFO] [App$] Initialized Event Store for this app ID: 1.
[INFO] [App$] Created new app:
[INFO] [App$]       Name: MyApp1
[INFO] [App$]         ID: 1
[INFO] [App$] Access Key: 3mZWDzci2D5YsqAnqNnXH9SB6Rg3dsTBs8iHkK6X2i54IQsIZI1eEeQQyMfs7b3F
```
Take note of the Access Key and App ID. You will need the Access Key to refer to "MyApp1" when you collect data. At the same time, you will use App ID to refer to "MyApp1" in engine code.

$ pio app list will return a list of names and IDs of apps created in the Event Server.

```
$ pio app list
[INFO] [App$]                 Name |   ID |                                                       Access Key | Allowed Event(s)
[INFO] [App$]               MyApp1 |    1 | 3mZWDzci2D5YsqAnqNnXH9SB6Rg3dsTBs8iHkK6X2i54IQsIZI1eEeQQyMfs7b3F | (all)
[INFO] [App$]               MyApp2 |    2 | io5lz6Eg4m3Xe4JZTBFE13GMAf1dhFl6ZteuJfrO84XpdOz9wRCrDU44EUaYuXq5 | (all)
[INFO] [App$] Finished listing 2 app(s).
```

## Collecting Data

Next, let's collect some training data. By default, the Engine Template reads 2 properties of a user record: sentence and sentiment.

You can send these data to PredictionIO Event Server in real-time easily by making a HTTP request or through the EventClient of an SDK.

A Python import script import_eventserver.py is provided in the template to import the data to Event Server using Python SDK.
Replace the value of access_key parameter by your Access Key and run:
```python
$ cd MyRecomendation
$ python data/import_eventserver.py --access_key 3mZWDzci2D5YsqAnqNnXH9SB6Rg3dsTBs8iHkK6X2i54IQsIZI1eEeQQyMfs7b3F
```
You should see the following output:
```
Importing data...
117053 events are imported.
```
This python script converts the data file to proper events formats as needed by the event server.
Now the training data is stored as events inside the Event Store.

## Deploy the Engine as a Service
Now you can build, train, and deploy the engine. First, make sure you are under the Template directory.

### Build

Start with building your Sentimant Analysis engine.
```
$ pio build
```
This command should take few minutes for the first time; all subsequent builds should be less than a minute. You can also run it with --verbose to see all log messages.

Upon successful build, you should see a console message similar to the following.
```
[INFO] [Console$] Your engine is ready for training.
```

### Training the Predictive Model

Train your engine.

```
$ pio train
```
When your engine is trained successfully, you should see a console message similar to the following.

```
[INFO] [CoreWorkflow$] Training completed successfully.
```
### Deploying the Engine

Now your engine is ready to deploy.

```
$ pio deploy
```
This will deploy an engine that binds to http://localhost:8000. You can visit that page in your web browser to check its status.


