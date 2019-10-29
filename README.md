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

**Judges must be tested by the judges before the contest**

## Main Judge Server
Judge is hosted in `icpc` machine with Nginx. Domjudge has its configuration for Nginx so
put the following configuration in `/etc/nginx/site-enabled/domjudge` is enough.

```
# Generated from 'nginx-conf.in' on Wed May  8 05:10:46 +0430 2019.

# nginx configuration for DOMjudge

### upstream ###
#
# always include this and make sure it points to the socket of PHP-FPM
upstream domjudge {
	server unix:/var/run/php-fpm-domjudge.sock; # if using with etc/domjudge-fpm.conf
	#server unix:/run/php/php7.0-fpm.sock; # default on ubuntu servers
}

limit_req_zone $binary_remote_addr zone=domjudge:10m rate=100r/s;

### http host config ###
#

server {
	listen 80;
	listen [::]:80;

        location / {
                return 301 /domjudge;
        }

	include /home/parham/Documents/DomJudge/domserver/etc/nginx-conf-inner;
}

# Alternatively, use HTTPS and redirect HTTP to HTTPS:

# server {
# 	listen   80;
# 	listen   [::]:80;
# 	server_name _default_;
# 	return 301 https://$host$request_uri;  # enforce https
# }

# server {
# 	listen   443;
# 	listen   [::]:443;

# 	ssl on;
# 	ssl_certificate /path/to/your/SSL-certificate-with-chain.crt;
# 	ssl_certificate_key /path/to/your/SSL-key.key;
# 	ssl_session_timeout 5m;
# 	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
#	ssl_ciphers ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA;
# 	ssl_prefer_server_ciphers on;

	# Strict-Transport-Security is not set by default since it will break
	# instalations without a valid TLS certificate. Enable it if your
	# DOMjudge installation only runs with a valid TLS certificate.
# 	add_header Strict-Transport-Security max-age=31556952;

# 	include /home/parham/Documents/DomJudge/domserver/etc/nginx-conf-inner;
# }
```
