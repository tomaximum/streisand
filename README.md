![Streisand Logo](https://missingm.co/streisand.jpg "Automate the effect")

Streisand
=========

**Silence censorhip. Automate the [effect](http://en.wikipedia.org/wiki/Streisand_effect).**

Streisand is an advanced anti-censorship toolkit built on the [Ansible](http://www.ansible.com/home) open source automation platform that does the following:

* Makes it possible for individuals and organizations to quickly provision new servers running the latest version of [Debian](https://www.debian.org/) and a wide variety of VPN and encrypted networking software
* Helps users configure their computers and mobile devices to securely route their Internet traffic through these servers and evade blocking attempts
* Removes the pain involved for both groups

Features
--------
* Nginx powers a password-protected and encrypted Gateway that serves as the starting point for new users. The Gateway is accessible over SSL, or as a Tor [hidden service](https://www.torproject.org/docs/hidden-services.html.en). Each Streisand server is entirely self-contained, and includes absolutely everything that users need in order to get back online as quickly as possible without any external dependencies.
  * Beautiful, custom, step-by-step client configuration instructions are generated for each new server that Streisand creates. Users can quickly access these instructions through any web browser. The instructions are responsive and look fantastic on mobile phones.
  * Streisand mirrors local copies of all necessary client software and it is made available for download directly from the Gateway, rendering any attempted censorship of the default client download locations completely ineffective. The integrity of mirrored software is ensured using SHA-256 checksums, or by verifying GPG signatures if the project provides them. This protects users from downloading corrupted files.
  * All ancillary files, such as OpenVPN configuration profiles, are also available via the Gateway
  * The Gateway can be accessed as a Tor hidden service for users who wish to anonymously download configuration instructions on someone else's behalf. Current Tor users can also benefit from the additional anti-censorship utilities Streisand sets up to transfer large files or to handle other traffic (e.g. BitTorrent) that isn't appropriate for the Tor network.
  * A unique password, SSL certificate, and SSL private key are generated for each Streisand Gateway. The custom Gateway connection instructions contain the random Gateway password, SSL serial number, and complete SSL fingerprints for the Gateway certificate so users can be sure they are connecting to the right location. These Gateway instructions are transferred via SSH at the conclusion of Streisand's execution, and they can be given to friends, family, fellow activists, or even total strangers.
* Rapid creation and configuration of new servers. Several providers are supported directly:
  * [Amazon EC2](http://aws.amazon.com/ec2/)
  * [DigitalOcean](https://www.digitalocean.com/)
  * [Linode](https://www.linode.com/)
  * [Rackspace](http://www.rackspace.com/)

  Streisand can also run on any new fleet of Debian 7 servers regardless of provider, and **hundreds** of instances can be configured simultaneously using this method.
* Distinct services and multiple daemons provide an enormous amount of flexibility. If one connection method gets blocked there are numerous options available, most of which are resistant to Deep Packet Inspection.

  * All of the connection methods (including L2TP/IPsec and direct OpenVPN connections) are effective against the type of blocking Turkey has been experimenting with
  * OpenSSH, OpenVPN (wrapped in stunnel), Shadowsocks, and Tor (with obfsproxy and the obfs3 or ScrambleSuit pluggable transports) are all currently effective against China's Great Firewall

* Server setup is completely automated. There are no manual steps whatsoever. Every task has also been thoroughly documented and given a detailed description. Streisand is simultaneously the most complete HOWTO in existence for the setup of all of the software it installs, and also the antidote for ever having to do any of this by hand again.
* All software runs on ports that have been deliberately chosen to make simplistic port blocking unrealistic without causing massive collateral damage. OpenVPN, for example, does not run on its default port of 1194, but instead uses port 636, the standard port for LDAP/SSL connections that are beloved by companies worldwide.
  * *L2TP/IPsec is a notable exception to this rule because the ports cannot be changed without breaking client compatibility*
* The IP addresses of connecting clients are never logged. There's nothing to find if a server gets seized or shut down.

Services Provided
-----------------
* L2TP/IPsec using [strongSwan](http://strongswan.org/) and [xl2tpd](http://www.xelerance.com/software/xl2tpd/)
  * A randomly chosen pre-shared key and password are generated
  * Windows, OS X, Android, and iOS users can all connect using the native VPN support that is built into each operating system without installing any additional software  
  * *Note: Streisand does not install L2TP/IPsec on Amazon EC2 servers by default because the instances cannot bind directly to their public IP addresses which makes IPsec routing nearly impossible.*
* [OpenSSH](http://www.openssh.com/)
  * An unprivileged forwarding user and SSH keypair are generated for [sshuttle](https://github.com/apenwarr/sshuttle) and SOCKS capabilities. Windows and Android SSH tunnels are also supported.
  * [Tinyproxy](https://banu.com/tinyproxy/) is installed and bound to localhost. It can be accessed over an SSH tunnel by programs that do not natively support SOCKS and that require an HTTP proxy, such as Twitter for Android.
* [OpenVPN](https://openvpn.net/index.php/open-source.html)
  * Self-contained "unified" .ovpn profiles are generated for easy client configuration using only a single file
  * Multiple clients can easily share the same certificates and keys, but five separate sets are generated by default
  * Client DNS resolution is handled via [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) to prevent DNS leaks
  * TLS Authentication is enabled which helps protect against active probing attacks. Traffic that does not have the proper HMAC is simply dropped.
  * The [Dante](http://www.inet.no/dante/) proxy server is set up as a workaround for a [bug](https://bugzilla.mozilla.org/show_bug.cgi?id=947801) in Firefox for Android
* [Shadowsocks](http://shadowsocks.org/en/index.html)
  * A QR code is generated that allows the Android and iOS clients to be automatically configured by simply taking a picture. You can tag '8.8.8.8' on that concrete wall, or you can glue the Shadowsocks instructions and some QR codes to it instead!
* [Stunnel](https://www.stunnel.org/index.html)
  * Listens for and wraps OpenVPN connections. This makes them look like standard SSL traffic and allows OpenVPN clients to successfully establish tunnels even in the presence of Deep Packet Inspection.
  * Unified profiles for stunnel-wrapped OpenVPN connections are generated alongside the direct connection profiles. Detailed instructions are also generated.
  * The stunnel certificate and key are exported in PKCS #12 format so they are compatible with other SSL tunneling applications. Notably, this allows [OpenVPN for Android](https://play.google.com/store/apps/details?id=de.blinkt.openvpn) to tunnel its traffic through [SSLDroid](https://play.google.com/store/apps/details?id=hu.blint.ssldroid). OpenVPN in China on a mobile device? Yes!
* [Tor](https://www.torproject.org/)
  * A [bridge relay](https://www.torproject.org/docs/bridges) is set up with a random nickname
  * [Obfsproxy](https://www.torproject.org/projects/obfsproxy.html.en) is installed and configured, including support for the obfs3 and [ScrambleSuit](http://www.cs.kau.se/philwint/scramblesuit/) pluggable transports

Installation
------------

### Prerequisites ###
* The Streisand playbook should be run on a BSD, Linux, or OS X system
* You must have an SSH key present in ~/.ssh/id_rsa.pub
* The [pip](https://pip.pypa.io/en/latest/) package management system for Python should be installed
  * On Debian and Ubuntu

            sudo apt-get install python-pip
  * On Fedora

            sudo yum install python-pip
  * On OS X

            sudo easy_install pip
* [Ansible](http://www.ansible.com/home) should also be ready to go
  * On OS X Mavericks

            sudo CFLAGS=-Qunused-arguments CPPFLAGS=-Qunused-arguments pip install ansible
  * On BSD, Linux, or earlier versions of OS X

            sudo pip install ansible
* Any necessary Python libraries for your chosen cloud provider must be present if you are going to take advantage of Streisand's ability to create new servers using a supported API
  * Amazon EC2

            sudo pip install boto
  * DigitalOcean

            sudo pip install dopy
  * Linode

            sudo pip install linode-python
  * Rackspace Cloud

            sudo pip install pyrax

### Execution ###
1. Clone the Streisand repository and enter the directory

        git clone https://github.com/jlund/streisand.git && cd streisand
2. Execute the playbook for your chosen cloud provider
   * Amazon EC2

            ansible-playbook new-amazon-server.yml
   * DigitalOcean

            ansible-playbook new-digitalocean-server.yml
   * Linode

            ansible-playbook new-linode-server.yml
   * Rackspace

            ansible-playbook new-rackspace-server.yml
3. Follow the customized prompts to choose the physical region for the server and its name. You will also be asked to enter API information for your chosen provider.
4. Wait for the setup to complete (this usually takes around five minutes) and look for the corresponding HTML file in the 'generated-docs' folder in the Streisand repository directory. This file will explain how to connect to the Gateway (over SSL or via Tor) and all instructions, files, mirrored clients, and keys for the new server will be located there. You are all done!

You can also run Streisand on any number of new Debian 7 servers. Dedicated hardware? Great! Esoteric cloud provider? Awesome! To do this, simply edit the 'inventory' file and uncomment the final two lines. Replace the sample IP with the address (or addresses) of the servers you wish to configure. Then run the Streisand playbook directly:

    ansible-playbook streisand.yml

The servers should be accessible using SSH keys, and 'root' is used as the connecting user by default (though this is simple to change in the streisand.yml file, if necessary).

Acknowledgements
----------------
Huge thanks to [Paul Wouters](https://nohats.ca/) of [The Libreswan Project](https://libreswan.org/) for his generous help troubleshooting the L2TP/IPsec setup.

A massive round of applause to [Trevor Smith](https://github.com/trevorsmith) for his major contributions to the project. He suggested the Gateway approach and developed the HTML template that inspired me to take things to the next level before Streisand's public release. I also appreciated the frequent use of his iPhone while testing various clients.

[Corban Raun](https://github.com/CorbanR) was kind enough to lend me a Windows laptop that let me test and refine the instructions for that plaform, and he was a big supporter of the project from the very beginning.

I also listened to [Starcadian's](http://starcadian.com/) 'Sunset Blood' album approximately 300 times on repeat while working on this.
