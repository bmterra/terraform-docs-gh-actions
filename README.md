# terraform-docs
A Github action for generating terraform module documentation using terraform-docs and gomplate.  In addition to statically defined directory modules, this module can search specific sub folders or parse atlantis.yaml for module identification and doc generation.  This action has the ability to auto commit docs to an open PR or after a push to a specific branch.

# Usage

## Inputs

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| tf\_docs\_args | Additional args to pass | --sort-inputs-by-required | false |
| tf\_docs\_atlantis\_file | Generate directories by parsing an atlantis formatted yaml to enable provide the file name to parse (eg atlantis.yaml) (disabled by default) | disabled | false |
| tf\_docs\_content\_type | Generate document or table | table | false |
| tf\_docs\_find\_dir | Generate directories by running find ./tf\_docs\_find\_dir -name \*.tf (disabled by default) | disabled | false |
| tf\_docs\_git\_commit\_message | Commit message | terraform-docs: automated action | false |
| tf\_docs\_git\_push | If true it will commit and push the changes | false | false |
| tf\_docs\_indention | Indention level of Markdown sections [1, 2, 3, 4, 5] (default 2) | 2 | false |
| tf\_docs\_output\_file | File in module directory where the docs should be placed | USAGE.md | false |
| tf\_docs\_output\_method | Method should be one of (replace/inject/print) where replace will replace the tf\_docs\_output\_file, inject will inject the content between start and close delims and print will just print the output | inject | false |
| tf\_docs\_template | When provided will be used as the template if/when the OUTPUT\_FILE does not exist | # Usage<br><!--- BEGIN\_TF\_DOCS ---><br><!--- END\_TF\_DOCS ---><br> | false |
| tf\_docs\_working\_dir | Directories of terraform modules to generate docs for seperated by commas (conflicts with atlantis/find dirs) | . | false |

## Outputs

| Name | Description |
|------|-------------|
| num\_changed | Number of files changed |

# Important Notes

In addition to the below notes, further documentation on terraform-docs can be found [here](https://github.com/segmentio/terraform-docs)

## Output Method (tf\_docs\_output\_method)

### print
This will just print the generated file

### replace
This will create/replace the tf\_docs\_output\_file at the determined module path(s)

### inject
Instead of replacing the output file, this will inject the generated documentation into the existing file between the predefined delimeters: `<!--- BEGIN_TF_DOCS --->` and `<!--- END_TF_DOCS --->`.  If the file exists but does not contain the delimeters, the action will fail for the given module.  If the file doesn't exist, it will create it using the value tf\_docs\_template which MUST have the delimeters.

## Auto commit changes
To enable you need to ensure a few things first:
- set tf\_docs\_git\_push to 'true'
- use actions/checkout@v2 with the head ref for PRs or branch name for pushes

### PR
```
on:
  - pull_request
jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
      with:
        ref: ${{ github.event.pull\_request.head.ref }}
```

### Push
```
on:
  push:
    branches:
      - master
jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
      with:
        ref: master
```

## Content type (tf\_docs\_content\_type)
- document - long form document
- table - github formatted table
- json - pure json output


# Examples

## Simple / Single folder
```
- name: Generate TF Docs
  uses: Dirrk/terraform-docs@v1
  with:
    tf_docs_working_dir: .
    tf_docs_output_file: README.md
```

## Use atlantis.yaml v3 to find all dirs
```
- name: Generate TF docs
  uses: Dirrk/terraform-docs@v1
  with:
    tf_docs_atlantis_file: atlantis.yaml
```

## Find all .tf file folders under a given directory
```
- name: Generate TF docs
  uses: Dirrk/terraform-docs@v1
  with:
    tf_docs_find_dir: examples/
```

Complete examples can be found [here](https://github.com/Dirrk/terraform-docs/tree/v1/examples)
