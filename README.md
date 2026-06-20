# wix-maven

For releasing WiX into the OSS Sonatype Maven repository.  
A set of projects providing various packaging to target Maven related builds.

mvn clean  
mvn -N generate-sources -Pdownload  
mvn deploy -Pnar

## Options
### Download
-Pdownload -N  
Should be run with the -N option to only run on the aggregate build, child projects reference the download location in the parent project.
Download from Github https://github.com/wixtoolset/wix3/releases is partly automated, version in URL is not automatically updated to match mvn build.
 
### Optional NAR packaging (com.github.maven-nar::3.0.0)
-Pnar

For integration tests requires
 *  local install of msvc compiler on path
 *  running  in x86 Native Tools command prompt
