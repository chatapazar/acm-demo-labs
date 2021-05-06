# Show Migrate From Dev to Prod

Reuse reversewords-finance-app
add new replacement rule

~~~sh
oc --context hubcluster create -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/11_placement_rule-finace-prod.yaml
~~~

check PlacementRule
~~~sh
oc --context hubcluster -n gitops-apps get placementrule finance-prod-clusters -o jsonpath='{.status.decisions[]}'
~~~

patch new PlacementRule 

~~~sh
oc --context hubcluster -n gitops-apps patch subscription.apps.open-cluster-management.io/reversewords-finance-app-subscription -p '{"spec":{"placement":{"placementRef":{"name":"finance-prod-clusters"}}}}' --type=merge
~~~

check output

~~~sh
oc --context cluster1 -n gitops-apps get pods,svc,route

oc --context cluster2 -n gitops-apps get pods,svc,route
~~~sh