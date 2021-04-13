# Secure Linux Server From Hackers

Every server is vulnerable from hackers especially if you have not taken any action to prevent this from happening.
So what can you do to protect yourself from a hacker?

## Enable Automatic Updates

Most servers are hacked because they did not have any security updates. To update manually you first check your repositories for updates by running 
the following command as the root user.

```bash
    apt update
```

To upgrade you run the command

```bash
    apt upgrade
```

To install updates automatically you run:

```bash
    apt install unattended-upgrades
```

to setup automatic upgrades you run:

```bash
    dpkg-reconfigure --priority=low unattended-upgrades
```

## Limited User Account
The root user has the ability to make damaging changes to your linux system. So lets limit the user account.
As the root user run the following command:

```bash
    adduser <username>
``` 
Then add user to the sudo group

```bash
    usermod -aG sudo <username>
```
## SSH Keys
We will create an authentication key-pair and save it to the associated user:

```bash
    mkdir ~/.ssh && chmod 700 ~/.ssh
```

On your users machine run the following command to generate the key pair

```bash
    ssh-keygen -b 4096
```

the copy the public key to the server

Windows:

```bash
    scp $env:USERPROFILE/.ssh/id_rsa.pub <username>@<server-address>:<path-to-authorized-keys>
```

Linux:

```bash
    ssh-copy-id <username>@<server-address>
```

MacOS:

```bash
    scp ~/.ssh/id_rsa.pub <username>@<server-address>:<path-to-authorized-keys>
```

## Harden SSH
We will stop the use of password to login to our server. We edit the file /etc/ssh/sshd_config

First copy the file to a backup

```bash
    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

Then edit the file

```bash
    sudo nano /etc/ssh/sshd_config
```

update the contents to:

        #       $OpenBSD: sshd_config,v 1.103 2018/04/09 20:41:22 tj Exp $

        # This is the sshd server system-wide configuration file.  See
        # sshd_config(5) for more information.

        # This sshd was compiled with PATH=/usr/bin:/bin:/usr/sbin:/sbin

        # The strategy used for options in the default sshd_config shipped with
        # OpenSSH is to specify options with their default value where
        # possible, but leave them commented.  Uncommented options override the
        # default value.

        Include /etc/ssh/sshd_config.d/*.conf

        Port 35020
        #AddressFamily any
        #ListenAddress 0.0.0.0
        #ListenAddress ::

        #HostKey /etc/ssh/ssh_host_rsa_key
        #HostKey /etc/ssh/ssh_host_ecdsa_key
        #HostKey /etc/ssh/ssh_host_ed25519_key

        # Ciphers and keying
        #RekeyLimit default none

        # Logging
        #SyslogFacility AUTH
        #LogLevel INFO

        # Authentication:

        #LoginGraceTime 2m
        PermitRootLogin no
        #StrictModes yes
        MaxAuthTries 4
        #MaxSessions 10

        #PubkeyAuthentication yes

        # Expect .ssh/authorized_keys2 to be disregarded by default in future.
        #AuthorizedKeysFile     .ssh/authorized_keys .ssh/authorized_keys2

        #AuthorizedPrincipalsFile none

        #AuthorizedKeysCommand none
        #AuthorizedKeysCommandUser nobody

        # For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
        #HostbasedAuthentication no
        # Change to yes if you don't trust ~/.ssh/known_hosts for
        # HostbasedAuthentication
        #IgnoreUserKnownHosts no
        # Don't read the user's ~/.rhosts and ~/.shosts files
        #IgnoreRhosts yes

        # To disable tunneled clear text passwords, change to no here!
        PasswordAuthentication no
        PermitEmptyPasswords no

        # Change to yes to enable challenge-response passwords (beware issues with
        # some PAM modules and threads)
        ChallengeResponseAuthentication no

        # Kerberos options
        #KerberosAuthentication no
        #KerberosOrLocalPasswd yes
        #KerberosTicketCleanup yes
        #KerberosGetAFSToken no

        # GSSAPI options
        #GSSAPIAuthentication no
        #GSSAPICleanupCredentials yes
        #GSSAPIStrictAcceptorCheck yes
        #GSSAPIKeyExchange no

        # Set this to 'yes' to enable PAM authentication, account processing,
        # and session processing. If this is enabled, PAM authentication will
        # be allowed through the ChallengeResponseAuthentication and
        # PasswordAuthentication.  Depending on your PAM configuration,
        # PAM authentication via ChallengeResponseAuthentication may bypass
        # the setting of "PermitRootLogin without-password".
        # If you just want the PAM account and session checks to run without
        # PAM authentication, then enable this but set PasswordAuthentication
        # and ChallengeResponseAuthentication to 'no'.
        UsePAM yes

        #AllowAgentForwarding yes
        #AllowTcpForwarding yes
        #GatewayPorts no
        X11Forwarding yes
        #X11DisplayOffset 10
        #X11UseLocalhost yes
        #PermitTTY yes
        PrintMotd no
        #PrintLastLog yes
        #TCPKeepAlive yes
        #PermitUserEnvironment no
        #Compression delayed
        ClientAliveInterval 300
        #ClientAliveCountMax 3
        #UseDNS no
        #PidFile /var/run/sshd.pid
        #MaxStartups 10:30:100
        #PermitTunnel no
        #ChrootDirectory none
        #VersionAddendum none

        # no default banner path
        #Banner none

        # Allow client to pass locale environment variables
        AcceptEnv LANG LC_*

        # override default of no subsystems
        Subsystem sftp  /usr/lib/openssh/sftp-server

        # Example of overriding settings on a per-user basis
        #Match User anoncvs
        #       X11Forwarding no
        #       AllowTcpForwarding no
        #       PermitTTY no
        #       ForceCommand cvs server

        #Permitted users
        AllowUsers kscadminlive ianminara markmuigai

        #SSH protocal to use
        Protocol 2

Restart the ssh service:

```bash
    sudo service restart ssh
```
## Firewall
Check which ports are being used by the server:

```bash
    sudo ss -tupln
```

Then, check if your firewall is active:

```bash
    sudo ufw status
```
Allow the port that is enabled:

```bash
    sudo ufw allow <port-number>
```

