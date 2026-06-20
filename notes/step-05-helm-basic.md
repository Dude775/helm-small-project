# Step 05 - Helm basic chart

- created movie-chart
- added values.yaml and values-prod.yaml
- added helpers
- templated ConfigMap, Secret, Deployment, Service
- templated Mongo Service and StatefulSet
- added NOTES.txt

- removed manual Kubernetes resources before Helm install
- installed Helm release demo
- verified rollout and rendered release manifest

- upgraded release with replicaCount=3
- upgraded release with values-prod.yaml
- checked Helm history
- rolled back demo release to revision 1
