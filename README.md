# About Nuxeo Kibana For Demos

This repository contains configuration files and installation scripts to set up a Kibana server on an AWS Nuxeo demo instance.

Note: starting with version 6.3, Kibana includes X-Pack by default whereas the embdedded ElasticSearch server that Nuxeo uses does not. So this project now installs the "oss" version of Kibana, which doesn't include X-Pack.

# Requirements

* An EC2 instance created using the template from https://github.com/nuxeo/presales-vmdemo/tree/master/AWS-templates
* Certbot for HTTPS, which should have been installed by the above

# Install

### Clone this GitHub repository

```
sudo -E su ubuntu
cd /home/ubuntu
git clone https://github.com/nuxeo-sandbox/nuxeo-kibana-demo
cd nuxeo-kibana-demo
```

### Easy Install: The `auto.sh` script

**IMPORTANT**: This "auto" script contains the other parts: Create Kibana user for Apache, downolad.sh and then install.sh. If you need to change something, do it before running this script.

Let the script guess the kibana version to install as well as the host name for the apache config. This will request the password you want for the `kibana` user.

```
chmod 777 auto.sh
sudo -E ./auto.sh
```

### Custom: Create a kibana user for apache

```
sudo apt-get install apache2-utils
sudo htpasswd -c /etc/apache2/passwords kibana
```

### Custom: Use the provided script to Download Kibana

NOTE: to change the Kibana version pass the version to `download.sh`.

See Kibana documentation:
> Kibana should be configured to run against an Elasticsearch node of the same version. This is the officially supported configuration.

```
cd nuxeo-kibana-demo
chmod 777 download.sh
./download.sh
# --or--
./download.sh <version>
```

### Custom: Run install script as root
  * You can pass the host name to `install.sh` (e.g. if the demo is `cool.cloud.nuxeo.com` pass `cool` as a param) if not already set in the STACK_ID env variable

```
chmod 777 install.sh
sudo -E ./install.sh <myhost>
```

* If your Nuxeo instance already had some data, you must now [rebuild the Elasticsearch index](https://doc.nuxeo.com/display/ADMINDOC/Elasticsearch+Setup#ElasticsearchSetup-RebuildingtheIndexRebuildingtheIndex).

* Update Route53 (on AWS), if relevant, so to add kibana-somename.cloud.nuxeo.com (with the exact same TNAME as somename.cloud.nuxeo.com)

* To access kibana:  go to kibana-somename.cloud.nuxeo.com, use `kibana` as user, enter the password you set for this user.

# (Optional) Standalone Elasticsearch Setup

**IMPORTANT:** these steps are optional, standalone ES is not required, embedded ES may be used.

* Install the correct version of ES (see https://doc.nuxeo.com/nxdoc/compatibility-matrix/)
* Start ES is needed

```
# Assuming you used the defaut 9200 port
# Check to see if elasticsearch is installed:
curl "http://localhost:9200"
# Must return a value with status 200

# To start elasticsearch:
sudo service elasticsearch start
```

* Edit nuxeo your nuxeo.conf file

```
sudo vim /etc/nuxeo/nuxeo.conf
```

* Uncomment the following lines (and assuming you let the default value for clusters, 9300):

```
elasticsearch.addressList=localhost:9300
elasticsearch.clusterName=elasticsearch
```

* Restart your nuxeo instance

```
sudo service nuxeo restart
```


# Troubleshooting

* If using standalone Elasticsearch with less than three nodes, the Nuxeo server log will contain a warning about cluster health not being "GREEN". This is normal, an Elasticsearch cluster requires a minimum of 3 nodes to be GREEN.

* On some configuration, we noticed that after the installation process, nuxeo server displays the Welcome Wizard again. If this is the case for you, then edit `nuxeo.conf` and set `nuxeo.wizard.done` to `true`.

# Upgrade

To upgrade the kibana version, first delete or remove the existing folder

```
cd
mv kibana kibana.old
```

Modify the version in download.sh, run the script and finally restart supervisor

```
sudo service supervisor restart
```


# Support

**These features are not part of the Nuxeo Production platform.**

These solutions are provided for inspiration and we encourage customers to use them as code samples and learning resources.

This is a moving project (no API maintenance, no deprecation process, etc.) If any of these solutions are found to be useful for the Nuxeo Platform in general, they will be integrated directly into platform, not maintained here.

# License

[Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

# About Nuxeo

Nuxeo Platform is an open source Content Services platform, written in Java. Data can be stored in both SQL & NoSQL databases.

The development of the Nuxeo Platform is mostly done by Nuxeo employees with an open development model.

The source code, documentation, roadmap, issue tracker, testing, benchmarks are all public.

Typically, Nuxeo users build different types of information management solutions for [document management](https://www.nuxeo.com/solutions/document-management/), [case management](https://www.nuxeo.com/solutions/case-management/), and [digital asset management](https://www.nuxeo.com/solutions/dam-digital-asset-management/), use cases. It uses schema-flexible metadata & content models that allows content to be repurposed to fulfill future use cases.

More information is available at [www.nuxeo.com](https://www.nuxeo.com).
