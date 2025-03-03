# actions-batch

A prototype for turning GitHub Actions into a batch job runner.

In the 1970s - or so I hear, [time-sharing](https://en.wikipedia.org/wiki/Time-sharing) was all the rage, with users being able to submit tasks or batch jobs to large computers, and to collect the results when the jobs where done.

## Goal

Run a shell script in an isolated, immutable environment, collect the logs or results.

This works well with self-hosted runners managed by actuated (which use a full VM) or GitHub's hosted runners. It may also work with container-based runners, with some limitations on what software can be used securely i.e. `docker`.

## How it works

1. You write a bash script like the ones in [examples](examples) and pass it in as an argument
1. A new repo is created with a random name in the specified organisation
2. A workflow file is written to the repo along with the shell script, the workflow's only job is to run the shell script and exit
3. The workflow is triggered and you can check the results

`-owner` is intended to be a GitHub organisation, but this can be adapted to a personal account by passing `--org false` to the command.

```bash
git clone git@github.com:alexellis/actions-batch
cd actions-batch

go build
./actions-batch \
  --private \
  --owner actuated-samples \
  --token-file ../pat.txt \
  --runs-on actuated \
  --file examples/slim.sh
```

Example of what's written to the repo:

```bash
name: workflow

# Generated by alexellis/actuated-batch at: 2022-10-07 20:14:30.547822 +0100 BST m=+0.002729293
# Job requested by alex

on:
  pull_request:
    branches:
      - '*'
  push:
    branches:
      - master
      - main

jobs:
  workflow:
    name: agitated_solomon5
    runs-on: actuated
    steps:
      - uses: actions/checkout@v1
      - name: Run the job
        run: |
          chmod +x ./job.sh
          ./job.sh
```

## What's left

[See the issue tracker for ideas](https://github.com/alexellis/actions-batch/issues)

The Rate Limit for a Personal Access Token is quite limited, so this would need to be run as a GitHub App.

## License

MIT

DCO - a Signed-off-by message will be required in each commit message i.e. `git commit --signoff`
