Kubernetes deployment and GitHub Actions
=====================================

Files added
- `k8s/namespace.yaml` - namespace used for deployment (`hospital`).
- `k8s/deployment.yaml` - Deployment manifest for the Spring Boot app.
- `k8s/service.yaml` - Service (LoadBalancer) exposing the app on port 80.
- `.github/workflows/k8s-deploy.yml` - CI workflow that builds the image and deploys manifests.

How it works
1. Push to `main` triggers the workflow.
2. The workflow builds the jar with Maven, builds and pushes a Docker image to GitHub Container Registry (GHCR) at `ghcr.io/<owner>/<repo>:latest`.
3. The workflow expects a secret `KUBE_CONFIG_DATA` containing your kubeconfig base64-encoded. It writes it to `$HOME/.kube/config` and runs `kubectl apply -f k8s/`.

Required repo secrets
- `KUBE_CONFIG_DATA` - base64-encoded kubeconfig (run: `cat ~/.kube/config | base64 -w0` then paste into the secret value).
- (Optional) If you prefer using a PAT for GHCR instead of `GITHUB_TOKEN`, create `GHCR_PAT` and update the workflow login step.

Notes & next steps
- Replace the image name in `k8s/deployment.yaml` if you want a specific tag. The workflow currently pushes `:latest`.
- If your app does not expose `/actuator/health`, update the probes in `k8s/deployment.yaml` to use a valid path.
- For production use, add resource requests/limits, configs in ConfigMaps/Secrets, and more robust rollout strategies.

If you want, I can:
- Update the deployment image to a fixed explicit name/tag.
- Add an Ingress manifest for hostname routing.
- Add HPA (HorizontalPodAutoscaler) config.
