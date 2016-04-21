# Importing upstream changes

This document describes how to import changes from upstream repo
(https://github.com/JetBrains/jdk8u*.git). Before doing these, you need `forge-author`,
`forge-committer` and `direct-push` permissions for the relevant projects in
`jetbrains-master-mirror` and `jetbrains-master-mirror-osx` branches.


## Overview

JetBrains's projects have two branches - `master` and `master-osx` that we need to track. The
`master` branch is used for linux and windows. The `master-osx` branch is used for mac. These map
to `jetbrains-master-mirror` and `jetbrains-master-mirror-osx` respectively in our tree. No change
should be made directly to these branches and they should track the upstream bit-for-bit. After
importing the changes from jetbrains into these mirror branches, we can merge them into
`studio-master-dev` and `studio-master-dev-osx`. Any changes made by us, should be made to
`studio-master-dev`.

There is an auto-merger from `studio-master-dev` to `studio-master-dev-osx`. So, any change made
should be available for both branches.


## Steps

- Get the sources by cloning the repo

```bash
repo init -u https://android-googlesource.com/platform/manifest -b openjdk
repo sync -j8  # Do not use '-c' here
```

- Create a script called `merge.sh` with the following contents and grant it execute permissions:

```bash
#!/bin/bash
PROJECT_NAME=$(basename $REPO_PROJECT)

# Make sure we're in a clean state.
git checkout aosp/studio-master-dev # to make sure local_copy isn't checked out
git branch -D local_copy local_copy-osx

# Fetch changes and push to jetbrains-master-mirror.
git fetch https://github.com/JetBrains/$PROJECT_NAME.git master:local_copy
git fetch https://github.com/JetBrains/$PROJECT_NAME.git master-osx:local_copy-osx
git push aosp local_copy:refs/heads/jetbrains-master-mirror
git push aosp local_copy-osx:refs/heads/jetbrains-master-mirror-osx

# Merge the mirror into studio-master-dev and upload to gerrit
COMMIT_MSG="Merge 'jetbrains-master-mirrorSUFFIX' into studio-master-devSUFFIX" # edit as needed.
git branch -D studio-master-dev studio-master-dev-osx
git checkout -b studio-master-dev -t aosp/studio-master-dev
git merge -m "${COMMIT_MSG//SUFFIX/}" local_copy
repo upload --br=studio-master-dev $REPO_PROJECT
```

- Run:

```bash
repo forall -c ./merge.sh # assuming merge.sh is in the current dir.
```

- The above should create a merge CL per project that you can review in gerrit and submit.

- There is an auto-merger from `studio-master-dev` to `studio-master-dev-osx`. Once the merge
submitted above has merged into `studio-master-dev-osx`, continue with the following steps.

- Create another file called `merge2.sh` with the followning contents and grant it execute
permissions:

```bash
#!/bin/bash
COMMIT_MSG="Merge 'jetbrains-master-mirrorSUFFIX' into studio-master-devSUFFIX" # edit as needed.
git checkout -b studio-master-dev-osx -t aosp/studio-master-dev-osx
git merge -m "${COMMIT_MSG//SUFFIX/-osx}" local_copy-osx
repo upload --br=studio-master-dev-osx -D studio-master-dev-osx $REPO_PROJECT
```

- Run:

```bash
repo forall -c ./merge2.sh
```

- This should generate more CLs for you to merge through gerrit.

## ProTip

You can add the following lines to `~/.gitconfig` to skip "are you sure you want to upload"
question from repo.

```
[review "https://android-review.googlesource.com/"]
        autoupload = true
```

## Building

- TODO: add instructions to build linux and win binaries.
- To build mac binaries, checkout the project and sync (don't use the '-c' flag when syncing).
- Checkout the mac branch: `repo forall -c git checkout aosp/studio-master-dev-osx`.
- You can now build the mac binaries.
