# Ruby Test Matrix Workflow

This workflow is for the testing of ruby repositories against a matrix of both ruby and gemfile versions. Ruby versions are provided via input parameter and Gemfiles are automatically parsed out of the calling repository.

## Usage

Other workflows can run this workflow similar to using a Github Action

**Example**
```yaml
---
jobs:
  tests:
    uses: Invoca/ruby-test-matrix-workflow/.github/workflows/ruby-test-matrix.yml@main
```

This default usage will run the rspec test suite using the following matrix strategy
```yaml
matrix:
  ruby: [3.1, 3.2, 3.3, 3.4]
  gemfile: // All files from the calling repo that ends with `*gemfile`
```

### Available Parameters

Parameters can be passed to the workflow using the usual Github Action syntax
```yaml
uses: Invoca/ruby-test-matrix-workflow/.github/workflows/ruby-test-matrix.yml@main
with:
  input-1: "hello"
  input-2: "world"
```

* `ruby-versions`: (string, optional) The array of ruby versions to use in the matrix (defaults to `"[3.1, 3.2, 3.3, 3.4]"`).
  ```yaml
  with:
    ruby-versions: "['3.0', 3.1, 3.2, 3.3, 3.4]"
  ```

* `pre-test-hook`: (string, optional) A hook for running arbitrary commands before tests are run.
  ```yaml
  with:
    pre-test-hook: "bundle exec rails db:migrate"
  ```

* `test-command`: (string, optional) The specific test command to run (defaults to `bundle exec rspec`).
  ```yaml
  with:
    test-command: "bundle exec rake test"
  ```
