# GitHub Actions Testing

This repository has some examples used to test different GitHub Actions.

Note that this project uses the [`renv`](https://rstudio.github.io/renv/index.html) R package, which is required for the Shiny App Deployment action (the `renv` package creates the `renv.lock` file, i.e. the lockfile). That means the developer will need to do things like call `renv::install()` to add packages, [`renv::update()`](https://rstudio.github.io/renv/reference/update.html) to update package versions, and `renv::snapshot()` after packages are added or updated (which will record the packages and their sources in the lockfile), and collaborators can call `renv::restore()` (to get the specific package versions recorded in the lockfile). The documentation notes that if you're making major changes to a project that you haven't worked on for a while, it's often a good idea to start with an [`renv::update()`](https://rstudio.github.io/renv/reference/update.html) before making any changes to the code. For more information, see [Introduction to renv](https://rstudio.github.io/renv/articles/renv.html).

------------------------------------------------------------------------

## Shiny App Deployment

This is a test/example of how to use GitHub Actions to deploy updates to a Shiny web application. To set up this action, run the command: `usethis::use_github_action("shiny-deploy")`, and follow these instructions: <https://github.com/r-lib/actions/tree/v2/examples#shiny-app-deployment>

This automatically deploys the Shiny App in this repository (whose source code is in the `test-app/app.R` file) whenever any changes are pushed to the `test-app` directory (the deployed app is [here](https://daltare.shinyapps.io/test-app/)). In other words, the app is not manually deployed/published by the user from RStudio. This should ensure that the code shown in this repository always matches what's shown in the deployed app.

Note that:

-   The GitHub Action will run either (1) when there are changes pushed directly to the `main` branch or (2) whenever a pull request is accepted into the `main` branch (in both cases, it will only run if files in the `test-app` directory are changed). It will not run when changes are pushed to a different branch.
-   The GitHub Action will only run when there are changes to any file in the `test-app` directory in the `main` branch (not just when the `app.R` file changes). Note that you can also set up the action to run whenever there are changes to any file in the `main` branch (not just the `test-app` directory) by removing the line `paths: ['test-app/**']` from the `shiny-deploy.yaml` file (in the `.github/workflows/` directory).
-   Setting up this action requires use of the [`renv`](https://rstudio.github.io/renv/index.html) R package. See above for more information about how to work with that package.

### Shiny App Deployment Notes

In addition to setting up the shinyapps.io username/token/secret as as GitHub Secrets and setting up `renv` (all described in the instructions at the link above), I had to manually change a few things in the `.github/workflows/shiny-deploy.yaml` template file that was created by `usethis::use_github_action("shiny-deploy")`, including:

-   Add app name and account name (in the `APPNAME: your-app-name` and `ACCOUNT: your-account-name` lines)
-   Set app directory path in `rsconnect::deployApp()` function - this was done by adding an `APP_DIR` variable, and adding the `appDir = "${{ env.APP_DIR }}"` argument to `rsconnect::deployApp()`
-   Add `paths: ['test-app/**']` in the header info (under the `on:` part), so that the action is only run when changes are made to files in the directory that hold the application files (`test-app`), rather than running the deployment action whenever any file in the repository is changed (note that this is not required, and may not always be the best course of action)
-   Set `forceUpdate = TRUE` in `rsconnect::deployApp()` function (may not be strictly necessary)

To find examples of how other users on GitHub set up their `shiny-deploy.yaml` file, use the following search on GitHub (and modify as needed): `path:shiny-deploy.yaml` (can also add search details like `user:`, `org:`, etc.)

------------------------------------------------------------------------

## Lintr / Styler

-   Lint Project: <https://github.com/r-lib/actions/tree/v2/examples#lint-project-workflow>
-   Styler (maybe just for package?): <https://github.com/r-lib/actions/tree/v2/examples#style-package>
-   Styler - on pull request, with comment: <https://github.com/r-lib/actions/tree/v2/examples#commands-workflow>

------------------------------------------------------------------------

## Quarto

Followed instructions on [this page](https://quarto.org/docs/publishing/github-pages.html), as follows:

-   Start [here](https://quarto.org/docs/publishing/github-pages.html#publish-command) to set up the `gh-pages` branch in Git / GitHub and format `.gitignore` to ignore rendered directories
-   Then follow instructions [here](https://quarto.org/docs/publishing/github-pages.html#github-action) to:
    -   [Freeze computations](https://quarto.org/docs/publishing/github-pages.html#freezing-computations) -- only needed if your document(s) includes computation (e.g. with R or python code) that you want to execute locally rather that via the GitHub Action (e.g., if the computations are extensive, have external dependencies or side effects, etc.)
    -   Set up GitHub [Publish Action](https://quarto.org/docs/publishing/github-pages.html#publish-action), including:
        -   Publish manually (once)
        -   Add the `.github/workflows/publish.yml` file

Note that for this example I modified the `.github/workflows/publish.yml` file as follows (you won't need to do this if your Quarto project is at the top level of your repository):

-   Added `paths: ['test-doc/**']` in the header info (under the `on:` section), so that the GitHub Action only runs when changes are made to files in the `test-doc` directory (and not other files in the repository) (note that this isn't strictly necessary, it just reduces un-needed runs of the GitHub Action)
-   Added `path: test-doc` under the `Render and Publish` job section, to indicate the subdirectory where the quarto project is published from

More information on Quarto-related GitHub Actions is available here: <https://github.com/quarto-dev/quarto-actions>
