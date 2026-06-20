# Step 04 - MongoDB on Kubernetes

- added headless Service for MongoDB
- added MongoDB StatefulSet with PVC
- changed MONGO_URI to mongodb://mongo:27017/movies

- applied MongoDB headless Service and StatefulSet
- waited for MongoDB rollout
- reapplied app manifests with in-cluster Mongo URI
- restarted movie-api deployment

- verified Mongo pod and PVC
- checked app logs for mongodb://mongo connection
- created/listed movie through Kubernetes Service
- counted documents inside mongo-0
- Step 04 completed
