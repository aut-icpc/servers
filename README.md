# Servers

## Introduction

These servers are allocated on Amirkabir CEIT datacenter to Judges.

|    Host     |        IP         | Users           | Remote SSH Access | vCPUs |   RAM   |          URL          |
| :---------: | :---------------: | :-------------- | :---------------- | :---: | :-----: | :-------------------: |
|    icpc     |   172.23.132.39   | ssc, parham     | -                 |   2   |   4GB   | daavar.ceit.aut.ac.ir |
|   judge-1   |   172.23.135.18   | ssc, parham     | -                 |   2   |   4GB   |           -           |
| ~~judge-2~~ | ~~172.23.135.19~~ | ~~ssc, parham~~ | -                 | ~~2~~ | ~~4GB~~ |           -           |
|   judge-3   |   172.23.135.39   | ssc, parham     | -                 |   2   |   4GB   |           -           |
|   judge-4   |   172.23.135.63   | ssc, parham     | -                 |   2   |   4GB   |           -           |
|   judge-5   |   172.23.135.64   | ssc, parham     | -                 |   2   |   4GB   |           -           |
|  icpc-ftp   |  172.23.134.245   | ssc, parham     | -                 |   2   |   4GB   |           -           |
|   icpc-2    |   172.23.135.38   | ssc, parham     | -                 |   2   |   4GB   | judge.ceit.aut.ac.ir  |
| ~~aut-icpc-1~~  |  ~~172.25.157.111~~   | ~~acm01, parham~~   | -                 |   ~~2~~   |   ~~4GB~~   |           -           |
| ~~aut-icpc-2~~ | ~~172.25.157.112~~   | ~~acm01, parham~~   | -                 |   ~~2~~   |   ~~4GB~~   |   ~~daavar.aut.ac.ir~~    |

To install the DomJudge please refer to its documentation [here](https://www.domjudge.org/).
The only important points are `upload_max_filesize` and `post_max_size` in php and `max_allowed_packet` in mysql.
These variables help judges to upload their test cases and they must set before they want to upload their cases.

**Judges must be tested by the judges before the contest**

## Print in Domjudge

There is a print panel in Domjudge web interface that can be used by teams to print their solution.
In order to enable it you must change the following section of `domserver/webapp/src/DOMJudgeBundle/Utils`:

```php
// Make file readable for others than webserver user,
// and give it an extension:
// chmod($tmp, 0644);
// rename($tmp, $tmp.'.ps');
$cmd = '/usr/bin/lp ' . escapeshellarg($tmp) . " 2>&1" . " && " . "rm " . escapeshellarg($tmp);
exec($cmd, $output, $retval);
```

Here we use `lp` command for priting. Use the following instructions to have an up and running printer server that lp relies on:

1. Install hplip and cups : `sudo apt install hplip cups`
2. Open this URL: [http://localhost:631](http://localhost:631)
3. goes to administration > add printer > [select hp]
4. `smb://ceit\[username]:[password]@ceit/192.168.128.10/HPLaserJ4015`

CEIT Printer: 172.23.128.38 (HP LaserJet P4015n)

## Judgehosts

To install judgehosts you can refer to [this](https://www.domjudge.org/docs/manual/master/install-judgehost.html) document.

Keep in mind that:

- Put domjudge's sudoers rules into `/etc/sudoers.d`. this file is created after running `make install-domjudge`.

- If you are running judgehost on a multi-core server. each `judgedaemon` reserves and uses a **single** CPU core, but other resources are shared among daemons. Don't run more than 4 judgedaemon on a single machine.

- For each judge daemon create a user with `X` indicating a cpu core i.e. `0`:

  ```shell
  sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-X
  sudo usermod -aG sudo domjudge-run-X
  ```

- Run judgedaemon instance with `./judgedaemon -n X`

- Use **Systemd Service** provided in `lib/systemd/system` folder (if not installed automatically). Rename `domjudge-judgehost.service` to `domjudge-judgehost@.service` and Use `path-to-judgedaemon -n %i`.
  Enable all how many daemons you want with:

  ```shell
  sudo systemctl enable domjudge-judgehost@X
  ```
