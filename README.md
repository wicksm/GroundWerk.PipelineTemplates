# GroundWerk.PipelineTemplates

GroundWerk.PipelineTemplates holds the Azure Pipelines templates that the project's pipelines extend, so every pipeline builds, scans and gates code the same way (CICD-1). A pipeline pins a release tag of this repo, such as v1.0.0, so a change here reaches a pipeline only through a PR that moves it to a new tag, which Renovate opens in each repo once the tag is pushed (CICD-17).

## Templates

- `stages/ci.yml`: the ci stage, which opens every run (CICD-3). One container job on a Microsoft-hosted agent, in the image the pipeline names, scans the commits for secrets with gitleaks, signs in to Azure Artifacts, then runs the repo's build.cs inside SonarQube Cloud's analysis. In a PR build, a failed quality gate fails the job. A repo that ships NuGet packages sets `packageFeed` to its feed's URL: each PR build then pushes its packages as prereleases named for the PR, and the build of a version tag such as v1.2.0 pushes that version, without a second analysis, but only if the tag is on main. A tag on any other commit fails its build before the job signs in to Azure Artifacts, so it releases nothing (CICD-13). Each build then checks the repo's packages against the minimum versions in variable group minimum-versions, which GroundWerk's infra/azdo/minimum-versions.tf sets: a package below its minimum warns until the minimum's deadline and fails the build from that day on, in UTC (CICD-17).

## Releasing

Change a template through a PR into main. Once it merges, tag the merge commit with the next version, such as v1.1.0, and push the tag: a new major version when a pipeline has to change to use it, such as a new parameter without a default. Never move or delete a tag, since pipelines pin them.
