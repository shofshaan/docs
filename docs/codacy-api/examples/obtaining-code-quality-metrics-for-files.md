---
description: Example of how to obtain code quality metrics for files in a repository programmatically using the Codacy API endpoint listFiles.
---

# Obtaining code quality metrics for files

To obtain the code quality information for your files in a flexible way, use the Codacy API endpoint [listFiles](https://app.codacy.com/api/api-docs#listfiles).

For example, if you're managing your source code using a monorepo strategy you may want to generate separate code quality reports for the subset of files that belong to each component or project in your repository.

## Example: Obtaining code quality metrics for a subdirectory of your repository

This example exports the grade, total issues, complexity, coverage, and duplication in CSV format for all files in the directory `src/router` of the GitHub repository `codacy/website`.

The example script:

1.  Defines the [account API token](../api-tokens.md#account-api-tokens) used to authenticate on the Codacy API.
1.  Calls the Codacy API endpoint [listFiles](https://app.codacy.com/api/api-docs#listfiles) to retrieve the code quality metrics, filtering the results by files that include `src/router/` in the path.
1.  Uses [jq](https://github.com/stedolan/jq) to select only the necessary data fields and convert the results to the CSV format.

```bash
CODACY_API_TOKEN="<your account API token>"
GIT_PROVIDER="<your Git provider>" # gh, ghe, gl, gle, bb, or bbe
ORGANIZATION="<your organization name>"
REPOSITORY="<your repository name>"

curl -X GET "https://app.codacy.com/api/v3/organizations/$GIT_PROVIDER/$ORGANIZATION/repositories/$REPOSITORY/files?search=src/router/" \
     -H "api-token: $CODACY_API_TOKEN" \
| jq -r ".data[] | [.path, .gradeLetter, .totalIssues, .complexity, .coverage, .duplication] | @csv"
```

Example output:

```text
"src/components/router/index.ts","A",0,8,70,0
"src/components/router/Link.tsx","A",0,5,100,0
"src/components/router/Redirect.tsx","B",0,2,14,0
"src/components/router/routes/account.ts","A",0,0,100,0
[...]
```

{% include-markdown "../../assets/includes/api-example-pagination-important.md" %}

## See also

-   [Which metrics does Codacy calculate?](../../faq/code-analysis/which-metrics-does-codacy-calculate.md)
-   [Files page](../../repositories/files.md)
