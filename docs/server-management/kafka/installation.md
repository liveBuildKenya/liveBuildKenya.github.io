# Installing Kafka on Ubuntu 20.04
## Prerequisites
To follow along, you will need:

- One Ubuntu 20.04 server and a non-root user with sudo privileges. Follow the steps specified in this guide if you do not have a non-root user set up.
- At least 4GB of RAM on your server. Installations without this amount of RAM may cause the Kafka service to fail.
- OpenJDK 11 installed on your server. To install this version, follow our tutorial How To Install Java with APT on Ubuntu 20.04. Kafka is written in Java, so it requires a JVM.

## Step 1 — Creating a User for Kafka

Because Kafka can handle requests over a network, your first step is to create a dedicated user for the service. This minimizes damage to your Ubuntu machine in the event that someone compromises the Kafka server. We will create a dedicated kafka user in this step.

Logged in as your non-root sudo user, create a user called kafka:

```bash
    sudo adduser kafka
```

Follow the prompts to set a password and create the kafka user.

Next, add the kafka user to the sudo group with the adduser command. You need these privileges to install Kafka’s dependencies:

```bash
    sudo usermod -aG sudo kafka
```

Your kafka user is now ready. Log into the account using su:

```bash
    su -l kafka
```

Now that you’ve created a Kafka-specific user, you are ready to download and extract the Kafka binaries.

## Step 2 — Downloading and Extracting the Kafka Binaries

Let’s download and extract the Kafka binaries into dedicated folders in our kafka user’s home directory.

To start, create a directory in /home/kafka called Downloads to store your downloads:

```bash
    mkdir ~/downloads
```

Use curl to download the Kafka binaries:

```bash
    curl "https://dlcdn.apache.org/kafka/3.1.0/kafka_2.13-3.1.0.tgz" -o ~/downloads/kafka.tgz
```

Create a directory called kafka and change to this directory. This will be the base directory of the Kafka installation:

```bash
    sudo mkdir /usr/src/kafka && cd /usr/src/kafka
```

Extract the archive you downloaded using the tar command:

```bash
    sudo tar -xvzf ~/downloads/kafka.tgz --strip 1
```

We specify the --strip 1 flag to ensure that the archive’s contents are extracted in ~/kafka/ itself and not in another directory (such as /kafka/kafka_2.13-2.6.3/) inside of it.

Now that we’ve downloaded and extracted the binaries successfully, we can start configuring our Kafka server.

## Step 3 — Configuring the Kafka Server

Kafka’s default behavior will not allow you to delete a topic. A Kafka topic is the category, group, or feed name to which messages can be published. To modify this, you must edit the configuration file.

Kafka’s configuration options are specified in server.properties. Open this file with nano or your favorite editor:

```bash
    nano ~/kafka/config/server.properties
```

## Step 4 — Creating Systemd Unit Files and Starting the Kafka Server

In this section, you will create systemd unit files for the Kafka service. This will help you perform common service actions such as starting, stopping, and restarting Kafka in a manner consistent with other Linux services.

Zookeeper is a service that Kafka uses to manage its cluster state and configurations. It is used in many distributed systems. If you would like to know more about it, visit the official Zookeeper docs.

Create the unit file for zookeeper:

```bash
    sudo nano /etc/systemd/system/zookeeper.service
```

Enter the following unit definition into the file:

```bash

    [Unit]
    Requires=network.target remote-fs.target
    After=network.target remote-fs.target

    [Service]
    Type=simple
    User=kafka
    ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
    ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
    Restart=on-abnormal

    [Install]
    WantedBy=multi-user.target
```

The [Unit] section specifies that Zookeeper requires networking and the filesystem to be ready before it can start.

The [Service] section specifies that systemd should use the zookeeper-server-start.sh and zookeeper-server-stop.sh shell files for starting and stopping the service. It also specifies that Zookeeper should be restarted if it exits abnormally.

After adding this content, save and close the file.

Next, create the systemd service file for kafka:

```bash
    sudo nano /etc/systemd/system/kafka.service
```

Enter the following unit definition into the file:

```bash
    [Unit]
    Requires=zookeeper.service
    After=zookeeper.service

    [Service]
    Type=simple
    User=kafka
    ExecStart=/bin/sh -c '/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties > /home/kafka/kafka/kafka.log 2>&1'
    ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
    Restart=on-abnormal

    [Install]
    WantedBy=multi-user.target
```

The [Unit] section specifies that this unit file depends on zookeeper.service. This will ensure that zookeeper gets started automatically when the kafka service starts.

The [Service] section specifies that systemd should use the kafka-server-start.sh and kafka-server-stop.sh shell files for starting and stopping the service. It also specifies that Kafka should be restarted if it exits abnormally.

Now that you have defined the units, start Kafka with the following command:

```bash
    sudo systemctl start kafka
```

You now have a Kafka server listening on port 9092.

You have started the kafka service. But if you rebooted your server, Kafka would not restart automatically. To enable the kafka service on server boot, run the following commands:

```bash
 sudo systemctl enable zookeeper
```

## Step 6 — Hardening the Kafka Server
With your installation complete, you can remove the kafka user’s admin privileges. Before you do so, log out and log back in as any other non-root sudo user. If you are still running the same shell session that you started this tutorial with, type exit.

Remove the kafka user from the sudo group:

```bash
    sudo deluser kafka sudo
```

To further improve your Kafka server’s security, lock the kafka user’s password using the passwd command. This makes sure that nobody can directly log into the server using this account:

```bash
    sudo passwd kafka -l
```

At this point, only root or a sudo user can log in as kafka by typing in the following command:

```bash
    sudo su - kafka
```

In the future, if you want to unlock it, use passwd with the -u option:

```bash
    sudo passwd kafka -u
```

## Step 7 — Installing KafkaT (Optional)

KafkaT is a tool that Airbnb developed. It makes it easier to view details about your Kafka cluster and perform certain administrative tasks from the command line. But because it is a Ruby gem, you will need Ruby to use it. You will also need the build-essential package to build the other gems that KafkaT depends on. Install them using apt:

```bash
    sudo apt install ruby ruby-dev build-essential
```

You can now install KafkaT using the gem command:

```bash
    sudo CFLAGS=-Wno-error=format-overflow gem install kafkat
```

The “Wno-error=format-overflow” compilation flag is required to suppress Zookeeper’s warnings and errors during kafkat’s installation process.

KafkaT uses .kafkatcfg as the configuration file to determine the installation and log directories of your Kafka server. It should also have an entry pointing KafkaT to your ZooKeeper instance.

Create a new file called .kafkatcfg:

```bash
    nano ~/.kafkatcfg
```

Add the following lines to specify the required information about your Kafka server and Zookeeper instance:

```json
{
  "kafka_path": "~/kafka",
  "log_path": "/home/kafka/logs",
  "zk_path": "localhost:2181"
}
```

You are now ready to use KafkaT. For a start, here’s how you would use it to view details about all Kafka partitions:

```bash
    kafkat partitions
```



