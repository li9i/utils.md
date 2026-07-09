# C

## Cancel action goal from CLI

Action `load_operation` may be canceled by

```bash
ros2 service call /load_operation/_action/cancel_goal action_msgs/srv/CancelGoal "{}"
```

## Colcon

[source](https://gist.github.com/chapulina/7400a708df655cbfba218e169fcad97f#file-colcon_cheatsheet-md)

Only one package:

```bash
--packages-select <package>
```

Packages with regex:

```bash
--packages-select-regex <regex>
```

Verbose, print to console:

```bash
--event-handlers console_direct+
```

Run specific tests:

```bash
--ctest-args -R <part of test name>
```

Disable tests compilation:

```bash
--cmake-args -DBUILD_TESTING=false
```

Combine multiple CMake args:

```bash
--cmake-args ' -DBUILD_TESTING=false' ' -DENABLE_PROFILER=1'
```

Repeat test:

```bash
--retest-until-fail 10
```

Uninstall:

```bash
colcon build --packages-select <package> --cmake-target uninstall
```

Number of cores:

```bash
MAKEFLAGS="-j4" colcon build
```

Specific target, like `codecheck`:

```bash
--cmake-target codecheck
```

List all available targets for a project:

```bash
cd build/project_name
make help
```

---

# M

## Migrate signing key

```bash
sudo rm /usr/share/keyrings/ros-archive-keyring.gpg
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
sudo rm /etc/apt/sources.list.d/ros2.list
sudo rm /usr/share/keyrings/ros-archive-keyring.gpg
```

source: https://discourse.openrobotics.org/t/ros-signing-key-migration-guide/43937#p-93537-how-do-i-migrate-after-june-1st-4

---

# P

## Publish static transform on the fly

```bash
ros2 run tf2_ros static_transform_publisher X Y Z ROLL PITCH YAW PARENT_FRAME CHILD_FRAME
```

---

# R

## Release package via APT

Say you have a package called `apriltag_hitch_estimation` that you wish to release so that one may eventually acquire it with

```bash
sudo apt-get install ros-humble-apriltag-hitch-estimation
```

### Step 1

Place the package in a github repository under a branch called `$ROS_DISTRO-devel`. In this case

```
https://github.com/li9i/apriltag-hitch-estimation
```

under branch `humble-devel`.

Then create an empty repository titled with the same name but append to it the string `-release`, e.g.

```
https://github.com/li9i/apriltag-hitch-estimation-release
```

### Step 2

Make sure the package's `package.xml` has a version, e.g.

```xml
<version>0.0.1</version>
```

Then create and push a Git tag matching that version

```bash
git tag -a 0.0.1 -m "Release 0.0.1"
git push origin 0.0.1
```

> [!IMPORTANT]
> The tag name must exactly match the version in `package.xml`

Then follow Guide A.

### Guide A

- [Releasing a Package / Index Your Packages](https://docs.ros.org/en/humble/How-To-Guides/Releasing/Index-Your-Packages.html)

Follow the guide. At some point you come across [Open a pull request to ros/rosdistro](https://docs.ros.org/en/humble/How-To-Guides/Releasing/Index-Your-Packages.html#open-a-pull-request-to-ros-rosdistro). Before requesting a pull visit the [REVIEW_GUIDELINES](https://github.com/ros/rosdistro/blob/master/REVIEW_GUIDELINES.md) and make sure to adhere to them. Then follow `1` and `2` from [Manually Authorizing Bloom to Generate Github Pull Requests](https://wiki.ros.org/bloom/Tutorials/GithubManualAuthorization). Then [qwen's advice](https://chat.qwen.ai/s/dfa33c4a-8b08-4ecb-8fa3-a63ce24703a8?fev=0.0.248). Then generate a [github token](https://docs.ros.org/en/foxy/How-To-Guides/Releasing/First-Time-Release.html#set-up-a-personal-access-token).

Then run

```bash
bloom-release --rosdistro humble --track humble apriltag_hitch_estimation
```

Output

```bash
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
Specified repository 'apriltag_hitch_estimation' is not in the distribution file located at 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/humble/distribution.yaml'
Did you mean one of these: 'mola_state_estimation', 'apriltag_detector'?
Could not determine release repository url for repository 'apriltag_hitch_estimation' of distro 'humble'
You can continue the release process by manually specifying the location of the RELEASE repository.
To be clear this is the url of the RELEASE repository not the upstream repository.
For release repositories on GitHub, you should provide the `https://` url which should end in `.git`.
Here is the url for a typical release repository on GitHub: https://github.com/ros-gbp/rviz-release.git
==> Looking for a release of this repository in a different distribution...
No reasonable default release repository url could be determined from previous releases.
```

```bash
Release repository url [press enter to abort]: https://github.com/li9i/apriltag-hitch-estimation-release.git
```

```bash
==> Fetching 'apriltag_hitch_estimation' repository from 'https://github.com/li9i/apriltag-hitch-estimation-release.git'
Cloning into '/tmp/tmp03bsbv3e'...
```

```bash
Enter passphrase for key '/home/afilot/.ssh/id_ed25519_iti581_github':
```

```bash
warning: You appear to have cloned an empty repository.
WARNING [vcstools] Command failed: 'git checkout master'
 run at: '/tmp/tmp03bsbv3e'
 errcode: 1:
error: pathspec 'master' did not match any file(s) known to git
[/vcstools]
Creating 'master' branch.
Given track 'humble' does not exist in release repository.
Available tracks: dict_keys([])]
```

```bash
Create a new track called 'humble' now [Y/n]?
```

```bash
<Enter>
```

```bash
Creating track 'humble'...
Repository Name:
  <name>
    Name of the repository (used in the archive name)
  upstream
    Default value, leave this as upstream if you are unsure
  ['upstream']:
```

```bash
<Enter>
```

```bash
Upstream Repository URI:
  <uri>
    Any valid URI. This variable can be templated, for example an svn url
    can be templated as such: "https://svn.foo.com/foo/tags/foo-:{version}"
    where the :{version} token will be replaced with the version for this release.
```

```bash
  [None]: https://github.com/li9i/apriltag-hitch-estimation.git
```

```bash
Upstream VCS Type:
  git
    Upstream URI is a git repository
  hg
    Upstream URI is a hg repository
  svn
    Upstream URI is a svn repository
  tar
    Upstream URI is a tarball
  ['git']:
```

```bash
<Enter>
```

```bash
Version:
  :{auto}
    This means the version will be guessed from the devel branch.
    This means that the devel branch must be set, the devel branch must exist,
    and there must be a valid package.xml in the upstream devel branch.
  :{ask}
    This means that the user will be prompted for the version each release.
    This also means that the upstream devel will be ignored.
  <version>
    This will be the version used.
    It must be updated for each new upstream version.
```

```bash
  [':{auto}']: 0.0.1
```

```bash
Release Tag:
  :{version}
    This means that the release tag will match the :{version} tag.
    This can be further templated, for example: "foo-:{version}" or "v:{version}"

    This can describe any vcs reference. For git that means {tag, branch, hash},
    for hg that means {tag, branch, hash}, for svn that means a revision number.
    For tar this value doubles as the sub directory (if the repository is
    in foo/ of the tar ball, putting foo here will cause the contents of
    foo/ to be imported to upstream instead of foo itself).
  :{ask}
    This means the user will be prompted for the release tag on each release.
  :{none}
    For svn and tar only you can set the release tag to :{none}, so that
    it is ignored.  For svn this means no revision number is used.
  [':{version}']:
```

```bash
<Enter>
```

```bash
Upstream Devel Branch:
  <vcs reference>
    Branch in upstream repository on which to search for the version.
    This is used only when version is set to ':{auto}'.
```

```bash
  [None]: humble-devel
```

```bash
ROS Distro:
  <ROS distro>
    This can be any valid ROS distro, e.g. humble, jazzy, kilted, rolling
  ['humble']:
```

```bash
<Enter>
```

```bash
Patches Directory:
  <path in bloom branch>
    This can be any valid relative path in the bloom branch. The contents
    of this folder will be overlaid onto the upstream branch after each
    import-upstream.  Additionally, any package.xml files found in the
    overlay will have the :{version} string replaced with the current
    version being released.
  :{none}
    Use this if you want to disable overlaying of files.
  [None]:
```

```bash
<Enter>
```

```bash
Release Repository Push URL:
  <url>
    (optional) Used when pushing to remote release repositories. This is only
    needed when the release uri which is in the rosdistro file is not writable.
    This is useful, for example, when a releaser would like to use a ssh url
    to push rather than a https:// url.
  :{none}
    This indicates that the default release url should be used.
  [None]:
```

```bash
<Enter>
```

```bash
Created 'humble' track.
==> Testing for push permission on release repository
==> git remote -v
origin    git@github.com:li9i/apriltag-hitch-estimation-release.git (fetch)
origin    git@github.com:li9i/apriltag-hitch-estimation-release.git (push)
==> git push origin master --dry-run
Enter passphrase for key '/home/afilot/.ssh/id_ed25519_iti581_github':
To github.com:li9i/apriltag-hitch-estimation-release.git
 * [new branch]      master -> master
==> Releasing 'apriltag_hitch_estimation' using release track 'humble'
==> git-bloom-release humble
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
Processing release track settings for 'humble'

Executing release track 'humble'
==> bloom-export-upstream https://github.com/li9i/apriltag-hitch-estimation.git git --tag 0.0.1 --display-uri https://github.com/li9i/apriltag-hitch-estimation.git --name upstream --output-dir /tmp/tmpj36yos6p
Checking out repository at 'https://github.com/li9i/apriltag-hitch-estimation.git' to reference '0.0.1'.
Cloning into '/tmp/o369qi1w/upstream'...
Enter passphrase for key '/home/afilot/.ssh/id_ed25519_iti581_github':
remote: Enumerating objects: 30, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (27/27), done.
remote: Total 30 (delta 5), reused 23 (delta 1), pack-reused 0 (from 0)
Receiving objects: 100% (30/30), 33.92 KiB | 358.00 KiB/s, done.
Resolving deltas: 100% (5/5), done.
Exporting to archive: '/tmp/tmpj36yos6p/upstream-0.0.1.tar.gz'
md5: 2b1b42970291298f58f270a453eb719a

==> git-bloom-import-upstream /tmp/tmpj36yos6p/upstream-0.0.1.tar.gz  --release-version 0.0.1 --replace
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
Creating upstream branch.
Importing archive into upstream branch...
Creating tag: 'upstream/0.0.1'
I'm happy.  You should be too.

==> git-bloom-generate -y rosrelease humble --source upstream -i 1
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
Releasing package: ['apriltag_hitch_estimation']
Releasing package 'apriltag_hitch_estimation' for 'humble' to: 'release/humble/apriltag_hitch_estimation'

==> git-bloom-generate -y rosdebian --prefix release/humble humble -i 1 --os-name ubuntu
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
Generating source debs for the packages: ['apriltag_hitch_estimation']
Debian Incremental Version: 1
Debian Distributions: ['jammy']
Releasing for rosdistro: humble

Pre-verifying Debian dependency keys...
Running 'rosdep update'...
All keys are OK

Placing debian template files into 'debian/humble/apriltag_hitch_estimation' branch.
==> Placing templates files in the 'debian' folder.

####
#### Generating 'jammy' debian for package 'apriltag_hitch_estimation' at version '0.0.1-1'
####
Generating debian for jammy...
No historical releaser history, using current maintainer name and email for each versioned changelog entry.
No CHANGELOG.rst found for package 'apriltag_hitch_estimation'
Package 'apriltag-hitch-estimation' has dependencies:
Run Dependencies:
  rosdep key           => jammy key
  rclpy                => ['ros-humble-rclpy']
  apriltag_ros         => ['ros-humble-apriltag-ros']
  geometry_msgs        => ['ros-humble-geometry-msgs']
  sensor_msgs          => ['ros-humble-sensor-msgs']
  tf_transformations   => ['ros-humble-tf-transformations']
  tf2_ros              => ['ros-humble-tf2-ros']
Build and Build Tool Dependencies:
  rosdep key           => jammy key
  ament_cmake          => ['ros-humble-ament-cmake']
==> In place processing templates in 'debian' folder.
Expanding 'debian/compat.em' -> 'debian/compat'
Expanding 'debian/copyright.em' -> 'debian/copyright'
Expanding 'debian/source/options.em' -> 'debian/source/options'
Expanding 'debian/source/format.em' -> 'debian/source/format'
Expanding 'debian/gbp.conf.em' -> 'debian/gbp.conf'
Expanding 'debian/rules.em' -> 'debian/rules'
Expanding 'debian/control.em' -> 'debian/control'
Expanding 'debian/changelog.em' -> 'debian/changelog'
Creating tag: debian/ros-humble-apriltag-hitch-estimation_0.0.1-1_jammy
####
#### Successfully generated 'jammy' debian for package 'apriltag_hitch_estimation' at version '0.0.1-1'
####


==> git-bloom-generate -y rosdebian --prefix release/humble humble -i 1 --os-name debian --os-not-required
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
No platforms defined for os 'debian' in release file for the 'humble' distro. This os was not required; continuing without error.

==> git-bloom-generate -y rosrpm --prefix release/humble humble -i 1 --os-name fedora
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
No platforms defined for os 'fedora' in release file for the 'humble' distro.
Not performing RPM generation.

==> git-bloom-generate -y rosrpm --prefix release/humble humble -i 1 --os-name rhel
ROS Distro index file associate with commit 'de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e'
New ROS Distro index url: 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/index-v4.yaml'
Generating source RPMs for the packages: ['apriltag_hitch_estimation']
RPM Incremental Version: 1
RPM OS: rhel
RPM Distributions: ['8']
Releasing for rosdistro: humble

Pre-verifying RPM dependency keys...
Running 'rosdep update'...
All keys are OK

Placing RPM template files into 'rpm/humble/apriltag_hitch_estimation' branch.
==> Placing templates files in the 'rpm' folder.

####
#### Generating 'rhel 8' RPM for package 'apriltag_hitch_estimation' at version '0.0.1-1'
####
Generating RPM for rhel 8...
Package 'apriltag-hitch-estimation' has dependencies:
Run Dependencies:
  rosdep key           => rhel 8 key
  rclpy                => ['ros-humble-rclpy']
  apriltag_ros         => ['ros-humble-apriltag-ros']
  geometry_msgs        => ['ros-humble-geometry-msgs']
  sensor_msgs          => ['ros-humble-sensor-msgs']
  tf_transformations   => ['ros-humble-tf-transformations']
  tf2_ros              => ['ros-humble-tf2-ros']
Build and Build Tool Dependencies:
  rosdep key           => rhel 8 key
  ament_cmake          => ['ros-humble-ament-cmake']
==> In place processing templates in 'rpm' folder.
Expanding 'rpm/template.spec.em' -> 'rpm/template.spec'
Creating tag: rpm/ros-humble-apriltag-hitch-estimation-0.0.1-1_8
####
#### Successfully generated 'rhel 8' RPM for package 'apriltag_hitch_estimation' at version '0.0.1-1'
####





Tip: Check to ensure that the debian tags created have the same version as the upstream version you are releasing.
Everything went as expected, you should check that the new tags match your expectations, and then push to the release repo with:
  git push --all && git push --tags  # You might have to add --force to the second command if you are over-writing existing tags
✅  Released 'apriltag_hitch_estimation' using release track 'humble' successfully
==> git remote -v
origin    git@github.com:li9i/apriltag-hitch-estimation-release.git (fetch)
origin    git@github.com:li9i/apriltag-hitch-estimation-release.git (push)
Releasing complete, push to release repository?
Continue [Y/n]?
==> Pushing changes to release repository for 'apriltag_hitch_estimation'
==> git push --all
Enter passphrase for key '/home/afilot/.ssh/id_ed25519_iti581_github':
Enumerating objects: 142, done.
Counting objects: 100% (142/142), done.
Delta compression using up to 6 threads
Compressing objects: 100% (120/120), done.
Writing objects: 100% (142/142), 48.38 KiB | 5.38 MiB/s, done.
Total 142 (delta 33), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (33/33), done.
To github.com:li9i/apriltag-hitch-estimation-release.git
 * [new branch]      debian/humble/apriltag_hitch_estimation -> debian/humble/apriltag_hitch_estimation
 * [new branch]      debian/humble/jammy/apriltag_hitch_estimation -> debian/humble/jammy/apriltag_hitch_estimation
 * [new branch]      master -> master
 * [new branch]      patches/debian/humble/apriltag_hitch_estimation -> patches/debian/humble/apriltag_hitch_estimation
 * [new branch]      patches/debian/humble/jammy/apriltag_hitch_estimation -> patches/debian/humble/jammy/apriltag_hitch_estimation
 * [new branch]      patches/release/humble/apriltag_hitch_estimation -> patches/release/humble/apriltag_hitch_estimation
 * [new branch]      patches/rpm/humble/8/apriltag_hitch_estimation -> patches/rpm/humble/8/apriltag_hitch_estimation
 * [new branch]      patches/rpm/humble/apriltag_hitch_estimation -> patches/rpm/humble/apriltag_hitch_estimation
 * [new branch]      release/humble/apriltag_hitch_estimation -> release/humble/apriltag_hitch_estimation
 * [new branch]      rpm/humble/8/apriltag_hitch_estimation -> rpm/humble/8/apriltag_hitch_estimation
 * [new branch]      rpm/humble/apriltag_hitch_estimation -> rpm/humble/apriltag_hitch_estimation
 * [new branch]      upstream -> upstream
✅  Pushed changes successfully
==> Pushing tags to release repository for 'apriltag_hitch_estimation'
==> git push --tags
Enter passphrase for key '/home/afilot/.ssh/id_ed25519_iti581_github':
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:li9i/apriltag-hitch-estimation-release.git
 * [new tag]         debian/ros-humble-apriltag-hitch-estimation_0.0.1-1_jammy -> debian/ros-humble-apriltag-hitch-estimation_0.0.1-1_jammy
 * [new tag]         release/humble/apriltag_hitch_estimation/0.0.1-1 -> release/humble/apriltag_hitch_estimation/0.0.1-1
 * [new tag]         rpm/ros-humble-apriltag-hitch-estimation-0.0.1-1_8 -> rpm/ros-humble-apriltag-hitch-estimation-0.0.1-1_8
 * [new tag]         upstream/0.0.1 -> upstream/0.0.1
✅  Pushed tags successfully
==> Generating pull request to distro file located at 'https://raw.githubusercontent.com/ros/rosdistro/de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/humble/distribution.yaml'
Would you like to add documentation information for this repository? [Y/n]?
==> Looking for a doc entry for this repository in a different distribution...
No existing doc entries found for use as defaults.
Please enter your repository information for the doc generation job.
This information should point to the repository from which documentation should be generated.
VCS Type must be one of git, svn, hg, or bzr.
VCS type [git]:
VCS url [https://github.com/li9i/apriltag-hitch-estimation.git]:
VCS version must be a branch, tag, or commit, e.g. master or 0.1.0
VCS version [humble-devel]:
Would you like to add source information for this repository? [Y/n]?
==> Looking for a source entry for this repository in a different distribution...
No existing source entries found for use as defaults.
Please enter information which points to the active development branch for this repository.
This information is used to run continuous integration jobs and for developers to checkout from.
VCS Type must be one of git, svn, hg, or bzr.
VCS type [git]:
VCS url [https://github.com/li9i/apriltag-hitch-estimation.git]:
VCS version must be a branch, tag, or commit, e.g. master or 0.1.0
VCS version [humble-devel]:
Since you are on github we can add a job to run your tests on each pull request.If you would like to turn this on please see http://wiki.ros.org/buildfarm/Pull%20request%20testing for more information. There is more setup required to setup the hooks correctly.
Would you like to turn on pull request testing? [y/N]?
Would you like to add a maintenance status for this repository? [Y/n]?
Please enter a maintenance status.
Valid maintenance statuses:
- developed: active development is in progress
- maintained: no new development, but bug fixes and pull requests are addressed
- unmaintained: looking for new maintainer, bug fixes and pull requests will not be addressed
- end-of-life: should not be used, will disappear at some point
Status: maintained
You can also enter a status description.
This is usually reserved for giving a reason when a status is 'end-of-life'.
Status Description [press Enter for no change]:
Unified diff for the ROS distro file located at '/tmp/tmp65ueyc8e/apriltag_hitch_estimation-0.0.1-1.patch':
--- de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/humble/distribution.yaml
+++ de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e/humble/distribution.yaml
@@ -584,6 +584,21 @@
       url: https://github.com/ros-misc-utilities/apriltag_detector.git
       version: release
     status: developed
+  apriltag_hitch_estimation:
+    doc:
+      type: git
+      url: https://github.com/li9i/apriltag-hitch-estimation.git
+      version: humble-devel
+    release:
+      tags:
+        release: release/humble/{package}/{version}
+      url: https://github.com/li9i/apriltag-hitch-estimation-release.git
+      version: 0.0.1-1
+    source:
+      type: git
+      url: https://github.com/li9i/apriltag-hitch-estimation.git
+      version: humble-devel
+    status: maintained
   apriltag_mit:
     doc:
       type: git
==> Checking on GitHub for a fork to make the pull request from...
==> Using this fork to make a pull request from: li9i/rosdistro
==> Cloning li9i/rosdistro...
==> mkdir -p rosdistro
==> git init
Initialized empty Git repository in /tmp/2vgvkjy3/rosdistro/.git/
Pull Request Title: apriltag_hitch_estimation: 0.0.1-1 in 'humble/distribution.yaml' [bloom]
Pull Request Body :
Increasing version of package(s) in repository `apriltag_hitch_estimation` to `0.0.1-1`:

- upstream repository: https://github.com/li9i/apriltag-hitch-estimation.git
- release repository: https://github.com/li9i/apriltag-hitch-estimation-release.git
- distro file: `humble/distribution.yaml`
- bloom version: `0.13.0`
- previous version for package: `null`

Open a pull request from 'li9i/rosdistro:bloom-apriltag_hitch_estimation-0' into 'ros/rosdistro:master'?
Continue [Y/n]?
==> git checkout -b bloom-apriltag_hitch_estimation-0
Switched to a new branch 'bloom-apriltag_hitch_estimation-0'
==> Pulling latest rosdistro branch
remote: Enumerating objects: 230987, done.
remote: Counting objects: 100% (47/47), done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 230987 (delta 34), reused 24 (delta 20), pack-reused 230940 (from 2)
Receiving objects: 100% (230987/230987), 126.75 MiB | 39.53 MiB/s, done.
Resolving deltas: 100% (133579/133579), done.
From https://github.com/ros/rosdistro
 * branch                master     -> FETCH_HEAD
==> git reset --hard de9ea1cc7bfd1ebb2cc1c21b7269c6951b5ec54e
HEAD is now at de9ea1cc7 rosidl_typesupport_fastrtps: 3.9.3-1 in 'rolling/distribution.yaml' [bloom] (#48727)
==> Writing new distribution file: humble/distribution.yaml
==> git add humble/distribution.yaml
==> git commit -m "apriltag_hitch_estimation: 0.0.1-1 in 'humble/distribution.yaml' [bloom]"
[bloom-apriltag_hitch_estimation-0 5afdf9ea9] apriltag_hitch_estimation: 0.0.1-1 in 'humble/distribution.yaml' [bloom]
 1 file changed, 15 insertions(+)
==> Pushing changes to fork
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 6 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 487 bytes | 487.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
remote:
remote: Create a pull request for 'bloom-apriltag_hitch_estimation-0' on GitHub by visiting:
remote:      https://github.com/li9i/rosdistro/pull/new/bloom-apriltag_hitch_estimation-0
remote:
To https://github.com/li9i/rosdistro.git
 * [new branch]          bloom-apriltag_hitch_estimation-0 -> bloom-apriltag_hitch_estimation-0
✅  Pull request opened at: https://github.com/ros/rosdistro/pull/48735
```

### Next steps

If the pull request is merged then the package will appear at https://index.ros.org/.

> Once your pull request has been submitted, usually within one or two days, one of the maintainers of rosdistro will review and merge your Pull Request. If your package build is successful, in 24-48 hours your packages will become available in the ros-testing repository, where you can [test your pre-release binaries](https://docs.ros.org/en/humble/Installation/Testing.html).

> Approximately every two to four weeks, the distribution’s release manager manually synchronizes the contents of ros-testing into the main ROS repository. This is when your packages actually become available to the rest of the ROS community. To get updates on when the next synchronization (sync) is coming, subscribe to the [Packaging and Release Management Category on ROS Discourse](https://discourse.openrobotics.org/c/ros/release/16).

[source](https://docs.ros.org/en/humble/How-To-Guides/Releasing/First-Time-Release.html#next-steps)

#### Updating to new version

```bash
bloom-release --rosdistro humble --track humble hitch_estimation_apriltag_array --edit
```

And when you hit

```bash
Version:
  :{auto}
    This means the version will be guessed from the devel branch.
    This means that the devel branch must be set, the devel branch must exist,
    and there must be a valid package.xml in the upstream devel branch.
  :{ask}
    This means that the user will be prompted for the version each release.
    This also means that the upstream devel will be ignored.
  <version>
    This will be the version used.
    It must be updated for each new upstream version.
```

type the new version number, e.g.

```bash
  [':{auto}']: 0.0.2
```

---

# S

## Save map published at custom topic

```bash
# save from topic `my_custom_map` to file with name `my_map`
ros2 run nav2_map_server map_saver_cli -f my_map --ros-args -r /map:=/my_custom_map
```

or, thoroughly:

```bash
ros2 run nav2_map_server map_saver_cli -f $MAP_SAVE_FILEPATH -t MAP_TOPIC_TO_SAVE --occ 0.65 --free 0.196 --ros-args -p map_subscribe_transient_local:=true
```

---

# T

## `topic echo`

### Echo field of message

```bash
ros2 topic echo --once /stereo/right/image_rect --field header.stamp
```

### Throttle frequency

Echo every `10`th message in topic `/topic_name`

```bash
ros2 topic echo /topic_name | awk 'BEGIN{RS="---\n"} NR % 10 == 1 {print; printf "---\n"}'
```
