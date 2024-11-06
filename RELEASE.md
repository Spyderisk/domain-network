# Domain Network Release Process

Very briefly, when a release needs to be made:

1. Put a git tag on a branch at the point corresponding to the release to be made
   1. Use `git tag` in your local client, e.g. `git tag <tagname>`
   2. Use `git push` to push the tag to origin (GitHub), e.g. `git push origin <tagname>`
2. In the GitHub interface, go to the ["Releases" page](https://github.com/Spyderisk/domain-network/releases) and [create a new release](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository)
3. The "Gradle Package Domain Model" workflow found in [`.github/workflows/gradle-publish.yml`](.github/workflows/gradle-publish.yml) will execute, convert from the CSV sources to an NQ file using [csv2nq](https://github.com/Spyderisk/domain-csv2nq), create a zip file and publish the artifact to GitHub Packages (a Maven repository). See the file for details.

If it is required, the Gradle Package Domain Model workflow can be run manually on any branch using this page: <https://github.com/Spyderisk/domain-network/actions/workflows/gradle-publish.yml>