# Step Demo ACM

## Step 0
- show ACM Grafana
- deploy sample app 
  - https://docs.google.com/document/d/1-Vkq3ADtTd65pYzWWn2IoK4a-rsjdHCnJLRoe8WG6Eg/edit# page 9

## Step 1
- prepare to application-portablility, lab 7
- start lab 8 - application portability
- show cluster
    ~~~sh
    $ for cluster in cluster1 cluster2 cluster3;do echo "*** $cluster ***"; oc get deployment --context $cluster -n pacman;done
    ~~~   

- review deployment with [kustomize](https://kustomize.io/)
- review git, base, overlay, with kustomize
- pathc scale to 0, update git (commit, push)
- show acm auto update

    ~~~sh
    # Browse to the directory 
    $ cd lab-7-assets
    # Modify the replica count in cluster1
    $ sed -i.bu 's/replicas: [0-9]/replicas: 0/g' overlays/cluster1/pacman-deployment.yaml
    # Stage your changes to be sent to the git repository
    $ git add *
    $ git commit -am 'Cluster1 replicas scaled to 0'
    # Push your commits to the git repository
    $ git push origin master
    ~~~

- view deployment with command, view

    ~~~sh
    $ for cluster in cluster1 cluster2 cluster3;do echo "*** $cluster ***"; oc get deployment --context $cluster -n pacman;done
    ~~~

- show on acm ui
- change back to 1

    ~~~sh
    $ sed -i.bu 's/replicas: [0-9]/replicas: 1/g' overlays/cluster1/pacman-deployment.yaml
    $ git add *
    $ git commit -am 'Scale back cluster1 and cluster2 to 1'
    # Push your commits to the git repository
    $ git push origin master

    $ for cluster in cluster1 cluster2 cluster3;do echo "*** $cluster ***"; oc get deployment --context $cluster -n pacman;done
    ~~~

- canary deployment with lab9

## Introduction to GitOps and Policies with ACM

- add environment=dev --> cluster01, environment=prod --> cluster02 with ui/command
- lab 3
  - review namespace, channel, placementrule, application, subscription
  - deploy app to dev
  - deploy app to prod
  - review to allcluster
- show change with Replacement Rule
    ~~~sh
    $ oc --context hubcluster delete -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/03_subscription-dev.yaml

    $ oc --context hubcluster delete -f https://github.com/chatapazar/acm-app-lifecycle-policies-lab/raw/master/acm-manifests/reversewords-kustomize/05_subscription-prod.yaml
    ~~~

- review timewindows feature lab 4
- lab 5 diaster recovery, cluster replicas = 1
- lab 9 show another way