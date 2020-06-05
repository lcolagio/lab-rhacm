# Deploying Applications to Multiple Clusters

## **Sending an Application to the Dev Clusters**

In this use case we are going to deploy a sample application to our `development` clusters, that means that we're going to target clusters labeled as `env: dev`.

Let's explore the different ACM components we will be using in this example: `Channel`, `PlacementRule`, `Application` and `Subscription`:

> **NOTE**: Below commands should be run in HUB Cluster (where ACM is running)

1. Create a `Namespace` where we will store the ACM manifests for the application

    ~~~sh
    oc create -f https://github.com/lcolagio/lab-rhacm.git/master/usecase1/rhacm/channel.yaml
    oc create -f https://github.com/lcolagio/lab-rhacm.git/master/usecase1/rhacm/application.yaml
    oc create -f https://github.com/lcolagio/lab-rhacm.git/master/usecase1/rhacm/subscription.yaml
    oc create -f https://github.com/lcolagio/lab-rhacm.git/master/usecase1/rhacm/placementrule.yaml
    ~~~


2. Check object for the application managed with ACM

    ~~~sh
    oc get project | grep usecase1
    oc get applications -A | grep usecase1
    oc get channel -A | grep usecase1
    oc get subscription -A | grep usecase1
    oc get placementrule -A | grep usecase1
    oc describe subscription usecase1-subscription1 -n usecase1-app
    ~~~

    ~~~sh
    oc get deployable -A | grep usecase1

    ocp3                      usecase1-subscription1-deployable-mbb6s                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    ocp4                      usecase1-subscription1-deployable-7xbqw                                         Subscription    apps.open-cluster-management.io/v1   83s    Deployed
    usecase1-app              usecase1-subscription1-deployable                                               Subscription    apps.open-cluster-management.io/v1   83s    Propagated
    usecase1-app              usecase1-subscription1-usecase1-application-prometheus-example-app-deployment   Deployment      apps/v1                              83s
    usecase1-app              usecase1-subscription1-usecase1-application-usecase1-ns-namespace               Namespace       v1                                   83s
    ~~~

3. Delete oject managed with ACM

    ~~~sh
    # cluster Hub
    oc delete subscription usecase1-subscription1 -n usecase1-app
    oc delete placementrule usecase1-placement -n usecase1-app
    oc delete applications usecase1-applications -n usecase1-app
    oc delete channel usecase1-channel -n usecase1-channel
    oc delete project usecase1-channel
    oc delete project usecase1-app

    # managed server
    oc delete project usecase1-ns
    oc delete project usecase1-app

    
    
    ~~~

4. Edit oject managed with ACM

    ~~~sh
    oc edit subscription usecase1-subscription1 -n usecase1-app
    ~~~# lab-rhacm
