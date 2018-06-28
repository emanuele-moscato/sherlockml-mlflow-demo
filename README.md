# MLflow on SherlockML

In this demo we see how to use [MLflow](https://www.mlflow.org/) to for auditing and logging machine learning models. We will use two main tools that MLflow offers: a Python API that will be used from within the script or notebook where the model is developed and a UI that will run on a SherlockML interactive server.

For more information, please read the [MLflow documentation](https://www.mlflow.org/docs/latest/index.html).

## Installing MLflow

MLflow can be installed using `pip` (`pip install -U mlflow`). This will make available both the Python interface and the command line interface. To use MLflow from Python, import it as any other library (`import mlflow`).

The UI can be run from the command line, specifying to expose port 8888. Beore doing that, the Jupyter server must be stopped: open the command line and run `sudo sv stop jupyter`, then run `mlflow ui -p 8888` and click on the server's name to open the MLflow UI. This will prevent us from using both Jupyter and the MLflow UI on the same server: at the moment the best thing to do is having one server for each service, both with MLflow installed but one running the Jupyter server and the other one the MLflow UI one.

Notice that in order to be able to see the the logs, the UI must be run from the folder parent to the `mlruns/` one.

## Using MLflow for model selection
Have a look at the example notebook to see how MLflow can be used to log multiple runs of a machine learning model corresponding to different choices of the parameters and to select the one that give the best performance. The models are saved as well and can be loaded through MLflow.













