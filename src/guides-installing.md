---
redirect_from: 
   - "/install/"
   - "/installing/"
   - "/installation/"
   - "/upgrading/"
---

# Installing Version 2

## Via the install script

To install Learning Locker version 2, you can run one of the commands below **as the root user** on `CentOS`, `Fedora`, `Ubuntu`, and `Debian`. For more information, you can view the [deployment repository's documentation](https://github.com/LearningLocker/deploy).

Please run the required update script for your distro to ensure you have the latest version of all packages.

e.g.
```
yum update
```
or
```
apt-get update && apt-get upgrade
```


**You must run this script as root user. Typically this can be done by running `sudo su -`**

**Install with cURL**
```sh
curl -o- -L http://lrnloc.kr/installv2 > deployll.sh && bash deployll.sh
```
**Install with Wget**:
```sh
wget -qO deployll.sh http://lrnloc.kr/installv2 && bash deployll.sh
```

### Upgrading
To upgrade, simply re-run the command above. You will be asked if you wish to "upgrade" any existing instance, or perform a fresh install.

### Logs

By default, logs are written to `/var/log/learninglocker/`

#### Install log

If there is a problem installing the script, you can view the full install log output here:
`/var/log/learninglocker/install.log`

#### Service logs

Individual logs for the different services outputs (stdout) and errors (stderr) are available in this directory under the following names:

**stdout**
* `xapi_stdout***.log`
* `api_stdout***.log`
* `ui_stdout***.log`
* `worker_stdout***.log`

**stderr**
* `xapi_stderr***.log`
* `api_stderr***.log`
* `ui_stderr***.log`
* `worker_stderr***.log`

`***`: _Logs may have slightly different names due to rotation_


### Restarting the services
To restart the services, simply run the following command: 
```
service pm2-learninglocker restart
```
Where `learninglocker` is the system user you chose to install with in the script (defaults to `learninglocker`)

### Managing the services

The PM2 service manages the 4 micro-services that Learning Locker requires. This is installed by default with the install script under the system user you chose. 

**In order to use the pm2 service, first ensure you are in as the correct system user:**

(using the default `learninglocker` system user):
```
sudo su learninglocker
```

#### Service status
To view the status of your processes (using the default `learninglocker` system user):
```
pm2 status
```

Output:
```
┌──────────┬─────────┬────────┬───┬──────┬────────────┐
│ Name     │ mode    │ status │ ↺ │ cpu  │ memory     │
├──────────┼─────────┼────────┼───┼──────┼────────────┤
│ API      │ cluster │ online │ 0 │ 0%   │ 144.0 MB   │
│ UIServer │ cluster │ online │ 0 │ 0%   │ 105.8 MB   │
│ Worker   │ cluster │ online │ 0 │ 0%   │ 109.5 MB   │
│ xAPI     │ cluster │ online │ 0 │ 0%   │ 81.8 MB    │
└──────────┴─────────┴──-─────┴───┴──────┴────────────┘
```

#### Service logs

You can view a tail of the logs by running:
```
pm2 logs
```

Or view the logs for a particular service (by name or ID):
```
pm2 logs xAPI
```

To view more lines:
```
pm2 logs --lines 1000
```

### Restarting the services manually
You can restart all the services by running:
```
pm2 restart all
```

Or individual services by their name or ID:
```
pm2 restart UIServer
```


### Application Structure

There are two main repositories that are installed as part of a fresh Learning Locker installation, the [Learning Locker application](https://github.com/LearningLocker/learninglocker) and [xAPI service](https://github.com/LearningLocker/xapi-service). An in depth look at what both these packages do can be read in the [Architecture Overview](../overview-architecture).

When installing your LL instance using the install script, these packages will be (by default) installed to `/usr/local/learninglocker/current` (as a symlink to a directory inside `/usr/local/learninglocker/release/...`).

Inside `current/` lives the Learning Locker application which controls the User Interface, API and worker.

We also install the xAPI Service here, inside the `xapi/` directory.


### Configuration & Environment Variables

Each of these applications has their own `.env`. These hold all the configurations that the applications require in order to run, from database settings to logging configuration.

By default the install script will copy the (.env.example) from [both](https://github.com/LearningLocker/learninglocker/blob/master/.env.example) repos [respectively](https://github.com/LearningLocker/xapi-service/blob/master/.env.example).

It is likely you will wish to configure your application to connect to external databases, and whilst setup and configuration of these in beyond the scopes of this documentation, you will need to ensure that both `.env` files contain the same configuration values where appropriate.

A full description of all configuration values in both repositories is available in the [Configuration Guide](../guides-configuring)

___

## Production Installations
For production installations, we recommend the following setup**:**

* 1 Load balancer (e.g. [AWS Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/));
* 2 Learning Locker servers (e.g. [AWS EC2](https://aws.amazon.com/ec2/));
* 3 Mongo servers in a replica set (e.g. [Atlas](https://www.mongodb.com/cloud/atlas));
* 1 Redis server (e.g. [AWS ElastiCache](https://aws.amazon.com/elasticache/)).

This setup ensures good performance and a reasonable degree of redundancy in case of failures in some parts. We'd also recommend that you back up your Mongo database quite regularly depending on your own data requirements. If this sounds too costly or challenging, you may wish to consider using [our Software as a Service (SaaS) enterprise solution](https://www.ht2labs.com/learning-locker/). If you require more advice for your setup, please get in touch via [hello@ht2labs.com](mailto:hello@ht2labs.com).

___

## Custom installations

Please follow instructions [here](../guides-custom-installation) if you wish to install Learning Locker manually.