---
layout: "github"
page_title: "GitHub: github_repository"
description: |-
  Creates and manages repositories within GitHub organizations or personal accounts
---

# github_repository

This resource allows you to create and manage repositories within your
GitHub organization or personal account.

## Example Usage

```hcl
resource "github_repository" "example" {
  name        = "example"
  description = "My awesome codebase"

  visibility = "public"

  template {
    owner                = "github"
    repository           = "terraform-module-template"
    include_all_branches = true
  }
}
```

## Example Usage with GitHub Pages Enabled

```hcl
resource "github_repository" "example" {
  name        = "example"
  description = "My awesome web page"

  private = false

  pages {
    source {
      branch = "master"
      path   = "/docs"
    }
  }
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the repository.

* `description` - (Optional) A description of the repository.

* `homepage_url` - (Optional) URL of a page describing the project.

* `private` - (Optional) Set to `true` to create a private repository.
  Repositories are created as public (e.g. open source) by default.

* `visibility` - (Optional) Can be `public` or `private`. If your organization is associated with an enterprise account using GitHub Enterprise Cloud or GitHub Enterprise Server 2.20+, visibility can also be `internal`. The `visibility` parameter overrides the `private` parameter.

* `has_issues` - (Optional) Set to `true` to enable the GitHub Issues features
  on the repository.

* `has_projects` - (Optional) Set to `true` to enable the GitHub Projects features on the repository. Per the GitHub [documentation](https://developer.github.com/v3/repos/#create) when in an organization that has disabled repository projects it will default to `false` and will otherwise default to `true`. If you specify `true` when it has been disabled it will return an error.

* `has_wiki` - (Optional) Set to `true` to enable the GitHub Wiki features on
  the repository.

* `is_template` - (Optional) Set to `true` to tell GitHub that this is a template repository.

* `allow_merge_commit` - (Optional) Set to `false` to disable merge commits on the repository.

* `allow_squash_merge` - (Optional) Set to `false` to disable squash merges on the repository.

* `allow_rebase_merge` - (Optional) Set to `false` to disable rebase merges on the repository.

* `allow_auto_merge` - (Optional) Set to `true` to allow auto-merging pull requests on the repository.

* `squash_merge_commit_title` - (Optional) Can be `PR_TITLE` or `COMMIT_OR_PR_TITLE` for a default squash merge commit title.

* `squash_merge_commit_message` - (Optional) Can be `PR_BODY`, `COMMIT_MESSAGES`, or `BLANK` for a default squash merge commit message.

* `merge_commit_title` - Can be `PR_TITLE` or `MERGE_MESSAGE` for a default merge commit title.

* `merge_commit_message` - Can be `PR_BODY`, `PR_TITLE`, or `BLANK` for a default merge commit message.

* `delete_branch_on_merge` - (Optional) Automatically delete head branch after a pull request is merged. Defaults to `false`.

* `has_downloads` - (Optional) Set to `true` to enable the (deprecated) downloads features on the repository.

* `auto_init` - (Optional) Set to `true` to produce an initial commit in the repository.

* `gitignore_template` - (Optional) Use the [name of the template](https://github.com/github/gitignore) without the extension. For example, "Haskell".

* `license_template` - (Optional) Use the [name of the template](https://github.com/github/choosealicense.com/tree/gh-pages/_licenses) without the extension. For example, "mit" or "mpl-2.0".

* `default_branch` - (Optional) (Deprecated: Use `github_branch_default` resource instead) The name of the default branch of the repository. **NOTE:** This can only be set after a repository has already been created,
and after a correct reference has been created for the target branch inside the repository. This means a user will have to omit this parameter from the
initial repository creation and create the target branch inside of the repository prior to setting this attribute.

* `archived` - (Optional) Specifies if the repository should be archived. Defaults to `false`. **NOTE** Currently, the API does not support unarchiving.

* `archive_on_destroy` - (Optional) Set to `true` to archive the repository instead of deleting on destroy.

* `pages` - (Optional) The repository's GitHub Pages configuration. See [GitHub Pages Configuration](#github-pages-configuration) below for details.

* `security_and_analysis` - (Optional) The repository's [security and analysis](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-security-and-analysis-settings-for-your-repository) configuration. See [Security and Analysis Configuration](#security-and-analysis-configuration) below for details.

* `topics` - (Optional) The list of topics of the repository.

* `template` - (Optional) Use a template repository to create this resource. See [Template Repositories](#template-repositories) below for details.

* `vulnerability_alerts` (Optional) - Set to `true` to enable security alerts for vulnerable dependencies. Enabling requires alerts to be enabled on the owner level. (Note for importing: GitHub enables the alerts on public repos but disables them on private repos by default.) See [GitHub Documentation](https://help.github.com/en/github/managing-security-vulnerabilities/about-security-alerts-for-vulnerable-dependencies) for details. Note that vulnerability alerts have not been successfully tested on any GitHub Enterprise instance and may be unavailable in those settings.

* `ignore_vulnerability_alerts_during_read` (Optional) - Set to `true` to not call the vulnerability alerts endpoint so the resource can also be used without admin permissions during read.

* `allow_update_branch` (Optional) - Set to `true` to always suggest updating pull request branches.

### GitHub Pages Configuration

The `pages` block supports the following:

* `source` - (Required) The source branch and directory for the rendered Pages site. See [GitHub Pages Source](#github-pages-source) below for details.

* `cname` - (Optional) The custom domain for the repository. This can only be set after the repository has been created.

#### GitHub Pages Source ####

The `source` block supports the following:

* `branch` - (Required) The repository branch used to publish the site's source files. (i.e. `main` or `gh-pages`.

* `path` - (Optional) The repository directory from which the site publishes (Default: `/`).

### Security and Analysis Configuration

The `security_and_analysis` block supports the following:

* `advanced_security` - (Required) The advanced security configuration for the repository. See [Advanced Security Configuration](#advanced-security-configuration) below for details.

* `secret_scanning` - (Required) The secret scanning configuration for the repository. See [Secret Scanning Configuration](#secret-scanning-configuration) below for details.

* `secret_scanning_push_protection` - (Required) The secret scanning push protection configuration for the repository. See [Secret Scanning Push Protection Configuration](#secret-scanning-push-protection-configuration) below for details.

#### Advanced Security Configuration ####

The `advanced_security` block supports the following:

* `status` - (Required) Set to `enabled` to enable advanced security features on the repository. Can be `enabled` or `disabled`.

#### Secret Scanning Configuration ####

* `status` - (Required) Set to `enabled` to enable secret scanning on the repository. Can be `enabled` or `disabled`.

#### Secret Scanning Push Protection Configuration ####

* `status` - (Required) Set to `enabled` to enable secret scanning push protection on the repository. Can be `enabled` or `disabled`.

### Template Repositories

`template` supports the following arguments:

* `owner`: The GitHub organization or user the template repository is owned by.
* `repository`: The name of the template repository.
* `include_all_branches`: Whether the new repository should include all the branches from the template repository (defaults to false, which includes only the default branch from the template).

## Attributes Reference

The following additional attributes are exported:

* `full_name` - A string of the form "orgname/reponame".

* `html_url` - URL to the repository on the web.

* `ssh_clone_url` - URL that can be provided to `git clone` to clone the repository via SSH.

* `http_clone_url` - URL that can be provided to `git clone` to clone the repository via HTTPS.

* `git_clone_url` - URL that can be provided to `git clone` to clone the repository anonymously via the git protocol.

* `svn_url` - URL that can be provided to `svn checkout` to check out the repository via GitHub's Subversion protocol emulation.

* `node_id` - GraphQL global node id for use with v4 API

* `repo_id` - GitHub ID for the repository

* `pages` - The block consisting of the repository's GitHub Pages configuration with the following additional attributes:
 * `custom_404` - Whether the rendered GitHub Pages site has a custom 404 page.
 * `html_url` - The absolute URL (including scheme) of the rendered GitHub Pages site e.g. `https://username.github.io`.
 * `status` - The GitHub Pages site's build status e.g. `building` or `built`.

## Import

Repositories can be imported using the `name`, e.g.

```
$ terraform import github_repository.terraform terraform
```
