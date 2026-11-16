# GroundWerk.PipelineTemplates

GroundWerk.PipelineTemplates holds the Azure Pipelines templates that GroundWerk's pipelines extend, so every pipeline builds, scans and gates code the same way (CICD-1). A pipeline pins a release tag of this repo, such as v1.0.0, so a change here reaches a pipeline only through a PR that moves it to a new tag.

## Templates

- `stages/ci.yml`: the ci stage, which opens every run (CICD-3). One container job on a Microsoft-hosted agent, in the image the pipeline names, scans the commits for secrets with gitleaks, then runs the repo's build.cs inside SonarQube Cloud's analysis. In a PR build, a failed quality gate fails the job.

## Releasing

Change a template through a PR into main. Once it merges, tag the merge commit with the next version, such as v1.1.0, and push the tag: a new major version when a pipeline has to change to use it, such as a new parameter without a default. Never move or delete a tag, since pipelines pin them.
