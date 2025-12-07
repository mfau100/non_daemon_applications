#### To clear all the completed Pods, you must delete the Jobs that created them.
#### In Kubernetes/OpenShift, Pods created by a Job do NOT get deleted automatically unless you #### configure cleanup policies.
#### 

1. Delete Jobs → completed Pods disappear

#### 
#### Your completed Pods belong to Jobs created by the CronJob and by the one-shot Job. 
#### You can delete all Jobs in the namespace:
#### 
```
oc delete jobs --all
```
#### 
#### 
#### This will automatically remove:
#### 
- all Completed Pods
- all Failed Pods
- all Job history
#### 
#### Then check:
#### 
```
oc get pods
```
#### 
#### 
#### Your Completed pods should be gone.
#### 
#### 🛠️ 2. If you want to stop the CronJob from creating new ones
#### 
#### Suspend the CronJob:
#### 
```
oc patch cronjob busybox-cron -p '{"spec": {"suspend": true}}'
```
#### 
#### Or delete the CronJob entirely:
####
``` 
oc delete cronjob busybox-cron
```
#### 
#### 🧹 3. Configure automatic cleanup (optional)
#### 
#### You can tell a CronJob to keep only the last X Jobs:
#### 
```
successfulJobsHistoryLimit: 1
failedJobsHistoryLimit: 1
``` 
#### 
#### Example patch:
#### 
```
oc patch cronjob busybox-cron -p '{"spec": {"successfulJobsHistoryLimit": 1, ### "fa#iledJobsHistoryLimit": 1}}'
```
#### 
#### 🧹 4. Manually delete completed pods only
#### 
#### If you want to remove only pods with status Completed:
####
``` 
oc delete pod --field-selector=status.phase=Succeeded
``` 
#### 
#### Or for both Succeeded + Failed:
#### 
#### oc delete pod --field-selector=status.phase!=Running
#### 
#### ✔️ Summary
#### Goal	Command
#### Delete all Jobs + Pods they created	oc delete jobs --all
#### Delete only Completed Pods	oc delete pod --field-selector=status.phase=Succeeded
#### Prevent CronJob from creating more	oc patch cronjob ...
#### Auto-cleanup history	Set successfulJobsHistoryLimit
#### 
#### If you want, I can help you modify your CronJob YAML to auto-prune old Jobs so this cleanup #### happens automatically.