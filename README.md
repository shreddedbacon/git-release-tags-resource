# Git Release Tags Resource

A Concourse resource for the getting releases from github tags. Will only work for release tags that use semver in their tags.

## Source Configuration

```yaml
resource_types:
- name: git-release-tags
  type: docker-image
  source:
    repository: shreddedbacon/git-release-tags

resources:
- name: nginx
  type: git-release-tags
  source:
    repo: nginx
    owner: nginx
    token: <github api token>
    final_only: true
    tag_prefix: release-
    per_page: 100
    version_family: 1.12
```

* `repo` is the name of the repository in github
* `owner` is the name of the repository owner in github
* `token` your github api token
* `final_only` will only grab releases that match semver format of `major.minor.patch`, no dev releases. set to false and see next note
* `semver_dev_format` defaults to 1 which will match semver `1.1.1rc1, 1.1.1RC1, etc..` other options are 2 which will match semver `1.1.1-rc.1, 1.1.1-RC.1`
* `tag_prefix` this should match what releases are listed as for the repository naming standard you want to check, ie nginx uses `release-1.12.0`, so the prefix is `release-`
* `per_page` specify the number of results to return in the github api call, defaults to 100 but you can increase if you are getting no results in a repo with lots of tags
* `version_family` you can use this to drill down the versions you want to watch, ie if you only want nginx version 1.12 and its patch releases, you can use `version_family: 1.12`

## Behavior

### `check`: Check for release tags

Checks if there are new versions of the source.

### `in`: Download a version

Places the following files in the destination:

* `source.tar.gz`: The source tarball from the release tag
* `url`: A file containing the URL of the file
* `version`: The version identified in the file name

### `out`: Not implemented

Does nothing

## Example

```yaml
jobs:
- name: get-nginx-source
  plan:
  - get: nginx
    trigger: true
```
