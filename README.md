
# SecretBench -A Dataset of Software Secrets

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 
[![DOI](https://zenodo.org/badge/589857531.svg)](https://doi.org/10.5281/zenodo.7553204)


## Table of Contents
   * [Introduction](#introduction)
   * [How To Use](#how-to-use)
   * [Data Overview](#data-overview)
	  * [Data statistics](#data-statistics)
   * [Data](#data)
	  * [Selecting Target Repositories](#selecting-target-repositories)
	  * [Ground Rules for Labeling Suspected Credential Information](#ground-rules-for-labeling-suspected-credential-information)
   * [Metadata](#metadata)
   * [Obfuscation](#obfuscation)
   * [License](#license)
   * [Directory Structure](#directory-structure)
   * [Benchmark](#benchmark)
     * [Main Requirements](#main-requirements)
     * [Installation](#installation)
     * [How to run](#how-to-run)
     * [Benchmark Result](#benchmark-result)
     * [Used Tools for Benchmarking](#used-tools-for-benchmarking)
   * [Citation](#citation)
   * [How to Get Involved](#how-to-get-involved)
   * [How to Contact](#how-to-contact)

## Introduction

Throughout 2021, GitGuardian’s monitoring of public GitHub repositories revealed a two-fold increase in the number of secrets (API keys, Authentication Tokens, Database Credentials, and other credentials) exposed compared to 2020, accumulating more than six million secrets. However, no benchmark dataset is publicly available for researchers and tool developers to improve secret detection tools to avoid secret leakage. 

We present a large and versatile dataset, SecretBench, consisting of 97,477 manually labeled secrets of various secret types extracted from 818 public GitHub repositories. The dataset contains 15,086 true secrets out of 97,477 secrets. Our dataset covers 248 programming languages and 311 file types. We have made the dataset available for researchers and tool developers in Google BigQuery and Cloud Storage.

## How to Use

The dataset is stored in Google BigQuery and Cloud Storage. First, you need to create a Google Cloud Account. Google Cloud gives a $300 free credit after opening the account. You can run SQL queries in Google BigQuery to access the secrets and download repositories and related files from Google Cloud Storage.

 - **Google BigQuery Dataset id (*dev-range-332204.secretbench.secrets*):**  Google BigQuery contains 97,477 secrets with ground truth info, whether the secret is true or false. Addition metadata information regarding the secrets such as commit id, start line, and end line can also be accessed. More details of the metadata is described in [Data Overview](#data-overview) section.
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

## License:

## Ethics:


## Citation:


## Authors:
 - Setu Kumar Basak (sbasak4@ncsu.edu)
 - Lorenzo Neil (lcneil@ncsu.edu)
 - Bradley Reaves (bgreaves@ncsu.edu)
 - Laurie Willams (lawilli3@ncsu.edu) 
