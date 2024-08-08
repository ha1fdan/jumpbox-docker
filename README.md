# My jumpbox (bastion host) - Config files & scripts

## Get started
Start by cloning the repo: `git clone https://github.com/ha1fdan/jumpbox-docker.git`

### Install dependencies and setup a firewall & SSH

1. [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
2. Setup SSH to be secure:

    /etc/ssh/sshd_config:
    ```yaml
    Include /etc/ssh/sshd_config.d/*.conf
    Port 22
    ListenAddress 0.0.0.0

    HostKey /etc/ssh/ssh_host_ed25519_key

    # Ciphers and keying
    KexAlgorithms -ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group14-sha256
    MACs -umac-64-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1,umac-128@openssh.com,umac-128-etm@openssh.com

    # Logging
    SyslogFacility AUTH
    LogLevel INFO

    # Authentication:
    LoginGraceTime 1m
    PermitRootLogin yes
    PermitEmptyPasswords no
    PasswordAuthentication no
    MaxAuthTries 3
    KbdInteractiveAuthentication no
    UsePAM no
    UseDNS no
    IgnoreRhosts yes
    Compression no 
    AllowStreamLocalForwarding no
    AllowAgentForwarding no

    PrintMotd no
    Banner none
    PrintLastLog yes
    AcceptEnv LANG LC_*

    # Forwarding
    DisableForwarding no
    AllowTcpForwarding yes
    GatewayPorts yes
    X11Forwarding yes
    TCPKeepAlive yes
    PermitTunnel yes

    # override default of no subsystems
    Subsystem	sftp    internal-sftp	/usr/lib/openssh/sftp-server
    ```

3. Install & open ports in firewall:

    1. `apt update && apt install firewalld -y`
    2. Open port 22 TCP and 51820 UDP in firewall:

        ```bash
        firewall-cmd --add-port=22/tcp --permanent
        firewall-cmd --add-port=51821/udp --permanent
        firewall-cmd --reload
        ```
    3. Optionally open port 80, 443, 3000, 5000, 9001 in firewall:

        ```bash
        firewall-cmd --add-port=80/tcp --permanent
        firewall-cmd --add-port=443/tcp --permanent
        firewall-cmd --add-port=3000/tcp --permanent
        firewall-cmd --add-port=5000/tcp --permanent
        firewall-cmd --add-port=9001/tcp --permanent
        firewall-cmd --reload
        ```
4. Reboot to apply all changes.

5. Done.

---

### Information about SSH tunneling

#### Forward from your pc to jumpbox:

`ssh -N -R [jumpbox_port]:127.0.0.1:[local_port] user@jumpbox`

---

#### Forward from jumpbox to your pc:

`ssh -L [local_port]:127.0.0.1:[jumpbox_port] user@jumpbox`

Example for the wg-easy admin webui:

`ssh -N -L 51821:localhost:51821 user@jumpbox`

You can now access the webui on your machine at http://localhost:51821

---