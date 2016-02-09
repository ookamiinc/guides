# Contributing

We love pull requests. If you have something you want to add or remove, please
open a new pull request.

## Getting Feedback

Since these are our guides, we want everyone at ookami to see them.

## Before Create Pull Request

You can create Pull Request to master or staging (if it exists).

1. Update Your Branch

   It's pretty likely that other changes to staging have happened while you were working. Go get them:

        git checkout staging
        git pull --rebase origin staging

   Reapply your patch on top of the latest changes:

        git checkout my_new_branch
        git rebase staging

2. Run `rake`

   Tests still pass? Change still seems reasonable to you? Then move on.

3. Run `rubocop -RD`

   Check your coding style.