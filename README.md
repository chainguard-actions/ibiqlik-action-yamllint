# GitHub YAMLlint

This action executes `yamllint` (https://github.com/adrienverge/yamllint) against file(s) or folder

## Usage

Simple as:

```yaml
- uses: ibiqlik/action-yamllint@v3
```

### Optional parameters

- `config_file` - Path to custom configuration
- `config_data` - Custom configuration (as YAML source)
- `file_or_dir` - Enter file/folder (space separated), wildcards accepted. Examples:
  - `.` - run against all yaml files in a directory recursively (default)
  - `file1.yaml`
  - `file1.yaml file2.yaml`
  - `kustomize/**/*.yaml mychart/*values.yaml`
- `format` - Format for parsing output `[parsable,standard,colored,github,auto] (default: parsable)`
- `strict` - Return non-zero exit code on warnings as well as errors `[true,false] (default: false)`
- `no_warnings` - Output only error level problems `[true,false] (default: false)`

**Note:** If `.yamllint` configuration file exists in your root folder, yamllint will automatically use it.

### Example usage in workflow

```yaml
name: Yaml Lint
on: [push]
jobs:
  lintAllTheThings:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v1
    - name: yaml-lint
      uses: ibiqlik/action-yamllint@v3
      with:
        file_or_dir: myfolder/*values*.yaml
        config_file: .yamllint.yml
```

Or just simply check all yaml files in the repository:

```yaml
name: Yaml Lint
on: [push]
jobs:
  lintAllTheThings:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v1
    - name: yaml-lint
      uses: ibiqlik/action-yamllint@v3
```

Config data examples:

```yaml
# Single line
config_data: "{extends: default, rules: {new-line-at-end-of-file: disable}}"
```

``` yaml
# Multi line
config_data: |
  extends: default
  rules:
    new-line-at-end-of-file:
      level: warning
    trailing-spaces:
      level: warning
```

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
