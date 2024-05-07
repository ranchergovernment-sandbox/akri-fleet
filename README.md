# Akri Fleet

Deploy Akri and expose devices from Akri.

## Use-Case (SDR)

For this specific use-case, you need to ensure the Fleet cluster that has the RTL SDR attached to it has 2 labels:

* `akri=enabled`
* `peripheral=sdr`

Once both of those are in place, Akri should deploy and you should have an Akri configuration for your SDR. You can verify with the following:

```bash
# Validate the configuration is in place.
[12:09:21] / $ kubectl get configurations.akri.sh -n akri
NAME            CAPACITY   AGE
akri-sd-radio   1          5d20h

# Validate an instance of the SDR exists and is pointing to the right node.
[12:09:29] / $ kubectl get instances.akri.sh -n akri
NAME                   CONFIG          SHARED   NODES                AGE
akri-sd-radio-d24113   akri-sd-radio   false    ["turing-agent03"]   5d20h
```

Once those are both place, you'll need to add the respective resource block to your deployment/pod so Akri can allocate the pod(s) to the right node(s). Use the following as a minified example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: openwebrx
  namespace: openwebrx
    spec:
      containers:
      - image: jketterl/openwebrx:latest
        imagePullPolicy: IfNotPresent
        name: openwebrx
        resources:
          limits:
            akri.sh/akri-sd-radio: "1"
        securityContext:
          privileged: true
```