# DDoS attack

A distributed denial-of-service (DDoS) attack is a malicious attempt to disrupt legitimate traffic of a targeted server, service or network, by bombarding it with so much traffic from multiple machines.  Typically, the attacker tries to saturate a system with so many connections and requests until the server it is no longer able to accept new traffic, or becomes so slow that it is effectively unusable. DDoS attacks can cost an organization both time and money while their resources and services are inaccessible. Luckily, servers can be configured to fight back.

## How does a DDoS attack work?
A DDoS attack requires an attacker to gain control of a network of online machines in order to carry out an attack. Usually computers and other devices are infected with malware, turning each one into a bot. The attacker then has remote control over the group of bots, which is called a botnet. Once in control, an attacker can command their botnet to conduct DDoS on a target. In this case, the infected devices are also victims of the attack.

When the IP address of a victim is targeted by the botnet, each bot will respond by sending a large number of requests or very large requests to the target, potentially causing the targeted server or network to overflow capacity, resulting in a denial-of-service to normal traffic. Because each bot is a legitimate Internet device, separating the attack traffic from normal traffic can be difficult.

## How do you know if an attack is happening?

Symptoms of a DDoS attack can resemble non-malicious availability issues, such as technical problems with a particular network or a system administrator performing maintenance. However, the following symptoms could indicate a DoS or DDoS attack:

- Unusually slow network performance

- Unavailability of a particular website

- Unable to access any website

The best way to detect and identify a DDoS attack would be via network traffic monitoring and analysis. Network traffic can be monitored via a firewall, intrusion detection system and system or application logs.

## Using NGINX to prevent DDoS Attacks
Nginx, a highly popular server system for Unix machines, comes with enough built-in functionality to greatly limit the effectiveness of DDoS attacks. These features could handle a DDoS attack by regulating the incoming traffic and by controlling the traffic as it is proxied to backend services.

### Nginx worker connections

One of the important parameters that we tweak is the number of worker process and number of worker connections in the Nginx configuration file /etc/nginx/nginx.conf.

```bash
events {
    worker_connections 50000;
}
```

This setting allows each of the worker process to handle up to 50000 connections.

### Limiting requests rate
Among the many useful tactics worth trying for staving off DDoS attacks, one of the simplest and most effective is the limiting of incoming traffic rates. You can limit the rate at which NGINX accept incoming requests to a normal value for your service from a particular client IP address within a certain period.

```bash
limit_req_zone $binary_remote_addr zone=one:1m rate=30r/m;

server {
    location /wp_login.php {
        limit_req zone=one;
    }
}
```

This example creates a memory area called one, that can hold up to 16,000 (1m) unique IPs, and the 30r/m means that only 30 requests per minute are allowed. Then, we use the limit_req directive to rate limit the connections to a particular location or file, in this case wp_login.php.

### Limiting number of connections

You can limit the number of connections that can be opened by a single client IP address

```bash
limit_conn_zone $binary_remote_addr zone=two:1m;

server {
    location / {
        limit_conn two 10;
    }
}
```

This example creates a memory zone called two to store requests for the specified key, in this case the client IP address, $binary_remote_addr. Then the limit_conn directive sets a maximum of 10 connections from each client IP address.

### Timeout parameters

Slow connections can represent an attempt to keep connections open for a long time. As a result, the server can’t accept new connections.

```bash
server {
    client_body_timeout 5s;
    client_header_timeout 5s;
}
```

In this example, the client_body_timeout directive defines how long Nginx is waiting between the writes of the client body and  client_header_timeout means how long Nginx is waiting between the writes of client header. Both are set to 5 seconds.

### Limit requests size
Similarly, large buffer values or large HTTP requests size make DDoS attacks easier. So, we limit the following buffer values in the Nginx configuration file to mitigate DDoS attacks.

```bash
client_body_buffer_size 200K;
client_header_buffer_size 2k;
client_max_body_size 200k;
large_client_header_buffers 3 1k;
```

### Blacklist IP adresses

If you can identify the client IP addresses being used for an attack, you can blacklist them with the deny directive so that NGINX and NGINX Plus do not accept their connections or requests.

```bash
location / {
    deny 123.123.123.3;
    deny 123.123.123.0/24;
}
```

### Enable sysctl based protection
Additionally we can tweak kernel and system variables in our server. Edit the file /etc/sysctl.conf, and set these two lines to 1 like this:

```bash
net.ipv4.conf.all.rp_filter = 1
net.ipv4.tcp_syncookies = 1
```

The first parameter enables protection against IP spoofing, and the second allows TCP SYN cookie protection.

### Nginx as load balancer

When Nginx is used as load balancer, it is possible to adjust parameters to limit the connections number for each server:

```bash
upstream domain {
server 123.123.123.1:80 max_conns=100;
server 123.123.123.2:80 max_conns=100;
queue 20 timeout=10s;
}
```

Here the max_conns directive specifies the number of connections Nginx can open up for the server. The queue directive limits the number of requests that have been queued when all servers in this group have reached the connection limit. Finally, the timeout directive specifies how long a request can be retained in the queue.