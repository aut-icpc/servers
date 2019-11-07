# Servers
## Introduction
These servers are allocated on Amirkabir CEIT datacenter to Judges.

| Host | IP | Users | Remote SSH Access | vCPUs | RAM | URL |
|:----:|:--:|:----- | :------------ | :----: | :---: | :---: |
| icpc | 172.23.132.39 | ssc, parham | - | 2 | 4GB | daavar.ceit.aut.ac.ir |
| judge-1 | 172.23.135.18 | ssc, parham | - | 2 | 4GB | - |
| judge-2 | 172.23.135.19 | ssc, parham | - | 2 | 4GB | - |
| icpc-ftp | 172.23.134.245 | ssc, parham | - | 2 | 4GB | - |
| icpc-2 | 172.23.135.38 | ssc, parham | - | 2 | 4GB | judge.ceit.aut.ac.ir |

To install the DomJudge please refer to its documentation [here](https://www.domjudge.org/).
The only important points are `upload_max_filesize` and `post_max_size` in php and `max_allowed_packet` in mysql.
These variables help judges to upload their test cases and they must set before they want to upload their cases.

**Judges must be tested by the judges before the contest**

## Main Judge Server
Judge is hosted in `icpc` machine with **Nginx**. Domjudge has its configuration for Nginx so
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
```

Please note that we are using **MariaDB** as database and its **root** user is private to [@1995parham](https://github.com/1995parham).

## Print in Domjudge
There is a print panel in Domjudge web interface that can be used by teams to print their solution.
In order to enable it you must change the following section of `domserver/webapp/src/DOMJudgeBundle/Utils`:

```php
...
        // Make file readable for others than webserver user,
        // and give it an extension:
        // chmod($tmp, 0644);
        // rename($tmp, $tmp.'.ps');
        $cmd = '/usr/bin/lp ' . escapeshellarg($tmp) . " 2>&1" . " && " . "rm " . escapeshellarg($tmp);
        exec($cmd, $output, $retval);
...
```

Here we use `lp` command for priting. Use the following instructions to have an up and running printer server that lp relies on:

1. Install hplip and cups : `sudo apt install hplip cups`
2. Open this URL: [http://localhost:631](http://localhost:631)
3. goes to administration > add printer > [select hp]
4. `smb://ceit\[username]:[password]@ceit/192.168.128.10/HPLaserJ4015`

CEIT Printer: 172.23.128.38 (HP LaserJet P4015n)
