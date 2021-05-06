prepare to application-portablility, lab 7

lab 8 - application portability

for cluster in cluster1 cluster2 cluster3;do echo "*** $cluster ***"; oc get deployment --context $cluster -n pacman;done

change deployment with [kustomize](https://kustomize.io/)
review git, base, overlay, with kustomize
scale to 0, update git
acm auto update

# Browse to the directory 
cd lab-7-assets
# Modify the replica count in cluster1
sed -i.bu 's/replicas: [0-9]/replicas: 0/g' overlays/cluster1/pacman-deployment.yaml
# Stage your changes to be sent to the git repository
git add *
git commit -am 'Cluster1 replicas scaled to 0'
# Push your commits to the git repository
git push origin master

view deployment with command, view

for cluster in cluster1 cluster2 cluster3;do echo "*** $cluster ***"; oc get deployment --context $cluster -n pacman;done

show on acm ui

change back
sed -i.bu 's/replicas: [0-9]/replicas: 1/g' overlays/cluster1/pacman-deployment.yaml

git add *
git commit -am 'Scale back cluster1 and cluster2 to 1'
# Push your commits to the git repository
git push origin master

for cluster in cluster1 cluster2 cluster3;do echo "*** $cluster ***"; oc get deployment --context $cluster -n pacman;done

canary deployment with lab9

Introduction to GitOps and Policies with ACM

add environment=dev --> cluster01, environment=prod --> cluster02
lab 3
review namespace, channel, placementrule, application, subscription
app to dev
app to prod
review to allcluster

oc --context hubcluster delete -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/03_subscription-dev.yaml

oc --context hubcluster delete -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/05_subscription-prod.yaml

review timewindows feature lab 4

lab 5 dr, cluster replicas = 1
lab 9 show another way