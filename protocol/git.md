# Git Protocol

A guide for programming within version control.

We use [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html)

Please use American English. See [a list of American and British English spelling differences here](http://en.wikipedia.org/wiki/American_and_British_English_spelling_differences).

## Maintain a Repo

* Avoid including files in source control that are specific to your
  development machine or process.
* Delete local and remote feature branches after merging.
* Perform work in a feature branch.
* Rebase frequently to incorporate upstream changes.
* Use a [pull request] for code reviews.

[pull request]: https://help.github.com/articles/using-pull-requests/

## Write a Feature

Create a local feature branch based off master or staging (if it exists).

    git checkout master
    git pull
    git checkout -b <branch-name>

Rebase frequently to incorporate upstream changes.

    git fetch origin
    git rebase origin/master

Resolve conflicts. When feature is complete and tests pass, stage the changes.

    git add --all

When you've staged the changes, commit them.

    git status
    git commit --verbose

Write a [good commit message]. Example format:

    Present-tense summary under 50 characters

    * More information about commit (under 72 characters).
    * More information about commit (under 72 characters).

    http://project.management-system.com/ticket/123

If you've created more than one commit, use a rebase to squash them into
cohesive commits with good messages:

    git rebase -i origin/master

Share your branch.

    git push origin <branch-name>

Submit a [GitHub pull request].

Ask for a code review in the project's chat room.

[good commit message]: http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
[GitHub pull request]: https://help.github.com/articles/using-pull-requests/

### Branch name

- Use lowercase
- Use dash not underscore between words
- Use descriptive name what you are going to do

## Review Code

A team member other than the author reviews the pull request. They follow
[Code Review](../code_review.md) guidelines to avoid
miscommunication.

They make comments and ask questions directly on lines of code in the GitHub
web interface or in the project's chat room.

For changes which they can make themselves, they check out the branch.

    git checkout <branch-name>
    git diff staging/master..HEAD

They make small changes right in the branch, test the feature on their machine,
run tests, commit, and push.

When satisfied, they comment on the pull request `Ready to merge.`
After Approval, you can merge by yourself if you have the right to merge.

## Merge

Rebase interactively. Squash commits like "Fix whitespace" into one or a
small number of valuable commit(s). Edit commit messages to reveal intent. Run
tests.

    git fetch origin
    git rebase -i origin/master

Force push your branch. This allows GitHub to automatically close your pull
request and mark it as merged when your commit(s) are pushed to master. It also
 makes it possible to [find the pull request] that brought in your changes.

    git push --force origin <branch-name>

View a list of new commits. View changed files. Merge branch into master.

    git log origin/master..<branch-name>
    git diff --stat origin/master

Merge branch into master by clicking "Merge pull request" button on your Pull Request page, or using hub commands from your terminal.

Then, make your local master up-to-date.

    git checkout master
    git pull

Delete your remote feature branch.

    git push origin --delete <branch-name>

Delete your local feature branch.

    git branch --delete <branch-name>

[find the pull request]: http://stackoverflow.com/a/17819027
