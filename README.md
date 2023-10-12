# DevGPT: Studying Developer-ChatGPT Conversations
[https://github.com/NAIST-SE/DevGPT/](https://github.com/NAIST-SE/DevGPT/)

![Example of a ChatGPT conversation from the dataset](example.png)

## Content
In this repository, we've incorporated six snapshots, each containing six JSON files encompassing GitHub Issues, Pull Requests, Discussions, Commits, Code Files, and Hacker News threads. Additionally, you can access the same dataset through the Zenodo data repository:
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.8304091.svg)](https://doi.org/10.5281/zenodo.8304091)


## Data Extraction

### GitHub [GraphQL](https://docs.github.com/en/graphql) and [REST](https://docs.github.com/en/rest?apiVersion=2022-11-28):
We performed a search function with the keyword `https://chat.openai.com/share/` in the GitHub GraphQL API to identify mentions of shared links in issues, pull requests, and discussions from GitHub. Since this function did not support searching special characters (e.g., `/` and `:`), we then identified the exact mentions of shared ChatGPT links by the regular expression:
```
https:\/\/chat\.openai\.com\/share\/[a-zA-Z0-9-]{36}
```
For the GitHub REST API, we performed the same keyword and regular expression to obtain the references of shared ChatGPT links in commits and code files from GitHub. Moreover, we applied additional filters (e.g., creation time) in order to work around GitHub’s limit on the search function, since it only supports a maximum of a thousand results per call. It's worth noting that the total count varies upon each API/[Webpage access](https://github.com/search?q=%22https%3A%2F%2Fchat.openai.com%2Fshare%2F%22&type=commits) in the GitHub, which leads to inconsistent results compared to the GitHub website.

### [HackerNews](https://hn.algolia.com/api): 
Similar to the GitHub search function, HackerNews also provides an endpoint (`http://hn.algolia.com/api/v1/search?query=...`) to obtain references of shared ChatGPT links in posts from HackerNews. The same regular expression was applied to further identify whether mentioned links are shared ChatGPT links.

### Shared ChatGPT conversations: 
After obtaining shared ChatGPT links from different sources, we fetched the web page of the shared conversation from ChatGPT. Since users could delete or inactivate the shared conversations, we fetched all data once a week (six snapshots in total from July 27, 2023, August 3, 2023, August 10, 2023, August 17, 2023, August 24, 2023, and August 31, 2023). If the response status is other than *200*, we obtain the data from the same shared link in all snapshots.

## Data Set Structure
GPTShare comprises six snapshots taken on July 27, 2023, August 3, 2023, August 10, 2023, August 17, 2023, August 24, 2023, and August 31, 2023. Each snapshot consists of six JSON files sourced differently. We name each JSON using a combination of its fetch time and source name. Additionally, we've provided a consolidated CSV file containing all ChatGPT links harvested from GitHub and Hacker News in each snapshot.
```
📁 /
├─ 📄 %Y%m%d_%H%M%S_file_sharing.json
├─ 📄 %Y%m%d_%H%M%S_hn_sharing.json
├─ 📄 %Y%m%d_%H%M%S_pr_sharing.json
├─ 📄 %Y%m%d_%H%M%S_issue_sharing.json
├─ 📄 %Y%m%d_%H%M%S_discussion_sharing.json
├─ 📄 %Y%m%d_%H%M%S_commit_sharing.json
├─ 📄 ChatGPT_Link_Sharing.csv
```

## Data Structure

### GitHub Code File
| Attribute    | Description |
| -------- | ------- |
| Type  | Source type   |
| URL | URL to the mentioned source     |
| ObjectSha    | Sha of the source     |
| FileName | Filename of this code file     |
| FilePath    | Filepath to this code file     |
| Author  | Author who introduced this mention   |
| Content | Content of this code file, which can be decoded by base64   |
| RepoName    | Name of the repository that contains this code file     |
| RepoLanguage  | Primary programming language of the repository that contains this code file **_NOTE:_** it can be null when this repository does not contain any code  |
| CommitSha | Sha of the commit that introduced this mention   |
| CommitMessage    | Message of the commit that introduced this mention     |
| AuthorAt  | When the author added this mention  |
| CommitAt | When the author committed this mention     |
| ChatgptSharing    | List of ChatGPT link mentions. Refer to the `ChatgptSharing` structure for details  |

### GitHub Commit
| Attribute    | Description |
| -------- | ------- |
| Type  | Source type   |
| URL | URL to the mentioned source     |
| Author  | Author who introduced this mention   |
| RepoName    | Name of the repository that contains this commit     |
| RepoLanguage  | Primary programming language of the repository that contains this commit **_NOTE:_** it can be null when this repository does not contain any code |
| Sha    | Sha of the source     |
| Message    | Message of this commit    |
| AuthorAt  | When the author added this mention  |
| CommitAt | When the author committed this mention     |
| ChatgptSharing    | List of ChatGPT link mentions. Refer to the `ChatgptSharing` structure for details  |

### GitHub Issue
| Attribute    | Description |
| -------- | ------- |
| Type  | Source type   |
| URL | URL to the mentioned source     |
| Author  | Author who introduced this mention   |
| RepoName    | Name of the repository that contains this issue     |
| RepoLanguage  | Primary programming language of the repository that contains this issue **_NOTE:_** it can be null when this repository does not contain any code |
| Number    | Issue number of this issue     |
| Title    | Title of this issue    |
| Body | Description of this issue     |
| AuthorAt  | When the author created this issue  |
| ClosedAt | When this issue was closed  **_NOTE:_** it can be null when this issue is not closed |
| UpdatedAt | When the latest update of this issue occurred |
| State | The state of this issue (i.e., OPEN and CLOSED)  |
| ChatgptSharing    | List of ChatGPT link mentions. Refer to the `ChatgptSharing` structure for details  |

### GitHub Pull Request
| Attribute    | Description |
| -------- | ------- |
| Type  | Source type   |
| URL | URL to the mentioned source     |
| Author  | Author who introduced this mention   |
| RepoName    | Name of the repository that contains this pull request     |
| RepoLanguage  | Primary programming language of the repository that contains this pull request **_NOTE:_** it can be null when this repository does not contain any code |
| Number    | Pull request number of this mention     |
| Title    | Title of this pull request |
| Body | Description of this pull request |
| CreatedAt  | When the author created this pull request |
| ClosedAt | When this pull request was closed  **_NOTE:_** it can be null when this issue is not closed |
| MergedAt | When this pull request was merged  **_NOTE:_** it can be null when this issue is not merged |
| UpdatedAt | When the latest update occurred |
| State | The state of this pull request (i.e., OPEN, CLOSED, MERGED)  |
| Additions | Number of lines added in this pull request |
| Deletions | Number of lines deleted in this pull request |
| ChangedFiles | Number of files changed in this pull request |
| CommitsTotalCount | Number of commits included in this pull request |
| CommitSha | A list of commit Shas that are included in this pull request |
| ChatgptSharing    | List of ChatGPT link mentions. Refer to the `ChatgptSharing` structure for details  |

### Hacker News
| Attribute    | Description |
| -------- | ------- |
| Type  | Source type   |
| ID  | Story ID of the mentioned source   |
| URL | URL to the mentioned source     |
| Author  | Author who introduced this mention   |
| Points | Number of points received from this story     |
| AttachedURL    | URL that is attached in this story **_NOTE:_** it can be null when this story does not attach the URL    |
| Title    | Title of this story **_NOTE:_** it can be null when this story does not have a title   |
| StoryText | Description of this story **_NOTE:_** it can be null when this story does not have a description   |
| CreatedAt  | When the author created this story  |
| CommentsTotalCount | Number of comments in this story  |
| ChatgptSharing    | List of ChatGPT link mentions. Refer to the `ChatgptSharing` structure for details  |

### GitHub Discussion
| Attribute    | Description |
| -------- | ------- |
| Type  | source type   |
| URL | URL to the mentioned source     |
| Author  | Author who introduced this mention   |
| RepoName    | Name of the repository that contains this discussion |
| RepoLanguage  | Primary programming language of the repository that contains this discussion **_NOTE:_** it can be null when this repository does not contain any code |
| Number    | Discussion number of this mention     |
| Title    | Title of this discussion |
| Body | Description of this discussion |
| AuthorAt  | When the author created this discussion  |
| ClosedAt | When this discussion was closed  **_NOTE:_** it can be null when this discussion is not closed |
| UpdatedAt | When the latest update of this discussion occurred |
| Closed | Whether or not this discussion is closed  |
| UpvoteCount | Number of upvotes that this discussion has received.  |
| ChatgptSharing    | List of ChatGPT link mentions. Refer to the `ChatgptSharing` structure for details  |

### ChatgptSharing
| Attribute    | Description |
| -------- | ------- |
| URL | URL to the shared ChatGPT links     |
| Mention | Attributes about mention. Refer to the `Mention` structure for details     |
| Status  | HTTP response status when accessing this URL  |
| DateOfConversation    | Date when this conversation occurred     |
| DateOfAccess  | Date when we accessed this URL    | 
| NumberOfPrompts    | Number of prompts in this conversation    |
| TokensOfPrompts    | Number of tokens of prompts in this conversation    |
| TokensOfAnswers | Tokens of answers in this conversation     |
| Model  | 	Version of the model used in this conversation  |
| Conversations    | List of conversations. Refer to the `Conversations` structure for details |
| HTMLContent  | HTML content from the shared ChatGPT link |

### Mention
| Attribute    | Description |
| -------- | ------- |
| MentionedURL | URL to the mentioned source     |
| MentionedProperty  | What kind of property was mentioned in this shared ChatGPT link   |
| MentionedAuthor    | Who mentioned this shared ChatGPT link    |
| MentionedText  | The context when this shared ChatGPT link was mentioned     |
| MentionedPath    | Where the comment was added in files for reference of shared ChatGPT links in review threads of GitHub pull requests (only exists when referenced in review threads of pull requests)   |
| MentionedIsAnswer    | Whether this comment is marked as an answer in discussion (only exists when referenced in comments of discussion) |
| MentionedUpvoteCount    | Number of upvotes that this comment has received (only exists when referenced in comments of discussion)  |

### Conversations
| Attribute    | Description |
| -------- | ------- |
| Prompt | Prompt that user inputted     |
| Answer  | Answer that ChatGPT generated   |
| ListOfCode    | Code list from the answer (includes code type and content)   |
