# Deploying Applications to Multiple Clusters

## **Sending an Application to the Dev Clusters**

In this use case we are going to deploy a sample application `prometheus-example-app` to our `development` clusters, that means that we're going to target clusters labeled as `env: dev`.

> **NOTE**: Below commands should be run in HUB Cluster (where ACM is running)

1. Create the different ACM components we will be using in this example: `Channel`, `PlacementRule`, `Application` and `Subscription`:

    ~~~sh
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/channel.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/application.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/subscription.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/placementrule.yaml
    ~~~

   ~~~sh
    # override
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/channel.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/application.yaml
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/subscription-override.yaml  
    oc create -f https://raw.githubusercontent.com/lcolagio/lab-rhacm/master/usecase1/rhacm/placementrule.yaml
    ~~~

2.  Check ACM components:

    created project

    ~~~sh
    oc get project | grep usecase1
    ~~~

    created ACM Components

    ~~~sh
    oc get applications -A | grep usecase1
    oc get channel -A | grep usecase1
    oc get subscription -A | grep usecase1
    oc get placementrule -A | grep usecase1

    oc describe subscription usecase1-subscription1 -n usecase1-app
    ~~~


    ~~~sh
    oc get deployable -A | grep usecase1
    ~~~
    output: 
    ~~~
    ocp3                      usecase1-subscription1-deployable-mbb6s                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    ocp4                      usecase1-subscription1-deployable-7xbqw                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    usecase1-app              usecase1-subscription1-deployable                                               Subscription    apps.open-cluster-management.io/v1   83s    Propagated
    usecase1-app              usecase1-subscription1-usecase1-application-prometheus-example-app-deployment   Deployment      apps/v1                              83s
    usecase1-app              usecase1-subscription1-usecase1-application-usecase1-ns-namespace               Namespace       v1                                   83s
    ~~~

3. How to edit an component ACM

    ~~~sh
    # Edit subscription
    oc edit subscription usecase1-subscription1 -n usecase1-app
    ~~~

    ~~~sh
    # Edit Placementrule to change cluster label selector from dev to qua by example
    oc edit placementrule usecase1-placementrule -n usecase1-app
    ~~~


4. How to Delete all created components for the sample application

    ~~~sh
    # on cluster Hub
    oc delete subscription usecase1-subscription1 -n usecase1-app
    oc delete placementrule usecase1-placement -n usecase1-app
    oc delete applications usecase1-applications -n usecase1-app
    oc delete channel usecase1-channel -n usecase1-channel
    oc delete project usecase1-channel
    oc delete project usecase1-app

    # on managed cluster
    oc delete project usecase1-ns
    oc delete project usecase1-app

    
    ~~~

