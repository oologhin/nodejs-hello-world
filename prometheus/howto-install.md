#Install Prometeus

This is the source: https://learn.microsoft.com/en-us/azure/openshift/howto-deploy-prometheus

it creates 3 clusters: a prometheus and two app clusters to monitor
###Clean-up
oc delete project monitoring
oc delete  project prometheus-project
oc delete  project app-project1
oc delete  project app-project2

###create 3 new namespaces
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2


                /*
                https://api.odev1.sbmsdev.eulisa:6443

                Now using project "prometheus-project" on server "https://api.odev1.sbmsdev.eulisa:6443".

                You can add applications to this project with the 'new-app' command. For example, try:

                    oc new-app rails-postgresql-example

                to build a new example application in Ruby. Or use kubectl to deploy a simple Kubernetes application:

                    kubectl create deployment hello-node --image=k8s.gcr.io/serve_hostname
                */

### Create two new keys
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project

## Deploy Prometheus
### Make sure you download the same prometheus stahdalone file 
    oc version

    oc process -f prometheus-standalone.yaml | oc apply -f - -n prometheus-project

### To verify if the prom StatefulSet has equal DESIRED and CURRENT number replicas, run the 

    oc get statefulset -n prometheus-project 

### To check all resources in the project, run the 

    oc get all -n prometheus-project command.

## Add permissions to allow service discovery
apply template to all projects

oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project

**To verify that Role and RoleBinding were created correctly, run the oc get role and oc get rolebinding commands.

