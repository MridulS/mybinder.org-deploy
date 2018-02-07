# Command snippets used during operations

This is a collection of frequently and infrequently used command-line snippets
that are useful for operating and investigating what is happening on the
cluster. Think of it as a mybinder.org specific extension of the [kubernetes
cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


## List all pods older than X

To get a list of all pods older than four hours:
```
kubectl get pod --namespace=prod | grep '^jupyter-' | grep '\(\([12][0-9]\|[4-9]\)h\|d\)$'
```

or all those that have existed for more than 24hours:
```
kubectl get pod --namespace=prod | grep '^jupyter-' | grep 'd$' | awk '{print $1}')
```


## Remove a node from the cluster

First cordon off the node with `kubectl cordon <nodename>`.
Wait for there to be no `jupyter-*` pods on the node. You can check this with
`kubectl get pods --namespace=prod -o wide | grep "<nodename>$" | grep "^jupyter-"`.
Then drain the node `kubectl drain <nodename>`. The kubectl drain command will
most likely give you an error about certain pods running on the node that
prevent it from draining the node. It is Ok (and expected) to use the suggested
flags to force the draining: `kubectl drain <nodename> --ignore-daemonsets --force --delete-local-data`. The autoscaler should now remove the  node for you.
This can take 10-15minutes.