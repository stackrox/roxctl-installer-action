# Releasing a new version

This documents all steps requires to release a new version of the roxctl-installer GitHub Action.

## Create a new GitHub release incl. tag

Once you're ready to release, create a GitHub release including a new release.
Currently, no special GitHub action is used to draft the release, we use the default GitHub template for the release
notes.

We strive to version according to [semantic versioning](https://semver.org/).

## Move the main version tag to the new version

Once the new release is created including the new tag, we move the main version tag (i.e `v1`) to point to the newly
released version.

This is done by manually triggering the workflow `update-main-version`.

As input, you have to choose the main version for which you want to move the referenced Git commit (i.e. `v1`) as well
as the newly created release tag.

Once the action run has been successful, the release of the new version is complete.
