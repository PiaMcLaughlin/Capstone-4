<img src = "https://www.gend.co/hs-fs/hubfs/gcp-logo-cloud.png?width=730&name=gcp-logo-cloud.png" align="right" height = 50 width = 60/>

# MLOPS with Kubeflow Pipelines in GCP

<img src ="https://logos-world.net/wp-content/uploads/2021/02/Docker-Logo-2013-2015.png" align = "right" height = 50 width = 70 />


In capstone 4 you will demonstrate how to set up and deploy a simple ML CI/CD pipeline using Google Clouds AI Platform, Kubeflow and Docker.

## ✍ Authors
* Pia-Auora McLaughlin, [PiaMcLaughlin](https://github.com/PiaMcLaughlin)
* Sabeel Rehman, [SabeelRehman](https://github.com/sabeelrehman)
* Amit Som, [AmitSom](https://github.com/DeeDossAttk)

## 🗺 Objectives
1. Build each task as a docker container and run the containers with cloud build
	a. Preprocessing step: Load data from GCS bucket, edit and store as a new file
	b.Training: Create a Pytorch   model to build a custom prediction routine (TensorFlow)
	c. Deployment: Deploy your custom model to the AI Platform with version control
2. Create a Kubeflow pipeline to connect with the objectives above
3. Perform CI by building GitHub Triggers in Cloud Build that rebuilds containers upon a push to repository
4. CD by using Cloud Function to trigger upon uploading new data to the GCS bucket


<p align="center">
  <img width="600" alt="gcloud_meme" src="https://i.redd.it/hll26nwmlsu31.jpg">
</p>


<img src = "https://global-uploads.webflow.com/5d3ec351b1eba4332d213004/5ec509611b60fb7f14e7e7ce_kubeflow-logo.png" align="right" height = 50 width = 60/>

## 🌉 Setting up the pipeline 
1.	Clone the [Git repository](https://github.com/PiaMcLaughlin/Capstone-4.git) 
2.	Create a Kubeflow pipeline in GCP
3.	Create two buckets in GCS, one for the project and one for data
    `{capstone4_initals)_bucket` and `{capstone4_initals}_data_bucket`
    
Do the following in the {capstone4_initals)_bucket 
1.	Create a package from the models directory in the containers/train folder by running: `$ python containers/train/models/setup.py sdist`, this creates a package with pytorch and the model structure, just drag and drop it to the package subfolder. 
2. Create a docker container for each step. Do this by running: 
3. `$ gcloud_CAP4_demo/containers./build_containers.sh` from the Cloud Shell
    This will run 
    `build_single_containers.sh` in each directory
3.	Each container (subfolder) includes >Details
4.	To test the container manually run
    $ docker run 
    `–t gcr.io/{YOUR_PROJECT}/{IMAGE}:latest –project {YOUR_PROJECT} --bucket {YOUR_BUCKET} local`
5.	Now run the container that deploys the model to AI platform run:
    $ docker run 
    `–t gcr.io/ml-pipeline-309409/ml-demo-deploy-toai`
6.	Create a pipeline in python using the Kubeflow API 
    The pipeline can runas a script or manually at the pipeline dashboard from 1

**In order to run the pipeline, you need to have the appropriate role**

<p align="center">
  <img width="600" alt="cicd" src="![image](https://user-images.githubusercontent.com/32612535/172225468-265eed77-bc81-4018-867b-149d682d210b.png)">
</p>

## 🛠 CI
Continuous Testing (CI) To set up CI and rebuild at every push:

Connect gcloud to GitHub, either in the Trigger UI or run: 
	`$ ./scripts setup_trigger.sh`
Push the newly created Cloud builds from GCP into the origin otherwise the trigger won’t find them
This trigger will run every time a push to master happens in any of the containers and thus rebuild the affected Docker Image

## 📦 CD
Continuous Delivery (CD) CD is necessary when we want to retrain/finetune the model give that we can get new data, not every time we update a component. So we will have a Cloud function that will trigger a training pipeline when we upload new data to the Cloud Storage.

Get the pipeline host url from pipeline settings
In the pipeline folder, run the deploy script 
	`$ ./deploy_cloudfunction $PIPELINE_HOST`
When a new file is added or deleted from the 
`{cap4_teamname}_bucket`
, it will rerun the pipeline


## 👓 Resources
* [Deploy your own custom model on GCP’s AI platform](https://medium.com/searce/deploy-your-own-custom-model-on-gcps-ai-platform-7e42a5721b43)
* [How to carry out CI/CD in Machine Learning (“MLOps”) using Kubeflow ML pipelines](https://medium.com/google-cloud/how-to-carry-out-ci-cd-in-machine-learning-mlops-using-kubeflow-ml-pipelines-part-3-bdaf68082112)
* [GCP documentation on model deployment](https://cloud.google.com/ai-platform/prediction/docs/deploying-models)
* [What is CI/CD?](https://www.redhat.com/en/topics/devops/what-is-ci-cd)
* [Kubeflow](https://www.kubeflow.org/docs/components/pipelines/introduction/)


## 📽 Video Demo (Upload to YT?. Just record what I do)
Demo video [here](link goes here). 
