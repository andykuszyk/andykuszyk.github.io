# Migrating project v2 boards in GitHub
## Introduction
Once upon a time, there were project boards in GitHub. They helped you plan, they looked like Trello, and they were much loved. They were classic.

Then, one day, they were deprecated! Along came project v2 boards. They were like Trello, but also like a spreadsheet, and much more between, and they became the new project planning tool in GitHub.

This post is about migrating project boards in GitHub. It's not, as you might expect, about migrating from classic project boards to v2 projects. GitHub offer a migration tool for that in their UI, and it's easy to do. Instead, this post is about migrating from one v2 board to another.

## Why would you want to do that?
Good question. Well, the GitHub classic to v2 migration tool migrates issues to a v2 board with the same status names/columns as the classic board. If you want to migrate the issues on this new board into a different v2 board, or you just want to combine multiple v2 project boards into one, you'll need to work outside of the GitHub UI.

## So, how do you do it?
In this blog post, I'm going to show you how I did this using a combination of:
- The [GitHub GraphQL API](https://docs.github.com/en/graphql/reference).
- The [GitHub command line interface](https://github.com/cli/cli).
- A little bit of Bash and Python scripting.

## Step 1: list the issues on the project v2 board you want to migrate
This can be achieved with the GitHub GraphQL API, via the GitHub CLI:
```sh
gh api graphql \
    --paginate \
    --jq '.data.organization.projectV2.items.nodes[]' \
    -f query='
query($endCursor: String) {
  organization(login:"<organisation-id>"){
    projectV2(number:<project-number>){
      items(first:10, after: $endCursor){
        pageInfo{ hasNextPage endCursor }
        nodes{
          fieldValueByName(name:"Status"){
            __typename
            ... on ProjectV2ItemFieldSingleSelectValue{
              name
            }
          }
          content{
            __typename
            ... on Issue{
              number
              title
              id
            }
          }
        }
      }
    }
  }
}' | jq | sed 's/^}$/},/g' | sed '1s/^{$/[{/g' | sed '$s/^},$/}]/g' | tee issues.json
```

This results in some output like this:
```json
[{
  "content": {
    "__typename": "Issue",
    "id": "I_kwDOGolhjhjkhjke",
    "number": 112,
    "title": "Handle thing field correctly in blah"
  },
  "fieldValueByName": {
    "__typename": "ProjectV2ItemFieldSingleSelectValue",
    "name": "Done 🎉"
  }
},
{
  "content": {
    "__typename": "Issue",
    "id": "I_kwDOGhjkhkjhjkkN",
    "number": 234,
    "title": "Add a stage to the CI which runs the load tests"
  },
  "fieldValueByName": {
    "__typename": "ProjectV2ItemFieldSingleSelectValue",
    "name": "Done 🎉"
  }
}]
```

Let's just examine the commands in the pipeline:
- `gh api graphql` makes the raw request to GitHub, but outputs paginated data that isn't well-formed in a JSON array.
- `jq` pretty-formats the JSON, which leaves characters like `{` and `}` on their own line, although still not in a JSON array.
- `sed 's/^}$/},/g'` replaces errant closing braces of array elements with a well formed brace and comma. This makes paginated elements into valid elements of a JSON array.
- `sed '1s/^{$/[{/g'` replaces the first line containing an opening brace with `[{`, which opens a well-formed JSON array.
- `sed '$s/^},$/}]/g'` replaces the final line containing a trailing comma, with a well-formed `}]`, which closes the JSON array.

The result is a nicely formatted JSON array containing an element for every issue on the board, along with its current status.

## Step 2: map the current statuses, to statuses on your target board
Now that we've got all the issues and their current statuses, we can map all their statuses to statuses on your new project board. This is the step that allows you to migrate issues from one board to another, when there isn't necessarily a straightforward mapping from the statuses on one board to the other.

This is hard to do in Bash, but relatively straight forward in Python:

```python
import json
import os
import sys

# This function maps statuses on your source board to those on your
# target board.
def map_old_status_to_new(old_status):
    if "Backlog" in old_status:
        return "Product planning"
    if "Ready To Start" in old_status:
        return "Ready to develop"
    if "In Progress" in old_status:
        return "In development"
    if "Done" in old_status:
        return None
    if "Parked" in old_status:
        return None

# Read the issues data from file.
with open('issues.json') as f:
	issues = json.loads(f.read())

# Iterate over each of the issues, and map the old status to their
# new status.
for issue in issues:
    try:
        number = issue["content"]["number"]
        old_status = issue["fieldValueByName"]["name"]
        new_status = map_old_status_to_new(old_status)
        if new_status is None:
            print(
                "ignoring issue {} because its status is {}".format(
                    number,
                    old_status
                ),
                file=sys.stderr
            )
            continue
		# Output some new JSON representing the issue and its mapped status.
        mapped_issue = {
            "number": number,
            "id": issue["content"]["id"],
            "old_status": old_status,
            "new_status": new_status
        }
        print(json.dumps(mapped_issue))
    except Exception as e:
        print(
            "error processing issue. error: {}, issue: {}".format(e, issue),
            file=sys.stderr
        )
```

This script reads your issues data, and re-shapes the data into a JSON object per line of standard output. Each object contains the information required to add the issue to the new project board in the correct status.

You could run this Python script from a shell and pipe its output to subsequent commands, or save the output to a file.

The output looks something like this:

```json
{"number": 1151, "id": "I_kwhjkol6Is5ZStTQ", "old_status": "In Progress \ud83c\udfd7\ufe0f", "new_status": "In development"}
{"number": 902, "id": "I_kwhjkol6Is5U9_wD", "old_status": "In Progress \ud83c\udfd7\ufe0f", "new_status": "In development"}
{"number": 1229, "id": "I_kwhjkol6Is5aw2yu", "old_status": "In Progress \ud83c\udfd7\ufe0f", "new_status": "In development"}
```

## Step 3: get the ID of the new project
In order to use the GitHub API to add the issues to the new project, we'll need its ID. We can get this easily using the GitHub CLI:

```sh
project_id=$(gh api graphql --jq '.data.organization.projectV2.id' -f query='
  query{
	organization(login:"form3tech"){
	  projectV2(number:345){
	    id
	  }
	}
  }')
```

## Step 4: find out about the status field in the new project
Project v2 boards have a more flexible status configuration than the columns in a classic board, so in order to make use of them via the API you need to know:
1. The ID of the status field on issues.
2. The IDs and names of the different status options.

We can find all this out in one go with a single request to the GitHub API:

```sh
gh api graphql -f query='
  query{
	organization(login: "form3tech"){
	  projectV2(number: 345) {
	    field(name:"Status"){
	      __typename
	      ... on ProjectV2SingleSelectField{
 		    id
		    options{
		      id
		      name
		    }
	      }
	    }
	  }
	}
  }' | jq
```

The output is something like this:

```json
{
  "data": {
    "organization": {
      "projectV2": {
        "field": {
          "__typename": "ProjectV2SingleSelectField",
          "id": "PVTSSF_lhjkerhkjhjkJlw9zgF9whc",
          "options": [
            {
              "id": "ehjkhkj6",
              "name": "Product planning"
            },
            {
              "id": "hjkhkje4",
              "name": "Ready to develop"
            },
            {
              "id": "hukjhkjh",
              "name": "In development"
            },
            {
              "id": "dhjkhk31",
              "name": "Ready for demo"
            },
            {
              "id": "4hjkhkcb",
              "name": "Blocked"
            },
            {
              "id": "hjkh6657",
              "name": "Approved"
            }
          ]
        }
      }
    }
  }
}
```

From this output, we can find the status field ID (`jq '.data.organization.projectV2.field.id' | sed 's/"//g'`), and the status options (`jq '.data.organization.projectV2.field.options'`)

## Step 5: migrate the issues to the new board
Now, we can bring all of this data together in a Bash script which iterates over each of the issue lines outputted by our Python script, and adds the issue to the correct status on the new board:

This script assumes that:
- The output from the Python script has been saved in `$issue_statuses`.
- The status options have been saved in `$status_option_ids`.
- The ID of the project has been saved in `$project_id`.

```sh
set -eu -o pipefail

echo -n "$issue_statuses" | while read -r issue; do
	# Parse each JSON object into the variables we need.
	issue_id=$(echo -n "$issue" | jq '.id' | sed 's/"//g')
	issue_number=$(echo -n "$issue" | jq '.number')
	new_status=$(echo -n "$issue" | jq '.new_status' | sed 's/"//g')
	status_option_id=$(echo -n "$status_option_ids" | jq ".[] | select(.name == \"$new_status\").id" | sed 's/"//g')

	# Print out the variables we're using for the user to see.
	echo "issue id: $issue_id"
	echo "issue number: $issue_number"
	echo "new status: $new_status"
	echo "status option id: $status_option_id"

	# Check that we've got valid data.
	if [[ -z "$issue_id" || -z "$issue_number" || -z "$new_status" || -z "$status_option_id" ]]; then
		echo "invalid args"
		exit 1
	fi

	echo "adding issue number $issue_number to new board"

	# First, add the issue to the new project, and store the item/card ID in a variable.
	item_id=$(gh api graphql -f query='
		mutation{
			addProjectV2ItemById(input:{
				contentId:"'"$issue_id"'",
				projectId:"'"$project_id"'"
			}){
				item{
					id
					project{
						title
					}
				}
			}
		}' | jq '.data.addProjectV2ItemById.item.id' | sed 's/"//g')

	echo "moving issue number $issue_number to new status $new_status"

	# Then, assign the correct status to the new project item/card.
	gh api graphql --jq '.data.updateProjectV2ItemFieldValue.projectV2Item.fieldValueByName.name' -f query='
		mutation{
			updateProjectV2ItemFieldValue(input:{
				itemId:"'"$item_id"'",
				value:{singleSelectOptionId:"'"$status_option_id"'"},
				fieldId:"'"$status_field_id"'",
				projectId:"'"$project_id"'",
				clientMutationId:"status-update"
			}){
				projectV2Item{
					fieldValueByName(name: "Status"){
						__typename
						... on ProjectV2ItemFieldSingleSelectValue{
							name
						}
					}
				}
			}
		}'
done
```

# Step 6: profit!
So there it is: it's a bit painful, but not possible via the GitHub UI. You need to make a few requests to the GraphQL to get the data you need, and re-shape some of the output into a format that's usable. For small project boards, you could probably do this manually, but if you need to migrate hundreds of issues then I hope you find this post useful!
