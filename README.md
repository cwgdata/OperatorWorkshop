# Confluent Operator Workshop Instructions

## Initial Setup

### Substitute your first initial + last name whever you see REPLACE THIS below, ex: cgilmore

## ssh to server 
(for MacOS / Linux users)

`ssh REPLACE_THIS@35.209.155.2542`

(for Windows users)

`putty REPLACE_THIS@35.209.155.254`

(for users who can't ssh on port 22)

`http://35.209.155.254:2222/ssh/host/jumphost-operator-workshop-central`

You will be prompted for a password, which is operat0r

## Test Kubernetes Config

`kubectl get nodes -o wide`

## Change to helm directory after cloning

`cd operator/helm`

## View YAML and walk through all the option

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

## Deploy Connect

`helm install -f ./providers/workshop.yaml --name connect-REPLACE_THIS  --namespace REPLACE_THIS --set connect.enabled=true ./confluent-operator`

## Deploy Schema Registry

`helm install -f ./providers/workshop.yaml --name schemaregistry-REPLACE_THIS  --namespace REPLACE_THIS --set schemaregistry.enabled=true ./confluent-operator`

## Deploy KSQL

`helm install -f ./providers/workshop.yaml --name ksql-REPLACE_THIS  --namespace REPLACE_THIS --set ksql.enabled=true ./confluent-operator`

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
