# Deploying Applications to Multiple Clusters

## **Sending an Application to the Dev Clusters**

In this use case we are going to deploy a sample application `mssql-example-app` to our `development` clusters, that means that we're going to target clusters labeled as `env: dev`.

> **NOTE**: Below commands should be run in HUB Cluster (where ACM is running)

1. Create the different ACM components we will be using in this example: `Channel`, `PlacementRule`, `Application` and `Subscription`:


    ~~~sh
    oc new-project usecase3-app
    oc new-project usecase3-channel
    
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase3/rhacm/channel.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase3/rhacm/application.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase3/rhacm/subscription.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase3/rhacm/placementrule.yaml
    
    # oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase3/rhacm/subscription-to-channel1.yaml
    
    ~~~

2.  Check ACM components:

    go to created project

    ~~~sh
    oc get project | grep usecase3
    ~~~

    created ACM Components

    ~~~sh
    oc get applications -n usecase3-app | grep usecase3
    oc get channel -n usecase3-channel | grep usecase3
    oc get subscription -n usecase3-app | grep usecase3
    oc get placementrule -n usecase3-app | grep usecase3

    oc describe subscription usecase3-subscription -n usecase3-app

    #    oc describe subscription usecase3-subscription1 -n usecase3-app
    ~~~


    ~~~sh
    oc get deployable -n usecase3-app | grep usecase3
    oc get deployable -n ocp3 | grep usecase3
    oc get deployable -n ocp4 | grep usecase3
    
    ~~~
    output: 
    ~~~
    ocp3                      usecase3-subscription1-deployable-mbb6s                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    ocp4                      usecase3-subscription1-deployable-7xbqw                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    usecase3-app              usecase3-subscription1-deployable                                               Subscription    apps.open-cluster-management.io/v1   83s    Propagated
    usecase3-app              usecase3-subscription1-usecase3-application-prometheus-example-app-deployment   Deployment      apps/v1                              83s
    usecase3-app              usecase3-subscription1-usecase3-application-usecase3-ns-namespace               Namespace       v1                                   83s
    ~~~

2.1 Check role amdin

   ~~~sh
   # cluster Hub
   oc get rolebinding  -n usecase3-app
   >
    NAME                    AGE
    admin                   6m52s
    system:deployers        6m52s
    system:image-builders   6m52s
    system:image-pullers    6m53s
    
    oc get rolebinding admin -o yaml | grep user1
    >
    name: user1

   # cluster Managed
   oc get rolebinding  -n usecase3-app
   >
    NAME                    AGE 
    system:deployers        6m52s
    system:image-builders   6m52s
    system:image-pullers    6m53s
   ~~~

3. How to edit an component ACM

    ~~~sh
    # Edit subscription
    oc edit subscription usecase3-subscription1 -n usecase3-app
    ~~~

    ~~~sh
    # Edit Placementrule to change cluster label selector from dev to qua by example
    oc edit placementrule usecase3-placementrule -n usecase3-app
    ~~~


4. How to Delete all created components for the sample application

    ~~~sh
    # on cluster Hub
    oc delete subscription usecase3-subscription1 -n usecase3-app
    oc delete placementrule usecase3-placementrule -n usecase3-app
    oc delete applications usecase3-app -n usecase3-app
    oc delete channel usecase3-channel -n usecase3-channel
    
    oc delete project usecase3-channel
    oc delete project usecase3-app

    # on managed cluster
    oc delete project usecase3-app

    
    ~~~

