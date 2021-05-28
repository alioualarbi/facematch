# Facematch : Running Automl container exports on Cloud Run

Context: https://groups.google.com/a/google.com/d/topic/ml-discuss/_L0aSD-rheA/discussion

For scenarios with many, low QPS models, hosting on AutoML infrastructure with 24/7 pricing can feel very expensive.

It is possible to serve the trained model with Cloud-Run, which scales to zero and is pay per request.

## Build the container image

Set the path to the exported model

This should be the full path to the dir that contains the `saved_model.pb` file.

    export MODEL_EXPORT=gs://xxxxxxxxxxxxxx/xxxxxxxxxxxxxxxx_tf-saved-model

Set the name used for the image and cloud-run service:

    export SERVICE_NAME=face
    export PROJECT_ID=<project_id>

Build and deploy the container:

    gcloud builds submit --config cloudbuild.yaml --substitutions "_MODEL_EXPORT=$MODEL_EXPORT,_MODEL_SERVICE=$SERVICE_NAME" .

Note that the [project-num-id]@cloudbuild.gserviceaccount.com service account needs `Service Account User` and `Cloud Run Admin` permissions in the project to deploy cloud-run from cloud-build.

## Call the running model

Assume you have set up a python virtualenv with the included requirements.txt

Get the URL for the Cloud Run Service:

    export RUN_HOST=$(gcloud run services describe $SERVICE_NAME --format="value(status.address.hostname)")

Run the client with the sample image:

    python face_detect_demo.py
