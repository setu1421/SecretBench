
# SecretBench: A Dataset of Software Secrets

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.7570239.svg)](https://doi.org/10.5281/zenodo.7570239)


## Table of Contents
   * [Introduction](#introduction)
   * [How To Use](#how-to-use)
   * [Data Overview](#data-overview)
	  * [Regular Expressions](#regular-expressions)
	  * [Overview of SecretBench Metadata](#overview-of-secretbench-metadata)
	  * [Secret Categorization](#secret-categorization)
	  * [Programming Languages](#programming-languages)
	  * [File Types](#file-types)
   * [Future RoadMap](#future-roadmap)
   * [License](#license)
   * [Ethics](#ethics)
   * [How to Contribute](#how-to-contribute)
   * [Authors](#authors)

## Introduction

Throughout 2021, GitGuardian’s monitoring of public GitHub repositories revealed a two-fold increase in the number of secrets (API keys, Authentication Tokens, Database Credentials, and other credentials) exposed compared to 2020, accumulating more than six million secrets. However, no benchmark dataset is publicly available for researchers and tool developers to improve secret detection tools to avoid secret leakage. 

We present a large and versatile dataset, SecretBench, consisting of 97,479 manually labeled secrets of various secret types extracted from 818 public GitHub repositories. The dataset contains 15,084 true secrets out of 97,479 secrets. Our dataset covers 248 programming languages and 311 file types. We have made the dataset available for researchers and tool developers in Google BigQuery and Cloud Storage.

## How to Use

The dataset is stored in Google BigQuery and Cloud Storage. First, you need to create a Google Cloud Account. Google Cloud gives a $300 free credit after opening the account. You can run SQL queries in Google BigQuery to access the secrets and download repositories and related files from Google Cloud Storage.

 - **Google BigQuery Dataset id (*dev-range-332204.secretbench.secrets*):**  Google BigQuery contains 97,479 secrets with ground truth info, whether the secret is true or false. Addition metadata information regarding the secrets such as commit id, start line, and end line can also be accessed. More details of the metadata is described in [Data Overview](#data-overview) section.
 - **Google Cloud Storage (*bucket-name: secretbench*):** The mined 818 public GitHub repositories are stored in Google Cloud Storage in a "**Repos.zip**" file. We have also stored the individual files containing the secrets in a "**Files.zip**" file.

**Important:** *The researchers and developers who want to use our dataset need to contact us. Since the dataset contains sensitive information, a data protection agreement has to be signed with us to avoid any unethical use of the data. Later, we will give access to the dataset using their email addresses.*

## Data Overview

### Regular Expressions:
We used 761 regular expression patterns to collect candidate repositories containing secrets. We collected the regular expression patterns from the source code of the detection packages of [TruffleHog](https://github.com/trufflesecurity/trufflehog/tree/main/pkg/detectors) and a study done by [Meli et al.](https://www.ndss-symposium.org/wp-content/uploads/2019/02/ndss2019_04B-3_Meli_paper.pdf). A snapshot of 10 regular expression patterns is presented in the below table. The complete list of regular expression patterns can be found [here](https://github.com/setu1421/SecretBench/blob/main/Regular%20Expressions/Secret%20Regular%20Expression.xlsx).

|Pattern ID|Secret Type|Regular Expression|Source|
|--------|--------|--------|--------|
|65|AWS API Secret|\b([A-Za-z0-9+/]{40})[ \r\n'"\x60]|TruffleHog|
|71|Azure Client Secret|(?i)(%s).{0,20}([a-z0-9_\.\-~]{34})|TruffleHog|
|216| Dropbox API Key|\b(sl\.[A-Za-z0-9\-\_]{130,140})\b|TruffleHog|
|237|Facebook Access Token|EAACEdEose0cBA[0-9A-Za-z]+|Meli et al.|
|278|Generic Pattern|(?i)(?:pass\|token\|cred\|secret\|key)(?:.\|[\n\r]){0,40}(\b[\x21-\x7e]{16,64}\b)|TruffleHog|
|290|Github Token|\b((?:ghp\|gho\|ghu\|ghs\|ghr)_[a-zA-Z0-9]{36,255})\b|TruffleHog|
|605|Slack Token|(xoxb\|xoxp\|xapp\|xoxa\|xoxr)\-[0-9]{10,13}\-[a-zA-Z0-9\-]*|TruffleHog|
|640|Stripe API Key|[rs]k_live_[a-zA-Z0-9]{20,30}|TruffleHog|
|691|Twitter Access Token|(?i)(?:twitter)(?:.\|[\n\r]){0,40}\b[1-9][0-9]\+\-[0-9a-zA-Z]{40}\b|Meli et al.|
|747|Youtube/Google OAuth ID|[0-9]+-[0-9A-Za-z_]{32}\.apps\.googleusercontent\.com|Meli et al.|

### Overview of SecretBench Metadata:

We curated 818 public Github repositories and extracted 97,479 candidate secrets. Out of 97,479 secrets, we labeled 15,084 secrets as true secrets. Each secret is manually labeled by finding out whether the secret is actual or not after inspecting the secret and the source code context of the secret. Below we present an overview of the SecretBench data.

|Field Name|Description|Data Type|
|--------|--------|--------|
|id|Unique identifier of the secret.|String|
|secret|Candidate secret string. The secret is surrounded by "[]" parenthesis.|String|
|repo_name|Name of the repository. For example: "setu1421/SecretBench"|String|
|domain|Domain of the repository such as GitHub|String|
|commit_id|Commit hash where the secret is added. For example: "a074a5afe1d2663fda756c1bf3c87bad426cf7de"|String|
|file_path|File path where the secret is included. For example: "dev.config" and "config/test.env".|String|
|file_type|Type of the file such as .py and .config.|String|
|start_line|Start line no. in the file where the secret is present.|Integer|
|end_line|End line no. in the file where the secret is present. For secrets present in a single line, the start_line and end_line will be same.|Integer|
|start_column|Start index of the secret in the start line.|Integer|
|end_column|End index of the secret in the end line.|Integer|
|committer_email|Email address of the developer who committed the secret.|String|
|commit_date|The timestamp of the commit. For example: 2018-10-24T21:22:19Z|TimeStamp|
|label|The ground truth label of the secret. "True" for actual secret and "False" for fake/dummy secret.|Boolean|
|is_template|Flag to indicate if the secret is a placeholder such as "MY_PASSWORD" and "Place_Your_Token_Here". | Boolean|
|in_url| Flag to indicate if the secret is part of URL such as "http://user:pwd@site.com".| Boolean|
|entropy| Shannon entropy value of the secret.| Float|
|character_set| Characters used in the secret such as NumberOnly, CharOnly and Any. | String|
|has_words| Flag to indicate if any [common English word](https://github.com/first20hours/google-10000-english) of at least length of 4 is present within the secret.| Boolean|
|length|Length of the secret.| Integer|
|is_multiline| Flag to indicate if the secret is present in multiple lines. Most of the time true for private keys.| Boolean|
|category| The category of the secret. The secrets are categorized in eight categories. See section [Secret Categorization](#secret-categorization).| String|
|file_identifier| Unique identifier of the file to check the secret from local system.| String|
|repo_identifier|Unique identifier of the repository to check the secret from local system. |String|
|comment| A description of the secret types such as Slack Token, AWS Access Key ID and Value with "key" as part of attribute name.|String|

The "**repo_identifier**" and "**file_identifier**" can be used to locate the specific repository and the file where the secret is present. The repositories and files can be downloaded from Google Cloud Storage. See Section [How to Use](#how-to-use).

### Secret Categorization:

The secrets present in our dataset in categorized into eight categories. In the table below, we present the number of total candidate secrets, true secrets, and the category description.

|Category Name|Description|True Secrets|Total Secrets
|--------|--------|--------|--------|
|Private Key|This category contains the private keys such as cryptographic RSA private key and EC private key.|5,789|8,584|
|API Key and Secret|This category contains any API Keys and secret such as Twillo API key and Stripe API key. | 4,529 |5,162|
|Authentication Key and Token|This category contains the access keys and tokens such as AWS Access Key ID and Slack Token.| 3,569| 5,833|
|Generic Secret|This category contains any generic secrets such application package secret, recaptcha site key.| 334 | 439|
|Database and Server URL |This category contains the database and server URLs. For example. any mongoDB connection string and any FTP server url.| 162 | 9,970|
|Password |This category contains any plain text passwords.| 150 | 705|
|Username |This category contains any plain text usernames.| 27 | 96|
|Other |This category contains other possible secrets such as Package Key ID or any random string.| 524 | 66,690|

### Programming Languages:

Our dataset covers 248 programming languages. Note that each GitHub repository can have multiple programming languages. The top 10 programming languages based on the number of repositories is presented below. The full list of programming languages can be found [here](https://github.com/setu1421/SecretBench/blob/main/Metadata/Programming%20Languages/language_repo_stat.csv).

|Language Name|No. of Repository|
|--------|--------|
|Shell|459|
|HTML|416|
|JavaScript|414|
|CSS|316|
|Python|312|
|Dockerfile|183|
|Java|180|
|Makefile|179|
|Ruby|172|
|C|128|

### File Types:

Our dataset consists of secrets present in 311 file types. Below we present the top 5 file types based on the number of candidate secrets in our dataset. The full list of file types can be found [here](https://github.com/setu1421/SecretBench/blob/main/Metadata/File%20Types/file_types.csv).
|File type|Description|Total Secrets|
|--------|--------|--------|
|js|Javascript file|10,412|
|nix|Package Manager File|8,623|
|json|JavaScript Object Notation File|8,132|
|txt|Text File|7,737|
|xml|Extensible Markup Language File|6,429|

In addition, we present the top 5 file types based on the number of true secrets in our dataset.
|File type|Description|Total Secrets|
|--------|--------|--------|
|txt|Text File|2,935|
|toml|Configuration File|1,985|
|js|Javascript file|1,583|
|html|Hypertext Markup Language File|1,337|
|pem|Privacy Enhanced Mail Format File|813|

### Future RoadMap:

 - Currently, our dataset consists of only GitHub repositories. We will expand our dataset by including repositories from other version control systems such as Gitlab and Bitbucket.
 - We will enrich our dataset with more features related to secrets, such as whether the secrets have parentheses (possible function call) and begin with a $ sign (possible variable). The complete list of our additional features is available [here](https://github.com/setu1421/SecretBench/blob/main/RoadMap/Additional%20Features.xlsx).


## License:

This project is licensed under the terms of the MIT license. Please check [LICENSE](https://github.com/setu1421/SecretBench/blob/main/LICENSE) for more details.


## Ethics:
Since our dataset contains sensitive information, we will make available to dataset only to researchers and tool developers. The researchers and tool developers will sign an agreement to protect the data from any unethical use.

### How to Contribute

Please email us if you want to contribute. See [Authors](#authors) section for contact information.

## Authors:
 - Setu Kumar Basak (sbasak4@ncsu.edu)
 - Lorenzo Neil (lcneil@ncsu.edu)
 - Bradley Reaves (bgreaves@ncsu.edu)
 - Laurie Willams (lawilli3@ncsu.edu) 
