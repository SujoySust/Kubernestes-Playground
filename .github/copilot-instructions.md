## Copilot instructions for this repository

This repo is a small, educational Kubernetes manifest collection (kubernetes-for-beginners). The guidance below is intentionally concise and focused on immediately useful, discoverable facts so an AI coding agent can be productive without guessing.

### Big picture
- Purpose: simple, hand-written Kubernetes YAML examples for learning (Pods and ReplicaSets).
- Structure: top-level folders are `pods/` and `replicasets/`. Files are single-resource manifests (no helm, kustomize, or CI detected).
- Runtime dependencies: images referenced are public Docker Hub images (for example `nginx` in `pods/*.yml` and `replicasets/replicaset.yaml`). There is no build or container image pipeline in this repo.

### Key files and patterns (use these as ground truth)
- `pods/pod.yml` — a single `Pod` with labels `app: nginx` and `tier: frontend`.
- `pods/nginx.yaml` — another standalone `Pod` using `image: nginx` and label `env: production`.
- `replicasets/replicaset.yaml` — `ReplicaSet` with `spec.selector.matchLabels.app: myapp` and `template.metadata.labels.app: myapp` and `replicas: 3`.

Patterns to mirror when making edits:
- Labels are the primary way resources are selected. Keep `selector.matchLabels` aligned with `template.metadata.labels` for ReplicaSets.
- Manifests are minimal; most fields are intentionally omitted (no resource requests/limits, no probes).

### Concrete developer workflows (explicit, reproducible commands)
Use these locally when testing or validating changes. These commands are the expected way to apply and inspect the YAML in this repo.

Apply a manifest or a directory of manifests:
```bash
kubectl apply -f pods/pod.yml
kubectl apply -f replicasets/replicaset.yaml
```

Basic validation and dry-run (client-side validation):
```bash
kubectl apply --dry-run=client -f <path-to-yaml>
```

Inspect created resources:
```bash
kubectl get pods
kubectl get rs
kubectl describe rs myapp-replicaset
kubectl get pods -l app=myapp
kubectl logs <pod-name>
```

Notes: the repo does not include CI scripts, test harnesses, or a local cluster setup. Assume a reachable Kubernetes cluster (kind, minikube, or remote) for live testing.

### Project-specific conventions and gotchas
- Single-resource YAML files: keep edits local to a file — the repository consistently uses one resource per YAML file.
- Label names used in this repo: `app`, `tier`, `env`, and `myapp` is used in the ReplicaSet sample.
- ReplicaSet template includes `metadata.name: nginx` in the pod template. That field in a Pod template is ignored by controllers and can be left or removed; rely on labels for selection.
- No Services, Deployments, or Ingress resources are present — if you add exposures, prefer adding `Service` manifests next to the relevant pod/replicaset file.

### Integration points & external dependencies
- Images: `nginx` (Docker Hub). No private registries referenced.
- No obvious external APIs, libraries, or tooling enforced by the repo.

### When to ask for clarification
- If you need a specific cluster target (kind/minikube/GKE) or CI pipeline to be added, ask which runner/cluster to support.
- If adding new resources, confirm label naming conventions (e.g., whether to use `app: <name>` or `component:`) — the repo uses `app` commonly.

### Minimal contract for edits by an AI agent
- Input: one or more changed YAML manifests in `pods/` or `replicasets/`.
- Output: well-formed Kubernetes YAML that preserves existing label/selectors where applicable.
- Error modes to avoid: breaking ReplicaSet selectors (mismatched labels) and adding non-referenced Service objects without labels.

If anything here is unclear or you'd like the file to include cluster-specific setup (kind/minikube), or templating (Helm/Kustomize) recommendations, tell me which approach you prefer and I'll iterate.
