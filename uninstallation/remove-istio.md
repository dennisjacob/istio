### Remove Istio

```
istioctl x uninstall --purge

kubectl delete ns/istio-system

# Alternate option
istioctl manifest generate --set profile=demo | kubectl delete -f -

```
