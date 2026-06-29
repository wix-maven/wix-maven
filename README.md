# wix-maven

For releasing WiX into the OSS Sonatype Maven repository through GitHub Actions.  
A set of projects providing various packaging to target Maven related builds.

For WiX 4 native NAR packaging:

`mvn clean deploy -Pnar`

The `nar` profile downloads the required WiX 4 native NuGet packages during `generate-sources`, so no separate prefetch step is required for `dutil-vc14`, `balutil-vc14`, `bextutil-vc14`, or `wcautil-vc14`.

## Release process

The `maven-release-plugin` can be used to version bump all projects in the reactor, release tag creation, and next snapshot update can be driven from Maven.

Prerequisites:

* Push access to the GitHub repository.
* A Git remote that can push tags for `git@github.com:wix-maven/wix-maven.git` or an equivalent push-capable URL.
* GitHub Actions repository secrets configured for Maven Central publishing and GPG signing.

Typical release flow:

1. Start from a clean `master` branch with no uncommitted changes.
2. Create the release commit and Git tag, then push both back to GitHub:

   `mvn -B release:clean release:prepare -Pnar -DreleaseVersion=4 -DdevelopmentVersion=5-SNAPSHOT`

3. Let GitHub Actions publish the release when the new `v<releaseVersion>` tag is pushed.

What the plugin does:

* Updates all reactor modules to the release version.
* Commits the release POM changes.
* Creates and pushes a Git tag named `v<releaseVersion>`.
* Bumps the reactor back to the next snapshot version and commits that change.
* Runs a non-publishing verification build if `release:perform` is invoked locally.

GitHub Actions note:

* The GitHub publish workflow now runs on pushes to `master` and on tags matching `v*`.
* `release:prepare` is the normal release entry point because the pushed tag triggers the publish workflow automatically.
* GitHub Actions is the only supported release publisher for Maven Central.

## Options

### Optional NAR packaging (com.github.maven-nar:3.10.1)

-Pnar

The WiX 4 vc14 native artifacts (`dutil-vc14`, `balutil-vc14`, `bextutil-vc14`, `wcautil-vc14`) stage headers and libraries from the corresponding `WixToolset.*` NuGet packages during `generate-sources`.

For integration tests requires

* local install of msvc compiler on path
* running in x86 Native Tools command prompt
* Visual Studio linker support DLLs such as `mspdbcore.dll` available on `PATH` via that toolchain environment
