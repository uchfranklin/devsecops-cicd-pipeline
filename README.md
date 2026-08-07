# DevSecOps CI/CD Pipeline

This repository delivers a small FastAPI service through a repeatable CI pipeline. It ensures code is tested, dependencies are audited, and the resulting Docker image is scanned before it is considered ready for release.

## Problem addressed

Manual releases make it easy to deploy broken code or insecure dependencies. The workflow in this repository applies the same validation on every pull request and change to `main`.

## Components

- FastAPI endpoints: `/health` and `/version`
- pytest test suite
- Multi-stage Docker image that runs as a non-root user
- GitHub Actions workflow for tests, dependency auditing, image build, and Trivy scanning

## Run locally

```bash
python -m venv .venv
# PowerShell: .venv\\Scripts\\Activate.ps1
source .venv/bin/activate
pip install -r requirements-dev.txt
pytest
uvicorn app.main:app --reload
```

Open `http://127.0.0.1:8000/health`; it should return `{"status":"ok"}`.

## Run in Docker

```bash
docker build -t secure-api:local .
docker run --rm -p 8000:8000 secure-api:local
curl http://localhost:8000/health
```

## CI pipeline

The workflow in `.github/workflows/ci.yml` runs automatically for pull requests and pushes to `main`. It fails if tests fail, a dependency audit finds a vulnerability, or Trivy finds a high- or critical-severity image finding.

## Verify security checks locally

```bash
pip-audit -r requirements.txt
trivy image secure-api:local
```

## Next steps

1. Create a GitHub repository and push this folder.
2. Confirm the CI workflow completes successfully.
3. Add branch protection requiring the CI check before merge.
4. Optionally add a tag-based release workflow to publish the image to GitHub Container Registry.

## Security notes

Never commit credentials. Store any registry or deployment credential as a GitHub Actions secret, use least-privilege access, and keep the default branch free of known critical findings.

## License

MIT
