# Minimal todo app (Jenkins + Docker practice)

Static todo UI in `public/` (saved in the browser via `localStorage`). Served by a tiny Node script with no npm dependencies.

## Build and run with Docker

From the repo root:

```bash
docker build -t jenkins-test-todo .
docker run --rm -p 3000:3000 jenkins-test-todo
```

Open http://localhost:3000

Override port inside the container:

```bash
docker run --rm -e PORT=8080 -p 8080:8080 jenkins-test-todo
```

## Run without Docker (local Node)

```bash
node server.js
```

Then open http://localhost:3000

## Jenkins notes

- **Your current `Jenkinsfile`** uses `agent { docker { image 'node:...' } }`, which runs the pipeline *inside* a Node container. That is useful for `node --version` or running tests, but it is **not** the same as building *this* app’s image from the `Dockerfile`.
- **To run `docker build` in Jenkins**, the agent that runs the stage needs access to the Docker daemon (for example `agent any` on a machine with Docker installed, plus `sh 'docker build -t jenkins-test-todo .'`), or use a dedicated approach (Kaniko, `docker.build` from a suitable plugin, etc.) depending on how your Jenkins is set up.

Example shell step you can add once your agent can run Docker:

```bash
docker build -t jenkins-test-todo:${BUILD_NUMBER} .
```
