<div align="center">
<img src="https://marketplace.deep-hybrid-datacloud.eu/images/logo-deep.png" alt="logo" width="300"/>
</div>

# DEEP-OC-unet
[![Build Status](https://jenkins.indigo-datacloud.eu/buildStatus/icon?job=Pipeline-as-code/DEEP-OC-org/UC-adnaneds-DEEP-OC-unet/main)](https://jenkins.indigo-datacloud.eu/job/Pipeline-as-code/job/DEEP-OC-org/job/UC-adnaneds-DEEP-OC-unet/job/main)

This is a container that will run the [unet](https://github.com/adnaneds/unet) application leveraging the DEEP as a Service API component ([DEEPaaS API V2](https://github.com/indigo-dc/DEEPaaS)).

    
## Running the container

### Directly from Docker Hub

To run the Docker container directly from Docker Hub and start using the API simply run the following command:

```bash
$ docker run -ti -p 5000:5000 -p 6006:6006 -p 8888:8888 deephdc/uc-adnaneds-deep-oc-unet
```

This command will pull the Docker container from the Docker Hub [adnaneds](https://hub.docker.com/u/adnaneds/) repository and start the default command (`deepaas-run --listen-ip=0.0.0.0`).

**N.B.** For either CPU-based or GPU-based images you can also use [udocker](https://github.com/indigo-dc/udocker).

### Building the container

If you want to build the container directly in your machine (because you want to modify the `Dockerfile` for instance) follow the following instructions:
```bash
git clone https://github.com/adnaneds/DEEP-OC-unet
cd DEEP-OC-unet
docker build -t deephdc/uc-adnaneds-deep-oc-unet . --no-cache
docker run -ti -p 5000:5000 -p 6006:6006 -p 8888:8888 deephdc/uc-adnaneds-deep-oc-unet
```

These three steps will download the repository from GitHub and will build the Docker container locally on your machine. You can inspect and modify the `Dockerfile` in order to check what is going on. For instance, you can pass the `--debug=True` flag to the `deepaas-run` command, in order to enable the debug mode.


## Connect to the API

Once the container is up and running, browse to http://0.0.0.0:5000/ui to get the [OpenAPI (Swagger)](https://www.openapis.org/) documentation page.


## Project structure
```
├─ Dockerfile             <- Describes main steps on integration of DEEPaaS API and
│                            <your_project> application in one Docker image
│
├─ Jenkinsfile            <- Describes basic Jenkins CI/CD pipeline
│
├─ LICENSE                <- License file
│
├─ README.md              <- README for developers and users.
│
└── metadata.json         <- Defines information propagated to the DEEP Marketplace
```

You can validate the `metadata.json` before making a git push using:
```shell
pip install git+https://github.com/deephdc/schema4apps
deep-app-schema-validator metadata.json
```
