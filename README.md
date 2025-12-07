## In OpenShift (and Kubernetes in general), an application that runs once and exits is called a non-daemon, run-to-completion, or batch workload.

## These are workloads not intended to run forever (unlike Deployments or DaemonSets).
## To run a busybox container that starts, runs a shell command, and exits without OpenShift restarting it, you must use one of the non-daemon workload types:

1. Job (most common)

### A Job runs a Pod to completion.
### If the Pod exits successfully (exit code 0), it is not restarted.

### Example Job (busybox that exits immediately)

apiVersion: batch/v1
kind: Job
metadata:
  name: busybox-once
spec:
  template:
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ['sh', '-c', 'echo Hello ; sleep 5']
      restartPolicy: Never


### Apply:

oc apply -f busybox-job.yaml

## What not to use for non-daemon workloads
## ❌ Deployment

## A Deployment expects long-running Pods (e.g. services, webservers).
## If a container exits, even normally, the Deployment controller will restart it.

## ❌ StatefulSet or DaemonSet

## Also expect continuously running Pods.

## Those are daemon-style workloads.

## 🧠 Key Rule:
## ✔️ If you want something to run once and exit → use Job
## ✔️ If you want something to run on a schedule → use CronJob
## ❌ If you use Deployment → Pod exiting is treated as a failure → it is restarted
## 👍 Example: Run a BusyBox container that exits immediately

## Quick one-liner using a Job:

## oc run busybox-once --image=busybox --restart=Never --command -- sh -c "echo hello; sleep 2"
