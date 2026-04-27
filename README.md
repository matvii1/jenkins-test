# Minimal todo app (Jenkins + Docker practice)

Static todo UI in `public/` (saved in the browser via `localStorage`). Served by a tiny Node script with no npm dependencies.

## Build and run with Docker Compose

From the repo root:

```bash
docker compose up --build
```

Open http://localhost:3000

Run in detached mode and stop later:

```bash
docker compose up -d --build
docker compose down
```

Use another host port (avoids "port already allocated"):

```bash
HOST_PORT=3001 docker compose up --build
```

## Build and run with plain Docker

```bash
docker build -t jenkins-test-todo .
docker run --rm -p 3000:3000 jenkins-test-todo
```

## Run without Docker (local Node)

```bash
node server.js
```

Then open http://localhost:3000

## Jenkins notes

- **Your current `Jenkinsfile`** uses `agent { docker { image 'node:...' } }`, which runs the pipeline *inside* a Node container. That is useful for `node --version` or running tests, but it is **not** the same as building *this* app’s image from the `Dockerfile`.
- **To run Docker in Jenkins**, the agent that runs the stage needs access to the Docker daemon (for example `agent any` on a machine with Docker installed), or use a dedicated approach (Kaniko, `docker.build` from a suitable plugin, etc.) depending on how your Jenkins is set up.

Freestyle job shell example with Docker Compose (build, smoke-test, cleanup):

```bash
set -e
export HOST_PORT=3001
docker compose -p todo-ci up -d --build
trap 'docker compose -p todo-ci down' EXIT
curl --fail --retry 10 --retry-delay 1 http://127.0.0.1:${HOST_PORT}
```
