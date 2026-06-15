# Step 04.5 — Rebuild the app (v2) and update the manifests

**Goal:** the next step (Helm) introduces an `extraEnv` knob whose example is `LOG_LEVEL`. But the app you containerized in Step 02 (**v1**) only ever read `MONGO_URI` — it ignores `LOG_LEVEL` entirely. Before Helm can show that knob *doing* something, the app has to actually read it.

In this short step you swap in the **v2** app (which reads `LOG_LEVEL` and adjusts its logging), rebuild and push the image as `:1.1`, and add a `LOG_LEVEL` env var to your `k8s/deployment.yaml`. After this, Step 05's "copy your `k8s/deployment.yaml` and templatize the `env:` block" is literally true — the block already exists.

> **Why a new version at all?** v1 configured logging implicitly. v2 reads `LOG_LEVEL` (default `info`) and routes startup/DB messages through Python's `logging`, so setting `DEBUG`/`WARNING` visibly changes the pod logs. The two source trees are shipped side by side: `movie-api-v1.zip` (original) and `movie-api-v2.zip` (this step). The `movie-api/` working folder is already v2.

---

## A. Get the v2 source

**Task:** make sure your `movie-api/` folder is the **v2** code.

*Hints:*
- If you've been editing in place, you already have it. Otherwise unzip `movie-api-v2.zip` over your `movie-api/` folder.
- Confirm it reads `LOG_LEVEL`: `app/main.py` should call `os.getenv("LOG_LEVEL", "info")` and `logging.basicConfig(...)`, and `app/db.py` should log the `[db] connected to …` line via a logger (not `print`).

**What changed v1 → v2 (so you know what to look for):**
- `app/main.py` — reads `LOG_LEVEL`, configures `logging`, logs a startup line (and a `debug` line visible only at `DEBUG`).
- `app/db.py` — the connection message goes through `logging` instead of `print`, so it honors the level.
- No new dependencies, no API changes — same endpoints, same `MONGO_URI` behavior.

## B. Rebuild and push as `:1.1`

**Goal:** a new, versioned image so the change is explicit and the cluster can pull fresh content (don't reuse `:1.0` — same tag with new bytes invites stale-cache confusion).

**Tasks (work out the commands — same shape as Step 02):**
1. Build from inside `movie-api/`, tagging `<dockerhub-user>/movie-api:1.1`.
2. Push the `:1.1` tag.

*Hints:*
- `docker build -t <dockerhub-user>/movie-api:1.1 .`
- `docker push <dockerhub-user>/movie-api:1.1`
- **Local cluster (minikube/kind):** instead of pushing, load the image into the cluster (`minikube image load movie-api:1.1` / `kind load docker-image movie-api:1.1`) and set `imagePullPolicy: Never` (or `IfNotPresent`).

## C. Update `k8s/deployment.yaml`

**Task:** point the Deployment at `:1.1` and add the `LOG_LEVEL` env var.

- Bump the image tag to `...:1.1`.
- Add an `env:` block alongside the existing `envFrom:` (keep `envFrom` — `LOG_LEVEL` is *in addition to* the ConfigMap/Secret):
  ```yaml
  env:
    - name: LOG_LEVEL
      value: "info"
  ```

> This is the `env:` block Step 05 turns into the templated `extraEnv` loop. You're writing it by hand here so there's something real to templatize there.

*Reference answer: `solved/step-04.5/k8s/deployment.yaml`.*

## D. Roll out and verify the level takes effect

**Tasks:**
1. Apply the updated Deployment and wait for the rollout.
2. Confirm the app logs the startup line and `[db] connected to …`.
3. Change `LOG_LEVEL` to `DEBUG`, re-apply, and confirm a `debug` line now appears. Set it to `WARNING` and confirm the startup/connect lines disappear.

*Hints:*
- `kubectl apply -f k8s/deployment.yaml` then `kubectl rollout status deploy/movie-api`.
- `kubectl logs deploy/movie-api | grep -Ei "log level|connected to|debug"`.
- Quick level flip without editing the file: `kubectl set env deploy/movie-api LOG_LEVEL=DEBUG` (then watch the logs), and `LOG_LEVEL=info` to restore.

---

## What you learned

- An env var only matters if the **app reads it** — `extraEnv`/`LOG_LEVEL` is inert until the code honors it (v1 → v2).
- Bumping the image tag (`1.0` → `1.1`) makes a code change explicit and avoids stale-image confusion from reusing a tag.
- The hand-written `env:` block here is exactly what Step 05 parameterizes as `extraEnv`.

## Next

→ [Step 05 — Package as a Helm chart](step-05-helm-basic.md)
