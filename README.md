# bump

A GitHub Action that updates a Ruby gem using bundler and opens a pull request with the changes.

## Usage

This action is designed to be used with `workflow_dispatch` to manually trigger gem updates. Add the following workflow to your repository:

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
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: .ruby-version
          bundler-cache: true

      - name: Bump gem
        uses: dentarg/bump@main
        with:
          gem_name: ${{ inputs.gem_name }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `gem_name` | Name of the Ruby gem to update | Yes | - |
| `github_token` | GitHub token for creating pull requests | Yes | - |
| `branch_name` | Name of the branch to create for the PR | No | `bump-<gem_name>-<timestamp>` |

## How it works

1. The action runs `bundle update --conservative <gem_name>` to update only the specified gem and its dependencies
2. If there are changes to `Gemfile.lock`, it creates a pull request with the updates
3. The PR includes a descriptive title and body explaining the changes

## Example

To update the `sinatra` gem in your project:

1. Go to the "Actions" tab in your repository
2. Select the "Bump Gem" workflow
3. Click "Run workflow"
4. Enter `sinatra` as the gem name
5. Click "Run workflow"

The action will create a pull request with the updated gem if there are any changes.