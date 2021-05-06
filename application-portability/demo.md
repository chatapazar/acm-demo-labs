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