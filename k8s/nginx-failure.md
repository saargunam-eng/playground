# SadServers Challenge: "Bilbao" (Kubernetes Problems)

### Question
The Nginx pod is stuck in a `Pending` state and cannot be accessed via the Load Balancer.

**Success Criteria:**
`curl 10.43.216.196` returns the default Nginx page.

---

### Solution
The pod failed to schedule because of restrictive `nodeSelector` requirements and excessive resource requests that the lab nodes could not satisfy.

1. **Diagnose**: 
   `kubectl describe pod <pod_name>` showed `FailedScheduling` due to `node(s) didn't match Pod's node affinity/selector`.
2. **Modify Manifest**:
   Edited `manifest.yml` to:
   - Remove the `nodeSelector` for `disk: ssd`.
   - Reduce the memory request from `2000Mi` to `128Mi` to fit node capacity.
3. **Redeploy**:
   `kubectl apply -f manifest.yml`
4. **Verify**:
   `curl 10.43.216.196`

---

### Key Learning
* **Scheduling Constraints**: `nodeSelector` forces Kubernetes to only place pods on nodes with specific labels. If no nodes match, the pod remains `Pending`.
* **Resource Quotas**: Setting high memory `requests` in a small cluster will prevent pods from starting if the sum of requests exceeds available node memory.
