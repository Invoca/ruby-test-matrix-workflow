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
    secrets: inherit
```

This default usage will run the rspec test suite using the following matrix strategy
```yaml
matrix:
  ruby: [3.1, 3.2, 3.3, 3.4]
  gemfile: // All files from the calling repo that ends with `*gemfile`
```

`secrets: inherit` passes the org-level `CODECOV_TOKEN` into the reusable workflow. One token works for every repository; there is nothing to provision per repo. Public repositories can omit the token and fall back to tokenless upload. Fork pull requests always take that fallback, because GitHub does not pass secrets to workflows triggered by `pull_request` from a fork.

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

## Codecov

After tests, each matrix cell uploads coverage with `codecov/codecov-action` pinned to v7.0.0. The house convention is a single LCOV file at `coverage/lcov.info`. Copy [Invoca/escalate/spec/simplecov_helper.rb](https://github.com/Invoca/escalate/blob/master/spec/simplecov_helper.rb) (and `gem "simplecov", "~> 0.22"` plus `gem "simplecov-lcov", "~> 0.8"`) so the tests actually write that file. Without it the upload step still runs, reports that nothing was found, and the job stays green.

These inputs are the GitHub Actions spelling of the same three concepts as the [codecov-buildkite-plugin](https://github.com/Invoca/codecov-buildkite-plugin): files, flags, and fail on error.

* `coverage-files`: (string, optional) Comma-separated coverage report paths to upload. Set to an empty string to disable the upload step entirely. Defaults to `"coverage/lcov.info"`.
  ```yaml
  with:
    coverage-files: "coverage/lcov.info"
  ```

* `coverage-flags`: (string, optional) Comma-separated Codecov flags used to group coverage metrics. Each flag must match `^[\w\.\-]{1,45}$` (no slashes). Defaults to `""`.
  ```yaml
  with:
    coverage-flags: "ruby"
  ```

* `coverage-fail-on-error`: (boolean, optional) Fail the job when the Codecov upload errors. False by default, matching the informational posture used everywhere else. Pass `true` to make a failed upload fail the job.
  ```yaml
  with:
    coverage-fail-on-error: true
  ```

* `coverage-name`: (string, optional) Label shown for this upload in the Codecov UI. Defaults to the matrix cell (`ruby-<version>-<gemfile>`).
  ```yaml
  with:
    coverage-name: "unit"
  ```
