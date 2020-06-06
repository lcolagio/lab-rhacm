# Deploying Applications to Multiple Clusters

## **Sending an Application to the Dev Clusters**

In this use case we are going to deploy a sample application `mssql-example-app` to our `development` clusters, that means that we're going to target clusters labeled as `env: dev`.

> **NOTE**: Below commands should be run in HUB Cluster (where ACM is running)

1. Create the different ACM components we will be using in this example: `Channel`, `PlacementRule`, `Application` and `Subscription`:

    ~~~sh
    # add use to scc mssql-persistent-scc
    oc adm policy add-scc-to-user mssql-persistent-scc -z mssql-persistent-sa -n usecase2-ap
    ~~~
 
    ~~~sh
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase2/rhacm/channel.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase2/rhacm/application.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase2/rhacm/subscription.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/temp/usecase2/rhacm/placementrule.yaml
    ~~~

2.  Check ACM components:

    created project

    ~~~sh
    oc get project | grep usecase2
    ~~~

    created ACM Components

    ~~~sh
    oc get applications -A | grep usecase2
    oc get channel -A | grep usecase2
    oc get subscription -A | grep usecase2
    oc get placementrule -A | grep usecase2

    oc describe subscription usecase2-subscription1 -n usecase2-app
    ~~~


    ~~~sh
    oc get deployable -A | grep usecase2
    ~~~
    output: 
    ~~~
    ocp3                      usecase2-subscription1-deployable-mbb6s                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    ocp4                      usecase2-subscription1-deployable-7xbqw                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    usecase2-app              usecase2-subscription1-deployable                                               Subscription    apps.open-cluster-management.io/v1   83s    Propagated
    usecase2-app              usecase2-subscription1-usecase2-application-prometheus-example-app-deployment   Deployment      apps/v1                              83s
    usecase2-app              usecase2-subscription1-usecase2-application-usecase2-ns-namespace               Namespace       v1                                   83s
    ~~~

3. How to edit an component ACM

    ~~~sh
    # Edit subscription
    oc edit subscription usecase2-subscription1 -n usecase2-app
    ~~~

    ~~~sh
    # Edit Placementrule to change cluster label selector from dev to qua by example
    oc edit placementrule usecase2-placementrule -n usecase2-app
    ~~~


4. How to Delete all created components for the sample application

    ~~~sh
    # on cluster Hub
    oc delete subscription usecase2-subscription1 -n usecase2-app
    oc delete placementrule usecase2-placementrule -n usecase2-app
    oc delete applications usecase2-appl -n usecase2-app
    oc delete channel usecase2-channel -n usecase2-channel
    
    oc delete project usecase2-channel
    oc delete project usecase2-app

    # on managed cluster
    oc delete project usecase2-app

    
    ~~~

