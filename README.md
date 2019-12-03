# Confluent Operator Workshop Instructions


## Initial Setup


Substitute your first initial + last name whever you see REPLACE THIS below, ex: cgilmore


## SSH to Workshop Server 

(for MacOS / Linux users)

`ssh REPLACE_THIS@35.209.155.254`

(for Windows users)

`putty REPLACE_THIS@35.209.155.254`

(for users who can't ssh on port 22, you can use the following in a web browser)

http://35.209.155.254:2222/ssh/host/jumphost-operator-workshop-central

You will be prompted for a password, which is `operat0r`


## Test Kubernetes Config

`kubectl get nodes -o wide`

## Change to helm directory

`cd operator/helm`

## View YAML and walk through all the options

`vi providers/workshop.yaml`

## Deploy Zookeeper


`helm install -f ./providers/workshop.yaml --name zookeeper-REPLACE_THIS  --namespace REPLACE_THIS --set zookeeper.enabled=true ./confluent-operator`


## Check deployment


`kubectl -n REPLACE_THIS get pods -o wide`


## Deploy Kafka


`helm install -f ./providers/workshop.yaml --name kafka-REPLACE_THIS  --namespace REPLACE_THIS --set kafka.enabled=true ./confluent-operator`


## List the Network Services we created


`kubectl -n REPLACE_THIS get services -o wide`


## Look at Persistant Volumes we created


`kubectl -n REPLACE_THIS get pvc -o wide`

## Look at Kafka cluster details


`kubectl -n REPLACETHIS describe kafka`

## Look at Kafka node logs

`kubectl -n REPLACE_THIS logs kafka-REPLACE_THIS-0`


## Deploy Connect


`helm install -f ./providers/workshop.yaml --name connect-REPLACE_THIS  --namespace REPLACE_THIS --set connect.enabled=true ./confluent-operator`


## Deploy Schema Registry


`helm install -f ./providers/workshop.yaml --name schemaregistry-REPLACE_THIS  --namespace REPLACE_THIS --set schemaregistry.enabled=true ./confluent-operator`


## Deploy KSQL


`helm install -f ./providers/workshop.yaml --name ksql-REPLACE_THIS  --namespace REPLACE_THIS --set ksql.enabled=true ./confluent-operator`


## Deploy C3


`helm install -f ./providers/REPLACE_THIS.yaml --name controlcenter-REPLACE_THIS  --namespace REPLACE_THIS --set controlcenter.enabled=true ./confluent-operator`


## Navigate browser to c3


### get IP for c3 endpoint

Grab the 2nd IP from the output of the following command

`kubectl -n REPLACE_THIS get svc | grep controlcenter | grep lb`

Navigate to below and use the login admin with password Developer1

`http://<IP FROM ABOVE>:9021`


## Scale Kafka

`helm upgrade -f ./providers/workshop.yaml --set kafka.enabled=true --set kafka.replicas=2 kafka-REPLACE_THIS ./confluent-operator`

## Look at new node!

`kubectl -n REPLACE_THIS get pods -o wide | grep kafka`

## Upgrade Kafka

`helm upgrade -f ./providers/workshop.yaml --set kafka.enabled=true --set kafka.replicas=2 --set kafka.image.tag=5.3.1.0 kafka-REPLACE_THIS ./confluent-operator`

## Watch the nodes rolling restart!

`watch -n 5 kubectl -n REPLACE_THIS get pods -o wide`

## Run a Producer

### Get Kafka bootstrap LB IP (2nd IP in the below

`kubectl -n REPLACE_THIS get svc | grep kafka | grep bootstrap-lb`


### Create kafka.properties file in your directory and substitute the IP above

```
bootstrap.servers=<IP ADDRESS FROM ABOVE>:9092
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="test"
 password="test123";
sasl.mechanism=PLAIN
```

### Run a producer

`seq 10000 | kafka-console-producer --topic example --broker-list <IP ADDRESS FROM ABOVE>:9092 --producer.config ka
fka.properties`
  
### Run a consumer

`kafka-console-consumer --from-beginning --topic example --bootstrap-server <IP ADDRESS FROM ABOVE>:9092 --consume
r.config kafka.properties`




## Clean up!

```
helm delete --purge controlcenter-REPLACE_THIS
helm delete --purge ksql-REPLACE_THIS
helm delete --purge schemaregistry-REPLACE_THIS
helm delete --purge connect-REPLACE_THIS
helm delete --purge kafka-REPLACE_THIS
helm delete --purge zookeeper-REPLACE_THIS
```
