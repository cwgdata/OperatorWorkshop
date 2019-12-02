# Confluent Operator Workshop Instructions

## Initial Setup

### ssh to server 
<TBD>

### Set KubeConfig from eks cluster

`aws eks --region us-west-2 update-kubeconfig --name se-workshop`

## Test Config

`kubectl get nodes`

## Change to helm directory after cloning

`cd k8workshop/helm`

## Run Script to build your yaml

`chmod 777 build_yaml.sh`

## Substitute your first initial last name whever you see REPLACE THIS below, ex: cgilmore
NOTE: build_yaml will update your readme in k8workshop directory if you want to copy paste from there after this command

`./build_yaml.sh REPLACE_THIS`

## Deploy Zookeeper

`helm install -f ./providers/REPLACE_THIS.yaml --name zookeeper-REPLACE_THIS  --namespace REPLACE_THIS --set zookeeper.enabled=true ./confluent-operator`

## Check deployment
`kubectl -n REPLACE_THIS get pods -o wide`

## Deploy Kafka

`helm install -f ./providers/REPLACE_THIS.yaml --name kafka-REPLACE_THIS  --namespace REPLACE_THIS --set kafka.enabled=true ./confluent-operator`

## Look at Services
`kubectl -n REPLACE_THIS get services -o wide`

## Deploy Connect

`helm install -f ./providers/REPLACE_THIS.yaml --name connect-REPLACE_THIS  --namespace REPLACE_THIS --set connect.enabled=true ./confluent-operator`

## Deploy Schema Registry

`helm install -f ./providers/REPLACE_THIS.yaml --name schemaregistry-REPLACE_THIS  --namespace REPLACE_THIS --set schemaregistry.enabled=true ./confluent-operator`

## Deploy KSQL

`helm install -f ./providers/REPLACE_THIS.yaml --name ksql-REPLACE_THIS  --namespace REPLACE_THIS --set ksql.enabled=true ./confluent-operator`

## Deploy C3

`helm install -f ./providers/REPLACE_THIS.yaml --name controlcenter-REPLACE_THIS  --namespace REPLACE_THIS --set controlcenter.enabled=true ./confluent-operator`

## Scale Kafka

`kubectl edit kafka kafka-REPLACE_THIS -n REPLACE_THIS`

Change the replicas value to 4 and save!

## Look at new node!

`kubectl -n REPLACE_THIS get pods -o wide | grep kafka`

## Clean up!

```
helm delete --purge controlcenter-REPLACE_THIS
helm delete --purge ksql-REPLACE_THIS
helm delete --purge schemaregistry-REPLACE_THIS
helm delete --purge connect-REPLACE_THIS
helm delete --purge kafka-REPLACE_THIS
helm delete --purge zookeeper-REPLACE_THIS
```
