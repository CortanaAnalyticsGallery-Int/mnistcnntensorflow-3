#Read Me


This project demonstrates on how to use Vienna for recognizing hand written digits using CNN based neural network through Tensor Flow. **In this project, we intentionally didn't used readily available layers "*tf.layers.conv2d()*" but rather created layers using raw neural network functions available in tensorflow.**


##Training


Run cifair.py in a local Docker container

```
    $ az ml execute start -c docker cifair.py
```

Download all files in output directory to your project.

- outputs/mnist - this folder has all model files
- outputs/tflogs - this folder has event logs for tensor board

##Scoring


Run scoring.py in a local Docker container

```
    $ az ml execute start -c docker scoring.py
```

Ensure accuracy from this run is very close to the accuracy from training.

Now run mnistimgscore.py in a local docker container

```
    $ az ml execute start -c docker mnistimgscore.py
```

Make sure the run is successful and you are able to see scored probabilities and labels for sample images

##Deployment



Setup environment and publish web service

```
	#setup environment
	az login
	az ml hostacct create -l eastus2euap -g amlviennagrp --sku-name S1 --sku-tier Standard -n myhstacct # this command was skipped along with other MMS commands
	az ml env setup

	source ~/.amlenvrc
	docker login wino16nenvacr.azurecr.io
	#create web service
	az ml service create realtime -n mnist_webservice -c aml_config/conda_dependencies.yml --model-file ./mnist -f mnistimgscore.py -s mnistschema.json -r spark-py -l
```


##Consumption



Consume by calling client.py or curl. Execute the  curl command using data stored in the file provided at this [link](http://neerajkh.blob.core.windows.net/images/tmp)

```
	# local docker execution
	curl -X POST -H "Content-Type:application/json" -d @tmp http://127.0.0.1:32787/score
	# remote cluster execution
	curl -X POST -H "Content-Type:application/json" -d @tmp http://52.170.83.20:80/api/v1/service/mnistws/score
```

You can also consume Web service on sample images by executing client.py program

```
	python client.py
	
	images/5.png :
	   	scored labels  scored probabilities
	0              5                   1.0
	images/6.png :
   		scored labels  scored probabilities
	0              6                   1.0
	images/7.png :
   		scored labels  scored probabilities
	0              7                   1.0
	images/2.png :
   		scored labels  scored probabilities
	0              2                   1.0
```

