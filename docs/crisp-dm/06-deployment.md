# 06 — Deployment

Status: not started. `deploy/` stays empty in cycle 1.

This study does not ship a diagnostic service. Deployment, when it is in scope, means a later artifact: the saved Keras model, the fitted preparation parameters, and the YAML that built them, so a rerun is the same run.

Docker, a serving process, and a monitoring loop are out of cycle 1. They are not a substitute for the evaluation rules in `docs/crisp-dm/05-evaluation.md`.
