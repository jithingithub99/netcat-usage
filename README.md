### File copy from pod to pod using netcat ####


We wanted to copy 20GB prometheus data file ( **prometheus-bkp.21122021.tar.gz**)from a pod (**prometheus-0**; namespace is monitoring) to  another pod in customer environment
we were getting "unexpected eof" error while copying 20gb file from pod to pod.This will copy some amount of mb file and stopped with error
So as a solution we tried using netcat utility and it was successfull.

First we have a created pod (**prometheus-bkp-pod**) and a pvc (**prometheus-bkp-pvc**) (50gb) attached to this pod
log in to  prometheus-bkp-pod and verify the 30gb pvc volume is mounted

`` kex sh ``

Select the  pod and change to volume mounted directory 

`` cd /prometheus-bkp ``

Install netcat in prometheus-bkp-pod & prometheus-0 

`` apt get update ``

`` apt install netcat ``

Make ready the pod prometheus-0 to send the data to newly created pod

On data receiving pod (prometheus-bkp-pod) execute below command


 `` nc -l -p 1234 > prometheus-bkp.21122021.tar.gz ``
 
 where 1234 is the network port 

On sending pod (prometheus-0) execute below commands

`` nc -w 3 10.244.10.153  1234 < prometheus-bkp.21122021.tar.gz `` 

 (Where 10.244.10.153 is the IP address of newly created pod (prometheus-bkp-pod)
 
 We can get the ip using  `` k get pod prometheus-bkp-pod -o wide -n monitoring ``
 
Verify data has been copied from kubernetes pod to destination pod


