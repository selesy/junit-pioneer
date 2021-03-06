# Contributing

The following guidelines were chosen very deliberately to make sure the project benefits from contributions.
This is true for such diverse areas as a firm legal foundation or a sensible and helpful commit history.

* [Contributor License Agreement](#junit-pioneer-contributor-license-agreement)
* [Fixing Bugs, Developing Features](#fixing-bugs-developing-features)
	* [Branching Strategy](#branching-strategy)
	* [Commits](#commits)
	* [Pull Requests](#pull-requests)
	* [Merging](#merging)
* [Adapting to Upstream Changes](#adapting-to-upstream-changes)
	* [Small, Unavoidable Changes](#small-unavoidable-changes)
	* [Unavoidable Changes](#unavoidable-changes)
	* [Optional Changes](#optional-changes)
* [Publishing](#publishing)
	* [Credentials](#credentials)
	* [Snapshots](#snapshots)
	* [Releases](#releases)

The guidelines apply to maintainers as well as contributors!


## JUnit Pioneer Contributor License Agreement

**Project License:** [Eclipse Public License v1.0](LICENSE.md)

* You will only submit contributions where you have authored 100% of the content.
* You will only submit contributions to which you have the necessary rights.
  This means that if you are employed you have received the necessary permissions from your employer to make the contributions.
* Whatever content you contribute will be provided under the project license(s).


## Fixing Bugs, Developing Features

This section governs how features or bug fixes are developed.
See the next section for how to adapt to upstream changes.

### Branching Strategy

By default development happens in branches, which are merged via pull requests.
Special cases, like fixing problems with the CI pipeline, are of course exempt from this guideline.

Please make sure to give branches a meaningful name!
As an example, the one creating this documentation was called `branching-merging-documentation`.

### Commits

While it is nice to have each individual commit pass the build, this is not a requirement - it is the contributor's branch to play on.

As a general rule, the style and formatting of commit messages should follow the [guidelines for good Git commit messages](http://chris.beams.io/posts/git-commit/).
Because of the noise it generates on the issue, please do _not_ mention the issue number in the message.

### Pull Requests

Pull requests are used to discuss a concrete solution, not the motivation nor requirements for it.
As such there should be at least one issue a pull request relates to.
At the same time it should be focused so it should usually not relate to more than one issue (although that can occasionally happen).
Please mention all issues in the request's body, possibly using [closing keywords](https://help.github.com/articles/closing-issues-via-commit-messages/) like `closes`, `fixes` (for bugs only), or `resolves`.

The [pull requests template](.github/PULL_REQUEST_TEMPLATE.md) contains a footer that must not be edited or removed.

To enforce the [branching strategy](#branching-strategy) pull requests from `master` will be closed.

### Merging

A pull request is accepted by squashing the commits and fast-forwarding master, making each bug fix or feature appear atomically on master.
This can be achieved with GitHub's [_squash and merge_](https://help.github.com/articles/about-pull-request-merges/#squash-and-merge-your-pull-request-commits) feature.

To make the single commit expressive its message must be detailed and [good]((http://chris.beams.io/posts/git-commit/)) (really, read that post!).
Furthermore, it must follow this structure:

```
<Action> (<issues> / <pull-request>)

<Body>
```

`<Action>` should succinctly describe what the PR does in good Git style.
It is followed by a comma-separated list of all related issues, a dash, and the pull request (to make all of them easy to find from a look at the log).
Ideally, this title line should not exceed 50 characters - 70 is the absolute maximum.

`<Body>` should outline the problem the pull request was solving - it should focus on _why_ the code was written not on _how_ it works.
This can usually be a summary of the issue description and discussion as well as commit messages.
Markdown syntax can be used and lines should usually not exceed 70 characters (exceptions are possible, e.g. to include stack traces).

Once a pull request is ready to be merged, the contributor will be asked to propose an action and body for the squashed commit and the maintainer will refine them when merging.

As an example, the squashed commit that created this documentation had the following message:

```
Document branching and merging (#30, #31 / #40)

To make sure the project has a sensible and helpful commit history and
interacts well with GitHub's features the strategy used for branching,
commit messages, and merging must be chosen carefully and deliberately.
The following aspects are particularly important:

 - a history that is accessible, detailed, and of high quality
 - backlinks from commits to isses and PRs without creating
   "notification noise" in the web interface
 - reduce necessity for maintainers policing contributors' commit
   messages

The chosen approach to squash and merge fulfills all of them except
the detailed history, which will be more coarse than with merge commits
or fast-forward merges. This was deemed acceptable in order to achieve
the other points, particularly the last one.
```


## Adapting to Upstream Changes

JUnit Pioneer has an uncharacteristically strong relationship to the JUnit 5 project (often called _upstream_).
It not only depends on it, it also uses its internal APIs, copies source code that is not released in any artifact, mimics code style, unit testing, build and CI setup, and more.
As such it will frequently have to adapt to upstream changes, so it makes sense to provision for that in the development strategy.

### Small, Unavoidable Changes

Some upstream changes (like [this one](https://github.com/junit-team/junit5/issues/793#issuecomment-294377755)) can cause compile or other blatantly obvious errors, for which only one fix exists and no discussion is required.
In such cases:

* no issue needs to be created
* pull requests are optional and the maintainer might opt to commit directly on `master` (please make sure it builds!)
* commit messages must be stellar:
	* structured and worded as defined above
	* reference to the upstream change (issue and pull request)

### Unavoidable Changes

Some breaking upstream changes might require solutions that are not as obvious as above.
In such cases:

* no issue needs to be created
* a pull request should be opened for review, in which case everything said earlier about commits, PRs, and merges in regular development holds
* the final commit message should reference the upstream change (issue and pull request)

### Optional Changes

In case an upstream change does not _require_ a fix, a discussion is warranted.
An issue should be opened and the procedure follows regular development as described earlier.
The final commit message should reference the upstream change (issue and pull request).


## Publishing

Because `gradle publish` tries to execute all publications (e.g. snapshots _and_ releases), it is deactivated.
Instead use the more specific tasks described below.

### Credentials

For any release you need the properties `mavenUserName` and `mavenPassword` to contain your credentials for the Sonatype repositories.
One way to define them is [a Gradle properties file](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_properties_and_system_properties).
For that approach, create a file `gradle.properties` in `GRADLE_USER_HOME` (which defaults to `USER_HOME/.gradle`) with the following content:

```
mavenUserName=...
mavenPassword=...
```

Another way are command line flags (but note that these add sensitive information to your terminal history):

```
gradle publish -PmavenUserName=... -PmavenPassword=...
```

Furthermore, non-snapshot releases require signed artifacts, for which the following properties have to be defined:

```
signing.keyId= # last eight digits of key ID
signing.password= # password for the key
signing.secretKeyRingFile= # path to local secring.gpg
```

For details on these have a look at the [Gradle Signing Plugin's documentation](https://docs.gradle.org/current/userguide/signing_plugin.html#sec:signatory_credentials).

### Snapshots

To publish a snapshot version simply execute `gradle publishSnapshot`.
This will fail if the current project version does not end in `-SNAPSHOT`.

### Releases

To publish a new release, go through the following check list:

* you should be operating on the `master` branch and have no uncommitted changes
* the version must not end in `-SNAPSHOT`
* no dependency on snapshot versions

If everything's in order, execute `gradle publishRelease`.

After a successful release do the following:

* tag the current HEAD as a release and upload all artifacts to GitHub
* go to the next snapshot version and commit the change
