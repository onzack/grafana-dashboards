# OpenShift Workaround

Unfortunately, there is a bug ([Bug 2037513](https://bugzilla.redhat.com/show_bug.cgi?id=2037513)) in cAdvisor/Kubelet in OpenShift.  
This error causes the label 'container' to be missing in the following metrics:

- `container_fs_reads_bytes_total`
- `container_fs_writes_bytes_total`
- `container_fs_reads_total`
- `container_fs_writes_total` 

**If you are using OpenShift, deploy PrometheusRule `onzack-namespace-monitoring-recording-rules-openshift-workaround.yaml` in your cluster.**
