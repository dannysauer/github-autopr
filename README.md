## Usage
This action automatically open Pull Request:

```workflow
workflow "Fast prototype" {
  on = "push"
  resolves = ["Create PR"]
}

action "Create PR" {
  uses = "funivan/github-autopr@master"
  secrets = ["GITHUB_TOKEN"]
}
```

## Filter
In every runtime environment for an Action we have file that holds the JSON 
data for the event [PushEvent payload](https://developer.github.com/v3/activity/events/types/#pushevent).
From that we can get the commit authors 
name and other important data. We can skip this action by checking this data.

Action support two arguments:
- First: argument [JQ](https://stedolan.github.io/jq/) selector which will select data from the event data file
- Second: grep regex
```workflow
action "Create PR" {
  uses = "funivan/github-autopr@master"
  secrets = ["GITHUB_TOKEN"]
  args = ".head_commit.message #pr"
}
```
Under the hood we will fetch commit message and check if it contains `#pr` word.
```
result=$(echo "some test commit message #pr" | grep "#pr" | wc -l )
```
If result is positive - we will continue our action. If negative - stop it immediately.

### More example
Check if branch ends with `-pr` word 
`args = ".repository.default_branch -pr$"`
