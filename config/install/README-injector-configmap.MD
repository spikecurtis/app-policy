# How to inject additional sidecar containers and volumes with Istio
Istio: 0.5.1
[Istio Instructions](https://istio.io/docs/setup/kubernetes/sidecar-injection.html)

## Use the injector configmap
* To inject Dikastes side car in the application pods use `istio-dikastes-sidecar-injector-configmap-release.yaml` or
`istio-dikastes-sidecar-injector-configmap-debug.yaml`

## Manual Injection
* Extract the injection configuration
```
kubectl create -f config/install/istio-dikastes-sidecar-injector-configmap-release.yaml \
    --dry-run \
    -o=jsonpath='{.data.config}' > inject-config.yaml
```

* Use the updated inject-config.yaml to manually launch application
```
istioctl kube-inject \
    --injectConfigFile inject-config.yaml \
    --filename samples/sleep/sleep.yaml \
    --output sleep-injected.yaml
    
kubectl apply -f sleep-injected.yaml    
```

## Webhook Injection

Inject the configmap specified here
```
kubectl apply -f config/install/istio-dikastes-sidecar-injector-configmap-release.yaml
```

Then follow the instructions to setup the [webhook](https://istio.io/docs/setup/kubernetes/sidecar-injection.html#installing-the-webhook)

## Launch and verify that pod's are launched with additional containers
```
~/istio-0.5.1$ kubectl create -f samples/sleep/sleep.yaml
```

Confirm that the sleep pod is running with __3__ containers (Envoy sidecar, dikastes-sidecar and the sleep container)
```
$ kubectl get pods
NAME                     READY     STATUS    RESTARTS   AGE
nodeagent-fxlnb          1/1       Running   3          1h
sleep-776b7bcdcd-7nvp9   3/3       Running   0          7s
```