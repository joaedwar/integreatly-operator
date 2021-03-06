---
products:
  - name: rhoam
    environments:
      - osd-fresh-install
      - osd-post-upgrade
    targets:
      - 0.1.0
      - 0.2.0
      - 1.0.0
      - 1.5.0
estimate: 120m
tags:
  - destructive
---

# J03B - Verify that namespaces get recreated by the integreatly-operator if deleted

https://github.com/integr8ly/integreatly-operator/blob/master/test/common/namespace_restoration.go

## Description

Note: this test should only be performed at a time it will not affect other ongoing testing, or on a separate cluster.

Test that all namespace will be automatically recreated by the integreatly-operator

## Steps

1. Login via `oc` as **kubeadmin**

2. By default, this test is not run as part of the functional test suite. To run this singular functional test, run the following command from the RHOAM operator repo against a target cluster:

```
export DESTRUCTIVE=true; export WATCH_NAMESPACE=redhat-rhoam-operator;  go clean -testcache && go test -v ./test/functional -run="//^J03" -timeout=80m | tee test-results.log
```

**Note finalizers:**

If a namespace stuck in 'Terminating' state, it's needed to remove finalizers to proceed. To find resources with finalizers:

```
kubectl api-resources --verbs=list --namespaced -o name | xargs -n 1 kubectl get -n <namespace> --ignore-not-found --show-kind
```

Then, to edit resource: `oc edit <resource> -n <namespace>`

**Other useful commands here:**

to get stage what is reconciler currently on: `oc get rhmis rhoam -n redhat-rhoam-operator -o json | grep 'stage":'`

to get statuses (+ info) of all stages: `oc get rhmis rhoam -n redhat-rhoam-operator -o json | jq -r '.status.stages'`
