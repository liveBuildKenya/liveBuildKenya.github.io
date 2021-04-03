# Handling ~4000 Concurrent Connections in Apache
By default, Apache web server is configured to support 150 concurrent connections. As your website traffic increases, Apache will start dropping additional requests and this will spoil customer experience. Here’s how to increase max connections in Apache, to support high traffic websites.

## Install MPM module
The MPMs are used to change the basic functionality of the web server. The MPM you use is responsible for the entire HTTP session, starting from listening on the network, taking requests in, and handling those requests.

1. Prefork MPM: Every request gets its own (memory-separated) process.
2. Worker MPM: Multi-threaded Apache, uses threads instead of processes, is generally faster than prefork and might use less memory.
3. Event MPM: threaded like the Worker MPM, but is designed to allow more requests to beserved simultaneously by passing off some processing work to supporting threads, freeing up the main threads to work on new requests.

We need to install MPM Apache module to be able to serve lots of concurrent connections. You can use mpm_worker or mpm_event module for this, instead of mpm_prefork module which consumes a lot of memory.

You can easily install MPM module in Apache with following command

CentOS7/RHEL7:

    adjust /etc/httpd/conf.modules.d/00-mpm.conf
    Comment the line LoadModule mpm_prefork_module modules/mod_mpm_prefork.so by adding # in front of it
    Uncomment the line LoadModule mpm_worker_module modules/mod_mpm_worker.so by removing # in front of it.

Ubuntu/Debian:

Disable the prefork module

``` bash
    a2dismod mpm_prefork
```

Enable the worker module

``` bash
    a2enmod mpm_worker
```

or enable the event module

``` bash
    a2enmod mpm_event
```

## Increase Max Connections in Apache
Open the configuration files for the apache modules.

CentOS/RHEL

    sudo nano /etc/httpd/conf.modules.d/00-mpm.conf

Ubuntu/Debian
    
    sudo vi /etc/apache2/mods-available/mpm_worker.conf


Adjust the following lines

    ServerLimit 250
    StartServers 10
    MinSpareThreads 75
    MaxSpareThreads 250
    ThreadLimit 64
    ThreadsPerChild 32
    MaxRequestWorkers 8000
    MaxConnectionsPerChild 10000

## Restart Apache Server

Ubuntu/Debian

``` bash
    sudo service apache2 restart
```

CentOS/Redhat/Fedora

``` bash
    sudo service httpd restart
```
