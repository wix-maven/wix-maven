# wix-maven

For releasing WiX into the OSS Sonatype Maven repository through GitHub Actions.  
A set of projects providing various packaging to target Maven related builds.

mvn clean  
mvn -N generate-sources -Pdownload  
mvn deploy -Pnar

## Release process

The `maven-release-plugin` can be used to version bump all projects in the reactor, release tag creation, and next snapshot update can be driven from Maven.

Prerequisites:

* Push access to the GitHub repository.
* A Git remote that can push tags for `git@github.com:wix-maven/wix-maven.git` or an equivalent push-capable URL.
* GitHub Actions repository secrets configured for Maven Central publishing and GPG signing.
* The WiX binary zip files present under `src/download`. Refresh them first with `mvn -N generate-sources -Pdownload` if needed.

Typical release flow:

1. Start from a clean `master` branch with no uncommitted changes.
2. Refresh the downloaded WiX artifacts if the release needs newer source zips:

   `mvn -N generate-sources -Pdownload`

3. Create the release commit and Git tag, then push both back to GitHub:

   `mvn -B release:clean release:prepare -DreleaseVersion=3.14.1 -DdevelopmentVersion=3.14.2-SNAPSHOT`

4. Let GitHub Actions publish the release when the new `v<releaseVersion>` tag is pushed.

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

### Download

-Pdownload -N  
Should be run with the -N option to only run on the aggregate build, child projects reference the download location in the parent project.
Download from GitHub <https://github.com/wixtoolset/wix3/releases> is partly automated, version in URL is not automatically updated to match mvn build.

### Optional NAR packaging (com.github.maven-nar:3.10.1)

-Pnar

For integration tests requires

* local install of msvc compiler on path
* running in x86 Native Tools command prompt
