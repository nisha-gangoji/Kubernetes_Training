# ASSIGNMENT 4 : VOTING APP

# My observations:
1. What happened after deleting voting pod?
  * Command used : `kubectl delete po vote-94849dc97-5w9wp`
  * The voting application is working fine
  * A new pod gets created with a new pod_hash/NAME :
     Earlier it was "vote-94849dc97-5w9wp" and now it is "vote-94849dc97-qfknr"
     
2. What happened after deleting worker pod?
  * Command used : `kubectl delete po worker-dd46d7584-8dk8t`
  * The voting application is working fine.
  * A new pod gets created with a new pod_hash/NAME :
      Earlier it was "worker-dd46d7584-8dk8t" and now it is "worker-dd46d7584-2jx5h"
  

3. What happened after db pod deletion?
  * Command used : `kubectl delete po db-b54cd94f4-bclmv`
  * The voting application stopped working after deleting the db pod. We can give the votes but the results does not gets updated.
  * The stats got reseted i.e voting for dogs and cats became equal to 50% each.
  * db pod is still running with a new hash.
  * Worker pod got restarted
  * Snapshot of the same:
    ```
    [root@ip-172-31-3-62 k8s-specifications]# kubectl delete po db-b54cd94f4-bclmv
    pod "db-b54cd94f4-bclmv" deleted
    [root@ip-172-31-3-62 k8s-specifications]# kubectl get po
    NAME                      READY   STATUS    RESTARTS   AGE
    db-b54cd94f4-xfh65        1/1     Running   0          2m30s
    redis-868d64d78-466g8     1/1     Running   0          66m
    result-5d57b59f4b-6s9wx   1/1     Running   0          66m
    vote-94849dc97-qfknr      1/1     Running   0          53m
    worker-dd46d7584-2jx5h    1/1     Running   1          53m
    ```
  * Deleted the worker pod which had got restarted and checked if the results are getting updated or not:
    * Command used: `kubectl delete po worker-dd46d7584-2jx5h`
    * No change (Still the result page of voting didn't get updated)
      
# Why result app STOPPED working after deleting the db pod?
The results app stopped getting updated or stopped working because the connection between the result pod and the db pod was stopped.

The following log shows how the connection got lost after deleting the db pod
```
[root@ip-172-31-3-62 k8s-specifications]# kubectl logs -f result-5d57b59f4b-swms5
Fri, 26 Aug 2022 13:44:35 GMT body-parser deprecated bodyParser: use individual json/urlencoded middlewares at server.js:67:9
Fri, 26 Aug 2022 13:44:35 GMT body-parser deprecated undefined extended: provide extended option at ../node_modules/body-parser/index.js:105:29
App running on port 80
Connected to db
Error performing query: Error: This socket has been ended by the other party
```
# How to make the RESULT POD work again?
**The connection got established back, after deleting the results pod.**
* Command used : `kubectl delete po result-5d57b59f4b-6s9wx`
* Snapshot of the same:
```
[root@ip-172-31-3-62 k8s-specifications]# kubectl delete po result-5d57b59f4b-zxb9c
pod "result-5d57b59f4b-zxb9c" deleted
[root@ip-172-31-3-62 k8s-specifications]# kubectl get po
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-jvs67        1/1     Running   0          7m33s
redis-868d64d78-466g8     1/1     Running   0          103m
result-5d57b59f4b-swms5   1/1     Running   0          68s
vote-94849dc97-qfknr      1/1     Running   0          90m
worker-dd46d7584-5wd4s    1/1     Running   1          21m
```

# Some Jargons learnt so far:
* Docker :
  * Isolation and Abstraction
  * Docker Daemon -> Docker Engine
  * Creation of containers and docker images
* Kuberentes :
  *  Cluster
  *  Node
  *  Pods and Labels
    * Creation and deletion of pods
  *  Containers
  *  Namespace
  *  Controllers
      - Replication Controller (RC)
      - Replica Set (RS)
      - Job -> Batch Job & Cron Job
      - Daemon Set (DS)
  * Controller manager
  * Replication Manager
  * KUBIA -> KUBernetes In Action by Luksa
  * Service
  * NodePort
  * Endpoints
  * Load Balance
  * Deployment
  * Rolling Update
