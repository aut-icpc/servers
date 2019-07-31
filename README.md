# Servers
## Introduction
These servers are allocated on Amirkabir CEIT datacenter to Judges.

| Host | IP | Users | Remote SSH Access | vCPUs | RAM | URL |
|:----:|:--:|:----- | :------------ | :----: | :---: | :---: |
| icpc | 172.23.132.39 | ssc, parham | - | 2 | 4GB | daavar.ceit.aut.ac.ir |
| judge-1 | 172.23.135.18 | ssc, parham | - | 2 | 4GB | - |
| judge-2 | 172.23.135.19 | ssc, parham | - | 2 | 4GB | - |
| icpc-ftp | 172.23.134.245 | ssc, parham | - | 2 | 4GB | - |

To install the DomJudge please refer to its documentation [here](https://www.domjudge.org/).
The only important points are `upload_max_filesize` and `post_max_size` in php and `max_allowed_packet` in mysql.
These variables help judges to upload their test cases and they must set before they want to upload their cases.
