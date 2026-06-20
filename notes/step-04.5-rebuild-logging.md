# Step 04.5 - rebuild app v2

- confirmed app reads LOG_LEVEL
- target image: idf775/movie-api:1.1

- built image idf775/movie-api:1.1
- pushed image idf775/movie-api:1.1 to Docker Hub

- updated k8s deployment image to idf775/movie-api:1.1
- added LOG_LEVEL=info env var

- applied updated deployment
- verified info logs
- changed LOG_LEVEL to DEBUG and verified debug logs
- restored LOG_LEVEL=info
- Step 04.5 completed

- applied updated deployment
- verified info logs
- changed LOG_LEVEL to DEBUG and verified debug logs
- restored LOG_LEVEL=info
- Step 04.5 completed
