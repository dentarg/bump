# bump

A GitHub Action that updates a Ruby gem using bundler and opens a pull request with the changes.

## Usage

This action is designed to be used with `workflow_dispatch` to manually trigger gem updates. Add the following workflow to your repository.

Also enable `Allow GitHub Actions to create and approve pull requests` in repository settings.

```yaml
name: Bump Gem

on:
  workflow_dispatch:
    inputs:
      gem_name:
        description: 'Name of the Ruby gem to update'
        required: true
        type: string

jobs:
  bump:
    runs-on: ubuntu-latest
    steps:
      - uses: dentarg/bump@main
        with:
          gem_name: ${{ inputs.gem_name }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          labels: dependencies
```

To be able to [trigger further workflow runs], the pull requests created are marked as draft, so you can use the [`ready_for_review` trigger] in your workflows to have them run when you mark the pull request as ready.

```yaml
name: CI

on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review] # this includes the defaults

jobs:
  specs:
    runs-on: ubuntu-latest
    steps:
    - run: my-test-command
```

## Inputs

See [`action.yml`](action.yml).

## How it works

1. The Ruby version needs to be specified in `Gemfile.lock`
1. The action runs `bundle update --conservative <gem_name>` to update only the specified gem and its dependencies
1. If there are changes to `Gemfile.lock`, it creates a pull request with the updates
1. The PR includes a descriptive title and body explaining the changes

## Development

```shell
# test main
bin/test

# test pull-request
bin/test <pull-request number>

# cleanup pull-request created from testing
bin/del <pull-request number>

# squash merge pull-request
bin/merge-pr <pull-request number>
```

[trigger further workflow runs]: https://github.com/peter-evans/create-pull-request/blob/main/docs/concepts-guidelines.md#triggering-further-workflow-runs
[`ready_for_review` trigger]: https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#pull_request
