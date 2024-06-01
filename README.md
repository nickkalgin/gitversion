# GitVersion + GitHub Actions usage

The project demonstrates automatic versioning of an application by [SemVer](https://semver.org) that is integrated into
its build lifecycle.

The project contains a simple Java application and a CI/CD pipeline created with a
[GitHub Actions](https://docs.github.com/en/actions/quickstart) workflow according to followed build lifecycle
principles. The pipeline deploys the application to
[GitHub Packages](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-apache-maven-registry).

## The build lifecycle principles

- Any branch must be built automatically. The build receives an identifier that shows its branch and commit.
- The main branch must be automatically built and deployed to the Package Registry as SNAPSHOT. A snapshot receives the
  next version number automatically.
- The main or a release branch might be manually built and deployed to the Package Registry as RELEASE. Release receives
  the next version number automatically.
- Each release automatically gets a corresponding tag in the Git repository. The tag holds a version number assigned to
  the release.

## CI/CD

The workflow implements described principles as a set of jobs that:

1. Calculate the next version number.
2. Build an artifact and assign the version to it.
3. Deploy an artifact to the Package Registry.
4. Save a release number in the Git repository by creating a tag.

## Branch naming conventions

There are two types of branches that can be used with this approach:

1. A **feature** branch - contains developing features that will be merged into the main release branch and deleted.
2. A **release** branch - one or many branches that receive features and fixes (main branch) or just fixes (other
   release branches).

A feature branch can have any name except that ends with the suffix `.x`. This suffix is used for release branches only.

A release branch must have a name in the format that matches RegExp `/^\d+\.\d+\.x$/`. The branch must be created from
the same commit in the main branch that is pointed by Git release tag. For example, a branch for release `1.0.0` must be
started from the tag `1.0.0` and have the name `1.0.x`. It helps GitVersion to support the automatic assignment of a
version number for each release from the `1.0.x` branch. They will continuously get `1.0.1`, `1.0.2`, and so on during
the
build.

## Building major release

1. Increment the property `next-version` in the `GitVersion.yml` file.
2. Commit the changes to the main branch.
3. Wait until the pipeline for the main branch completes successfully.
4. Run the deployment release `CI` workflow from the main
   branch [manually](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow) to publish the
   release.

## Building minor release

1. Commit the changes in the source code of the main branch.
2. Wait until the pipeline for the main branch completes successfully.
3. Run the deployment release `CI` workflow from the main
   branch [manually](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow) to publish the
   release.

## Building patch release

1. Define the release number which is going to get a patch.
2. Use an existing release branch or create a new one if it hasn't been created for the release yet.
3. Backport a bug fix from the main branch to the release branch.
4. Wait until the pipeline for the branch completes successfully.
5. Run the deployment release `CI` workflow from the release
   branch [manually](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow) to publish the
   patch. 
