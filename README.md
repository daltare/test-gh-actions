## GitHub Actions Testing

### Shiny App Deployment

Use `usethis::use_github_action("shiny-deploy")`, following these instructions to test using GitHub Actions to deploy updates to a Shiny web application: <https://github.com/r-lib/actions/tree/v2/examples#shiny-app-deployment>

The deployed app is here: <https://daltare.shinyapps.io/test-app/>

This automatically deploys changes to this Shiny app whenever changes to the `app.R` file are pushed to this repository (i.e., not deploying from RStudio). This should ensure that the code shown here always matches what's shown in the deployed app.

Notes: In addition to setting up the shinyapps.io username/token/secret as as GitHub Secrets and setting up `renv` (all described in the instructions at the link above), I had to manually change a few things in the `.github/workflows/shiny-deploy.yaml` template file that was created by `usethis::use_github_action("shiny-deploy")`, including:

-   Add app name and account name (in the `APPNAME: your-app-name` and `ACCOUNT: your-account-name` lines)
-   Set app directory path in `rsconnect::deployApp()` function - this was done by adding an `APP_DIR` variable, and adding the `appDir = "${{ env.APP_DIR }}"` argument to `rsconnect::deployApp()`
-   Set `forceUpdate = TRUE` in `rsconnect::deployApp()` function (may not be strictly necessary)

### Lintr / Styler

-   Lint Project: <https://github.com/r-lib/actions/tree/v2/examples#lint-project-workflow>
-   Styler (maybe just for package?): <https://github.com/r-lib/actions/tree/v2/examples#style-package>
-   Styler - on pull request, with comment: <https://github.com/r-lib/actions/tree/v2/examples#commands-workflow>

### Quarto

-   <https://github.com/quarto-dev/quarto-actions>
