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
```

## Inputs

See [`action.yml`](blob/main/action.yml).

## How it works

1. The action runs `bundle update --conservative <gem_name>` to update only the specified gem and its dependencies
2. If there are changes to `Gemfile.lock`, it creates a pull request with the updates
3. The PR includes a descriptive title and body explaining the changes

## Development

How to test the action:

```shell
bin/test

# test changes
bin/test <pull-request number>

# cleanup
bin/del <pull-request number>
```
