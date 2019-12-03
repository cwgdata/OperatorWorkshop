# Confluent Operator Workshop Instructions


## Initial Setup


Substitute your first initial + last name whever you see $NS below, ex: cgilmore
Alternatively, you can `export NS=cgilmore` in your shell first and it will substitute leveraging a shell variable.


## SSH to Workshop Server 

(for MacOS / Linux users)

`ssh $NS@35.209.155.254`

(for Windows users - you'll have to replace $NS with %NS%)

`set NS=cgilmore`

`putty %NS%@35.209.155.254`

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


`helm install -f ./providers/workshop.yaml --name zookeeper-$NS  --namespace $NS --set zookeeper.enabled=true ./confluent-operator`


## Check deployment


`kubectl -n $NS get pods -o wide`


## Deploy Kafka


`helm install -f ./providers/workshop.yaml --name kafka-$NS  --namespace $NS --set kafka.enabled=true ./confluent-operator`


## List the Network Services we created


`kubectl -n $NS get services -o wide`


## Look at Persistant Volumes we created


`kubectl -n $NS get pvc -o wide`

## Look at Kafka cluster details


`kubectl -n $NS describe kafka`

## Look at Kafka node logs

`kubectl -n $NS logs kafka-$NS-0`

## Deploy Schema Registry


`helm install -f ./providers/workshop.yaml --name schemaregistry-$NS  --namespace $NS --set schemaregistry.enabled=true ./confluent-operator`

## Deploy Connect


`helm install -f ./providers/workshop.yaml --name connect-$NS  --namespace $NS --set connect.enabled=true ./confluent-operator`


## Deploy KSQL


`helm install -f ./providers/workshop.yaml --name ksql-$NS  --namespace $NS --set ksql.enabled=true ./confluent-operator`


## Deploy Confluent Control Center


`helm install -f ./providers/workshop.yaml --name controlcenter-$NS  --namespace $NS --set controlcenter.enabled=true ./confluent-operator`


## Navigate web browser to Confluent Control Center


### get IP for Confluent Control Center (C3) endpoint

Grab the 2nd IP from the output of the following command

`kubectl -n $NS get svc | grep controlcenter | grep lb`

Navigate to below and use the login username `admin` with password `Developer1`

`http://<IP FROM ABOVE>:9021`


## Scale Kafka

`helm upgrade -f ./providers/workshop.yaml --set kafka.enabled=true --set kafka.replicas=2 kafka-$NS ./confluent-operator`

## Look at new node!

`kubectl -n $NS get pods -o wide | grep kafka`

## Upgrade Kafka

`helm upgrade -f ./providers/workshop.yaml --set kafka.enabled=true --set kafka.replicas=2 --set kafka.image.tag=5.3.1.0 kafka-$NS ./confluent-operator`

## Watch the nodes rolling restart!

`watch -n 5 kubectl -n $NS get pods -o wide`

## Run a Producer

### Get Kafka bootstrap LB IP (2nd IP in the below

`kubectl -n $NS get svc | grep kafka | grep bootstrap-lb`


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




## Clean up (please)!

```
helm delete --purge controlcenter-$NS
helm delete --purge ksql-$NS
helm delete --purge connect-$NS
helm delete --purge schemaregistry-$NS
helm delete --purge kafka-$NS
helm delete --purge zookeeper-$NS
```
