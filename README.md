# Monitoring Openshift/Kubernetes with NexClipper

NexClipper is the container and container orchestration monitoring and performance management solution specialized in Docker, Openshift, Kubernetes. NexClipper Cloud especially supports machine learning based predictive, forecasting, anomaly detection.

## Architecture Overview

![](docs/images/NexClipper_Architecture.png)

## NexClipper Dashboard

![](docs/images/NexClipper_dashboard.png)

## Quick Install

NexClipper can be deployed on Openshift/Kubernetes cluster. 

### Prerequisites

- Installed Openshift/Kubernetes Cluster 

### Prepare deployment

#### Step #1 Create project/namespace and do proper patching

```
oc new-project nexclipperagent
oc new-project nexclipper

oc patch namespace nexclipper -p '{"metadata":{"annotations":{"openshift.io/node-selector":"region=infra"}}}'
oc patch namespace nexclipperagent -p '{"metadata":{"annotations":{"openshift.io/node-selector":""}}}'
```

#### Step #2 Setup Redis

```
oc project nexclipper
oc create -f redis/deployment.yaml
oc create -f redis/service.yaml
```

#### Step #3 Setup Mysql

```
oc create -f mysql/mysql-pvc.yaml
oc create -f mysql/deployment.yaml
oc create -f mysql/service.yaml
```

- Create Mysql table and data
```
oc cp mysql/load.sql <mysql pod>:/tmp/
  oc rsh <mysql pod> 
  > mysql -uadmin -ppassword
  > use defaultdb
  > source load.sql
```

#### Step #3 Setup Influxdb

```
oc create -f influx/mysql-pvc.yaml
oc create -f influx/deployment.yaml
oc create -f influx/service.yaml
```

> #### rabbitmq (or kafka)

- create
```sh
  $ kubectl create -f <yaml/rabbitmq/deployment.yaml>
  $ kubectl create -f <yaml/rabbitmq/service.yaml>
```

- [If you want to use Kafka instead of RabbitMQ, Go to](https://github.com/NexClipper/NexClipper/blob/dev/docs/option/kafka.md)


### NexClipper service deployment

> #### workflow

- create
```sh
  $ kubectl create -f <yaml/workflow/deployment.yaml>
```

- [If you don't use 'defaultdb' for MySQL or use kafka, Go to](https://github.com/NexClipper/NexClipper/blob/dev/docs/option/workflow.md)

> #### collector

- create
```sh
  $ kubectl create -f <yaml/collector/deployment.yaml>
  $ kubectl create -f <yaml/collector/service.yaml>
```

- [If you don't use 'defaultdb' for MySQL or use kafka, Go to](https://github.com/NexClipper/NexClipper/blob/dev/docs/option/collector.md)


> #### nexservice

- create
```sh
  $ kubectl create -f <yaml/nexservice/deployment.yaml>
  $ kubectl create -f <yaml/nexservice/service.yaml>
```

- [If you want to use your own database insted of 'detaultdb', Go to](https://github.com/NexClipper/NexClipper/blob/dev/docs/option/nexservice.md)


### NexClipper Agent daemonset/deployment

- Deploy NexClipepr Agent on Kubernetes cluster as follows
  - agent deployed by daemonset: get host and docker container's information
  - agent deployed by deployment: get Kubernetes cluster's information

- create
```sh
  $ kubectl create -f <yaml/nexclipper-agent/nexclipper-agent.yaml>
```


### Now you can access web UI
```
  https://<k8s master ip>:32200
```

### For API Swasgger UI
```
  https://<k8s master ip>:32200/swagger-ui.html 
```

## Licensing

NexClipper is licensed under the Apache License, Version 2.0. See [LICENSE](https://github.com/NexClipper/NexClipper/blob/master/LICENSE) for the full license text.

## Contact

Email: nexclipper@nexclipper.io

Homepage: https://www.nexclipper.io/

Facebook : https://www.facebook.com/nexclipper/

Linkedin: https://www.linkedin.com/company/nexcloud/

Twitter: https://twitter.com/NexClipper

