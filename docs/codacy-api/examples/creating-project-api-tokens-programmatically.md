---
description: Example of how to create new project API tokens for all repositories in an organization using the Codacy API endpoint createRepositoryApiToken.
---


# Creating project API tokens programmatically

To create new [project API tokens](../api-tokens.md) for your Codacy repositories programmatically, use the Codacy API endpoint [createRepositoryApiToken](https://app.codacy.com/api/api-docs#createrepositoryapitoken).

For example, if you're [setting up coverage](../../coverage-reporter/index.md) for all your repositories and prefer not to use a single account API token that grants the same permissions as an administrator, you need to create an individual project API token for each repository.

## Example: Creating project API tokens for all repositories in an organization

This example creates new project API tokens for all the repositories in an organization and outputs a comma-separated list of repository names and corresponding token strings.

The example script:

1.  Defines the [account API token](../api-tokens.md#account-api-tokens) used to authenticate on the Codacy API, the Git provider, and the organization name.
1.  Calls the Codacy API endpoint [listOrganizationRepositories](https://api.codacy.com/api/api-docs#listorganizationrepositories) to retrieve the list of repositories in the organization.
1.  Uses [jq](https://github.com/stedolan/jq) to select only the name of the repositories.
1.  Asks for confirmation from the user before making any changes.
1.  For each repository, calls the Codacy API endpoint [createRepositoryApiToken](https://app.codacy.com/api/api-docs#createrepositoryapitoken) to create a new project API token and uses jq to obtain only the created token string.
1.  Outputs a comma-separated list of the repository names and the corresponding new token strings.
1.  Pauses a few seconds between requests to the Codacy API to avoid rate limiting.

```bash
#!/bin/bash

CODACY_API_TOKEN="<your account API token>"
GIT_PROVIDER="<your Git provider>" # gh, ghe, gl, gle, bb, or bbe
ORGANIZATION="<your organization name>"

repositories=$(curl -sX GET "https://app.codacy.com/api/v3/organizations/$GIT_PROVIDER/$ORGANIZATION/repositories" \
                    -H "api-token: $CODACY_API_TOKEN" \
               | jq -r ".data[] | .name")

count=$(echo "$repositories" | wc -l)
read -p "Create project tokens for $count repositories? (y/n) " choice
if [ "$choice" = "y" ]; then
    echo "$repositories" | while read repository; do
        echo -n "$repository,"
        curl -sX POST "https://app.codacy.com/api/v3/organizations/$GIT_PROVIDER/$ORGANIZATION/repositories/$repository/tokens" \
             -H "api-token: $CODACY_API_TOKEN" \
        | jq -r ".data | .token"
        sleep 2 # Wait 2 seconds
    done
else
    echo "No changes made.";
fi
```

Example output:

```text
chart,<new project API token>
docs,<new project API token>
website,<new project API token>
[...]
```

{% include-markdown "../../assets/includes/api-example-pagination-important.md" %}

## See also

-   [API tokens](../api-tokens.md)
