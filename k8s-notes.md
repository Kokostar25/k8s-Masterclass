1. Updated architecture of AWS infrastructure
2. understand pods --> replicasets --> deployments --> services
3. Other kubernetes concepts
   1. statelessness of kubernetes
   2. configmap
   3. secret
   4. persistent volumes and persistent volume claims
4. Templating kubernetes manifest files with Helm
5. Deploying tools into kubernetes with Helm
   1. Jenkins
   2. Vault
   3. Consul
   4. Cert-manager
   5. Tooling app
6. Building CI/CD Pipelines with Jenkinsfile, deploying into multiple environments (namespace and cluster separated environments)
   1. Dockerfile pipeline
   2. Terraform pipeline
   3. Kubernetes Pipeline
7. Further projects - Deploying more tools into Kubernetes
   1. Elasticsearch
   2. Prometheus
   3. Grafana
   


Branch Changes 

project/v5
1. Simulate Statelessness in Kubernetes 
2. Make deployments stateful with configmap
   -- Kubernetes has another API object that has more advanced usage of statefulness for stateful applications like databases - Statefulsets

project/v6
1. Introduce Helm 
2. helm installation 
3. helm create nginx
4. Deploy all yaml files with "Helm Install"
5. Helm Upgrade
6. Rolling update in action
7. Introduce Values 
8. If condition
9. Helm Rollback and forward
10. deploy a public chart
11. Clean up

Home Work
1. Deploy Tooling app with Helm - tooling-deployment.yaml