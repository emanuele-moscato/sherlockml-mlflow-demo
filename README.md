# MLflow on SherlockML

In this demo we see how to use [MLflow](https://www.mlflow.org/) to for auditing and logging machine learning models. We will use two main tools that MLflow offers: a Python API that will be used from within the script or notebook where the model is developed and a UI that will run on a SherlockML interactive server.

For more information, please read the [MLflow documentation](https://www.mlflow.org/docs/latest/index.html).

## Installing MLflow

MLflow can be installed using `pip` (`pip install -U mlflow`). This will make available both the Python interface and the command line interface. To use MLflow from Python, import it as any other library (`import mlflow`).

The UI can be run from the command line with `mlflow ui`, but is exposed as a default through port 5000 (the option to specify an arbitrary port should have been added to the version of MLflow found on [GitHub](https://github.com/databricks/mlflow), but at the time of writing that version is broken and is not usable at all). Since the SherlockML interactive servers only expose port 8888, the UI cannot be seen via the browser as it is. To circumvent this problem, we can use nginx to "mirror" port 5000 over port 8888. To do so, follow the steps below. This however will prevent us from using both Jupyter and the MLflow UI on the same server: at the moment the best thing to do is having one server for each service.

### 1 - Install nginx
Open the command line of a SherlockML server running Jupyter and execute `sudo apt-get install nginx`.

### 2 - Write a configuration file for nginx
Create a file `nginx_5000_to_8888.conf` containing (you can actually find it in the repository):
```
worker_processes  1;
daemon            off;
error_log         /dev/stdout debug;

events {
    worker_connections  1024;
}

http {
    default_type       application/octet-stream;
    sendfile           on;
    keepalive_timeout  65;
    access_log         /dev/stdout;
    server_tokens      off;

    server {
        listen       8888;
        server_name  localhost;

        location / {
            proxy_pass http://localhost:5000;
        }
    }
}
```
Copy the above file to the folder `/usr/share/nginx/` (use `sudo` to do this), which is where nginx looks for configuration files by default.

### 3 - Create a server
The Jupyter server and the MLflow UI cannot both listen to port 8888, so the Jupyter server must be stopped before proceeding. To do this, open he server's command line and execute `sudo sv stop jupyter`.

### 4 - Run nginx and the MLflow UI
There are two possible ways to do this. Notice that in order to be able to see the logged data for the runs, the mlflow UI should be launched from the folder parent to `/mlruns/`, which is where the information is stored.

#### Run nginx in one terminal and the MLflow UI in another one
Open a terminal and run `sudo nginx -c nginx_5000_to_8888.conf`, then open another terminal on the same server and run `mlflow ui`.

#### Run nginx as a background process and the MLflow UI as a foreground process
Open the terminal and run `nohup sudo nginx -c nginx_5000_to_8888.conf > /dev/null 2>&1 &` to run nginx in the background. Then run `mlflow ui`.

### 5 - Open the MLflow UI
You should be able to opend the MLflow UI in the browser just by clicking on the name of the SherlockML server on which it runs.













