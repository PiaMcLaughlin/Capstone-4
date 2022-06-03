MLOps with   Kubeflow Pipelines

In capstone 4 you will demonstrate how to set up and deploy a simple ML CI/CD pipeline using Google Clouds AI Platform, Kubeflow and Docker.

Objectives:
1.	Build each task as a docker container and run the containers with cloud build
    Preprocessing step: Load data from GCS bucket, edit and store as a new file
    Training: Create a Pytorch   model to build a custom prediction routine (TensorFlow)
    Deployment: Deploy your custom model to the AI Platform with version control
2.	Create a Kubeflow pipeline to connect with the objectives above
3.	Perform CI by building GitHub Triggers in Cloud Build that rebuilds containers upon a push to repository
4.	CD by using Cloud Function to trigger upon uploading new data to the GCS bucket

Setting up the Kubeflow Pipeline  
1.	Clone the Git repository 
    Link to Git clone here 
2.	Create a Kubeflow pipeline in GCP
3.	Create two buckets in GCS, one for the project and one for data
    {capstone4_initals)_bucket and {capstone4_initals}_data_bucket
    
Do the following in the {capstone4_initals)_bucket 
1.	Create a package from the models directory in the containers/train folder by running: $ python containers/train/models/setup.py sdist , this creates a package with pytorch and the model structure, just drag and drop it to the package subfolder. 
2. Create a docker container for each step. Do this by running: $ gcloud_CAP4_demo/containers./build_containers.sh from the Cloud Shell
    This will run “build_single_containers.sh in each directory”
3.	Each container (subfolder) includes >Details
4.	To test the container manually run
    $ docker run –t gcr.io/{YOUR_PROJECT}/{IMAGE}:latest –project {YOUR_PROJECT} --bucket {YOUR_BUCKET} local
5.	Now run the container that deploys the model to AI platform run:
    $ docker run –t gcr.io/ml-pipeline-309409/ml-demo-deploy-toai
6.	Create a pipeline in python using the Kubeflow API 
    The pipeline can runas a script or manually at the pipeline dashboard from 1
**In order to run the pipeline, you need to have the appropriate role
 
Continuous Testing (CI)
To set up CI and rebuild at every push:
1.	Connect gcloud to GitHub, either in the Trigger UI or run:
    $ ./scripts setup_trigger.sh
2.	Push the newly created Cloud builds from GCP into the origin otherwise the trigger won’t find them
3.	This trigger will run every time a push to master happens in any of the containers and thus rebuild the affected Docker Image

Continuous Delivery (CD)
CD is necessary when we want to retrain/finetune the model give that we can get new data, not every time we update a component. So we will have a Cloud function that will trigger a training pipeline when we upload new data to the Cloud Storage.
1.	Get the pipeline host url from pipeline settings
2.	In the pipeline folder, run the deploy script
    $ ./deploy_cloudfunction $PIPELINE_HOST
3.	When a new file is added or deleted from the {cap4_yourinitals}_bucket, it will rerun the pipeline
