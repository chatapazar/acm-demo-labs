# Disaster Recovery 

In this use case we are going to define the deployment of our application as follows:

1. We always want a single replica of our application running across a set of clusters
2. If the cluster hosting that single replica goes down, another one should deploy the application

For this use case we're going to create a new `PlacementRule` that matches the clusters labeled as `finance: dev`, we will add this new label to clusters named `cckcluster01` and `cckcluster02`. This new `PlacementRule` will include only one of the clusters
since we defined `clusterReplicas: 1` within the `PlacementRule`.

1. To avoid app creation collisions we are going to delete previous subscriptions and applications

    ~~~sh
    oc --context hubcluster delete -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/08_subscription-timewindow.yaml
    ~~~
2. List available clusters

    ~~~sh
    oc --context hubcluster get managedcluster
    ~~~

    ~~~sh
    NAME                    HUB ACCEPTED   MANAGED CLUSTER URLS   JOINED   AVAILABLE   AGE
    cckcluster01    true                                  True     True        1h6m
    cckcluster02   true                                  True     True        1h8m
    ~~~
3. Label the clusters

    > ![TIP](assets/tip-icon.png) **NOTE:** We are using the command line, but labeling can be done using the ACM WebUI as well
    ~~~sh
    # Patch development cluster
    oc --context hubcluster patch managedcluster cckcluster01 -p '{"metadata":{"labels":{"finance":"dev"}}}' --type=merge
    # Patch production cluster
    oc --context hubcluster patch managedcluster cckcluster02 -p '{"metadata":{"labels":{"finance":"dev"}}}' --type=merge
    ~~~
4. Create the new `PlacementRule`

    ~~~sh
    oc --context hubcluster create -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/09_placement_rule-finance.yaml
    ~~~
5. Before creating the `Subscription` let's check which cluster is matching the `PlacementRule` we just created

    ~~~sh
    oc --context hubcluster -n gitops-apps get placementrule finance-dev-clusters -o jsonpath='{.status.decisions[]}'
    ~~~

    ~~~sh
    map[clusterName:cckcluster01 clusterNamespace:cckcluster01]
    ~~~
    > ![WARNING](assets/warning-icon.png) **NOTE:** The application will be deployed to `cckcluster01` cluster only based on the output above
6. Create the `Subscription`

    ~~~sh
    oc --context hubcluster create -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/10_subscription-finance.yaml
    ~~~

Based on the `PlacementRule` decission, the application should be running on the `cckcluster01` cluster and not in `cckcluster02` cluster:

> ![TIP](assets/tip-icon.png) **NOTE:** We're using `oc` tool in order to verify the app deployment. Feel free to review the application on the ACM Console as well.

~~~sh
# Review app on development cluster
oc --context cluster1 -n gitops-apps get pods,svc,route
~~~

~~~sh
NAME                                READY   STATUS    RESTARTS   AGE
pod/reverse-words-7dd94446c-5lw6n   1/1     Running   0          2m53s

NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/reverse-words   ClusterIP   172.30.252.209   <none>        8080/TCP   2m53s

NAME                                     HOST/PORT                                                         PATH   SERVICES        PORT   TERMINATION   WILDCARD
route.route.openshift.io/reverse-words   reverse-words-gitops-apps.apps.cluster-6e02.red.osp.opentlc.com          reverse-words   8080                 None
~~~

~~~sh
# Review app on production cluster
oc --context cluster2 -n gitops-apps get pods,svc,route
~~~

~~~sh
No resources found in gitops-apps namespace.
~~~

Now we are going to simulate that we lose one of the `finance: dev` clusters, in order to do so, we are going to remove the `finance: dev` label from cluster named `cckcluster01`, that way the application should be deployed onto cluster named `cckcluster02`.

1. Remove `finance: dev` label from cluster named `cckcluster01`:

    ~~~sh
    oc --context hubcluster patch managedcluster cckcluster01 -p '{"metadata":{"labels":{"finance":null}}}' --type=merge
    ~~~
2. If we look now at the `PlacementRule` matches:

    ~~~sh
    oc --context hubcluster -n gitops-apps get placementrule finance-dev-clusters -o jsonpath='{.status.decisions[]}'
    ~~~

    ~~~sh
    map[clusterName:cckcluster02 clusterNamespace:cckcluster02]
    ~~~

The application should be moved to cluster named `cckcluster02` and removed from cluster named `cckcluster01`.

> ![TIP](assets/tip-icon.png) **NOTE:** We're using `oc` tool in order to verify the app deployment. Feel free to review the application on the ACM Console as well.

~~~sh
# Review app on development cluster
oc --context cluster1 -n gitops-apps get pods,svc,route
~~~

~~~sh
No resources found in gitops-apps namespace.
~~~

~~~sh
# Review app on production cluster
oc --context cluster2 -n gitops-apps get pods,svc,route
~~~

~~~sh
NAME                                READY   STATUS    RESTARTS   AGE
pod/reverse-words-7dd94446c-v55dj   1/1     Running   0          5m17s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/reverse-words   ClusterIP   172.30.23.99   <none>        8080/TCP   5m17s

NAME                                     HOST/PORT                                                         PATH   SERVICES        PORT   TERMINATION   WILDCARD
route.route.openshift.io/reverse-words   reverse-words-gitops-apps.apps.cluster-8aca.red.osp.opentlc.com          reverse-words   8080                 None
~~~

---

**Continue to [Infrastructure as Code](./06_infrastructure_as_code.md)**

**Back to [Using TimeWindows](./04_using_timewindows.md)**

**Go [Home](./README.md)**