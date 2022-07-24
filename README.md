# barabbas v1.1

<p align="justify">Barabbas is an open source minimalistic web server, used for file transfer (download; upload).
It supports authentication (client; server), encryption (data transfer), access restriction (client) and file integrity checking (validation).
Works, out of the box, with Python3 on major Linux-based OS distributions.

Supports the following file upload functionalities:
  * ```HTTP POST (Multipart/form-data)```
  * ```HTTP PUT```
  * ```HTTP BITS_POST``` (Windows)</p>

Screenshot
----

<p align="center">
  <img src="https://github.com/lighthouseitsecurity/barabbas/wiki/images/barabbas_server_default.jpg">
</p>

<p align="justify">Links to additional screenshots can be found under "Usage" section, further below.</p>

Requirements
----

1. Python3
2. ```openssl``` binary
3. ```iptables``` binary
4. ```which``` binary

<p align="justify">NOTE: All of the requirements are installed, by default, on all tested major Linux-based OS distributions (with the exception of Arch).</p>

Installation
----

1. download ```barabbas```
2. ```chmod 700 barabbas```
3. ```chown root:root barabbas```
4. move ```barabbas``` to directory in ```PATH``` (e.g. ```/usr/local/sbin/```)

Installer script:
```
sudo su
wget https://raw.githubusercontent.com/lighthouseitsecurity/barabbas/main/barabbas -O /tmp/barabbas && chmod 700 /tmp/barabbas && chown root:root /tmp/barabbas && mv /tmp/barabbas /usr/local/sbin/barabbas
```

Usage
----

```
usage: barabbas [-h] [-i LOCAL_IFACE_IP] [-p PORT] [-up USERNAME:PASSWORD] [-da] [-c X509_CERT_PATH] [-cn TEMP_X509_CN] [-de] [-w WEB_ROOT_PATH] [-dc] [-a CLIENT_IP_ADDR] [-di] [-dh] [-v]

optional arguments:
  -h, --help            show this help message and exit
  -i LOCAL_IFACE_IP, --interface LOCAL_IFACE_IP
                        Use specified IP address of local network interface as web server listening interface (default: any/all)
  -p PORT, --port PORT  Use specified port as web server listening port (TCP; default: 443)
  -up USERNAME:PASSWORD, --user-pass USERNAME:PASSWORD
                        Use specified credentials for client authentication (HTTP basic)
  -da, --disable-authn  Disable client authentication (HTTP basic; default: enabled)
  -c X509_CERT_PATH, --x509-cert X509_CERT_PATH
                        Use specified server-side X.509 certificate for web server authentication and encrypted communication (PEM; TLS v1.2)
  -cn TEMP_X509_CN, --temp-x509-cn TEMP_X509_CN
                        Use specified Common Name (CN) for autogenerated (temporary) server-side X.509 certificate for web server authentication and encrypted communication (PEM; TLS
                        v1.2; default: OS hostname)
  -de, --disable-encr   Disable web server authentication and encrypted communication (TLS v1.2; default: enabled)
  -w WEB_ROOT_PATH, --web-root-dir WEB_ROOT_PATH
                        Use specified directory as web server web root directory (default: current directory)
  -dc, --disable-chksm  Disable web server side file checksums (MD5; default: enabled)
  -a CLIENT_IP_ADDR, --allow-access CLIENT_IP_ADDR
                        Allow only specified IP address(es) to access web server (comma separated list; default: any/all)
  -di, --disable-IP-fw  Disable IP layer access restriction (iptables; default:enabled)
  -dh, --disable-app-fw
                        Disable application layer access restriction (HTTP; default:enabled)
  -v, --version         Display version information
```

All options can be specified:
* by hardcoding them in the script (marked section; beginning of file)
  * will override respective default values
* via CLI
  * will override respective default and hardcoded values

Concrete examples of various server and client configurations, along with additional screenshots, can be found on the wiki:
* [Server](https://github.com/lighthouseitsecurity/barabbas/wiki/1_0_0___Server)
* [Linux CLI clients](https://github.com/lighthouseitsecurity/barabbas/wiki/2_1_0___Linux)
* [Windows CLI clients](https://github.com/lighthouseitsecurity/barabbas/wiki/2_2_0___Windows)

Story/Idea
----

<p align="justify">During an assignment i tested a locked down/hardened system (Windows). Some data was required to be transferred to another system (Linux) for further analysis. Although none of the usual (pentest) file transfer methods were usable, one of the most obvious ways to transfer files was not locked down - accessing a controlled web server via a system's client (web browser/CLI).</p>

<p align="justify">Once the solution to the problem became obvious on the theoretical level i thought that putting it into practice would be quick and simple (i.e. without any web server configuration hassle). It turned out that none of the pentest tools, i was using/aware of, allowed fast and simple setup of a web server, which supported file upload.</p>

<p align="justify">Up to this point, in most HTTP file transfer scenarios, i was using the built-in Python simple web server, which supported (only) file download, (only) cleartext communication and lacked an access restriction functionality. To address all of these issues, I had the idea of "upgrading" this default built-in Python web server, for quite a while. The described event resulted with the motivation and initiative to develop such a tool.</p>

Philosophy
----

The tool was developed according to the following high-level guidelines:
* simple to setup
  * built on top of a robust and widely used platform (Python3)
  * easy and fast to deploy/install
    * download and run
      * zero dependencies (i.e. must work out of the box with plain Python3)
      * single file
      * must work on default installations of the most widely used Linux distributions (i.e. without requiring any additional OS configuration changes)
* functionality wise, straight to the point
  * do (only) one thing (file transfer)
  * additionally, only implement relevant functionalities (i.e. avoid bloat)
* configuration/usage wise, straightforward, simple, flexible and economic
  * overall, keep any additional typing/configuring on the bare minimum (i.e. take out the monkey work out of the equation, as much as possible)
    * default config - use the most meaningful settings
      * just run the tool (i.e. avoid any extra typing)
    * support simple modification of default values (hardcoding), to (permanently) override them
      * just run the tool, without any arguments (i.e. avoid any extra typing)
    * if/when required, override hardcoded (and/or default) settings, via CLI
      * use short and intuitive switches (i.e. minimize any required typing)
* quality oriented
  * test all functionalities in typical use case scenarios
    * anticipate user (mis)behavior and solve issues, caused by it (i.e. (attempt to) avoid (all) related program crashes)
  * make as lightweight as possible
  * make as reliable as possible
  * address all identified security issues
* simple to modify/upgrade
  * make source code easy to understand and follow
    * follow [PEP 8 coding guidelines](https://pep8.org/), as much as possible
      * be consistent throughout the whole code, as much as possible (and where meaningful)
      * put meaningful comments (throughout all code)

Implemented functionalities
----

* [MAIN] file transfer (download; upload)
  * file download functionality, using ```HTTP GET```
  * file upload functionality, using:
    * ```HTTP POST (Multipart/form-data)```
    * ```HTTP PUT```
    * ```HTTP BITS_POST``` (Windows)
  * interaction with file download/upload functionalities via both CLI and GUI (Windows; Linux)
    * drag and drop via GUI (file upload; Browse button)
  * multi user support (multi threading)
* [OPTIONAL] authentication
  * client authentication via HTTP Basic Access authentication
  * web server authentication via server-side X.509 certificate
* [OPTIONAL] (communication channel) encryption/data integrity
  * handled by (Python3) TLS v1.2 + server-side X.509 certificate
    * uses (only) A grade ciphers
* [OPTIONAL] access restriction
  * implemented at two different layers (OSI reference model)
    * Application layer - restricts access at HTTP level
      * upside - does not change any OS settings
      * downside - allows interaction of unauthenticated clients with server (Python3) via HTTP
        * the reason why an additional restriction layer (i.e. beneath the Application layer; IP layer), was implemented
    * IP layer - restricts access using the ```iptables``` OS utility
      * upside - more secure than the Application layer solution
        * disallows (direct) interaction of unauthenticated clients with server (Python3) via HTTP
      * downside - modifies existing OS settings (firewall rules)
        * although tested for most common "layer 8 misbehavior", it still may be possible to cause unintended damage to the system, by tool misusage (i.e. via program crashes)
    * any combination will work: both enabled (default); one enabled, other disabled; both disabled
* [OPTIONAL] file integrity checks
  * generally, HTTP is not the best/most reliable method to transfer files
    * to provide the ability to validate the outcome of file transfers, file checksums (MD5) have been implemented on the server side
      * applied to all files, served by the web server
      * applied to the file, successfully uploaded to the server side (included in the server response)

* NOTE: the following functionalities were taken into consideration, but were not implemented (the current ones serve the tool's purpose; some may be implemented in future release(s)):
  * X.509 client certificates 
    * second factor of client authentication
      * overall, seemed like an overkill (considering the tool's purpose)
      * useful in certain scenarios
      * adds an additional security layer, by disallowing interaction of unauthenticated clients with server (Python3) via HTTP
  * event logging to file (in addition to stdout)
    * overall, seemed like an overkill (considering the tool's purpose)
  * additional file upload methods
    * ```HTTP POST (application/x-www-form-urlencoded)```
  * support for additional operating systems (e.g. Windows/Commando VM)

Intended audience
----

<p align="justify">This tool is intended to be primarily used for penetration testing/red teaming purposes.
I believe that other IT-related professions, that routinely perform file transfers, between various systems (sysadmins/devops/developers), may find it useful, as well as HTB/CTF players/IT security enthusiasts.
Besides those uses, it may also come in handy for everyday computer-related activities (e.g. fast and simple file transfer, between different systems/VMs/containers, in cases where no "proper" file transfer service is installed and a quick (temporary) solution is required).</p>

Intended usage
----

<p align="justify">For penetration testing/red teaming purposes, the idea is to start the web server (when required), perform the file upload/download activity, followed by (immediately) stoping the web server, once the file transfer is finished (i.e. start server ➔ dl/ul file(s) ➔ stop server).
In other words, the tool is intended to be used only on demand (i.e. NOT left running in the background, when idle).

DISCLAIMER: **The author of the tool is NOT responsible for any damage, caused by using this tool. Use this tool at your own risk.** The underlying Python3 platform can be interacted with, in certain configurations/scenarios. This may result with execution of arbitrary Python code or external programs, [as stated](https://github.com/python/cpython/blob/3.9/Lib/http/server.py) by its vendor, Python Software Foundation.</p>

Additional remarks/notes:
* **do NOT use** this tool in any **production environment**
* **do NOT leave** this tool **running, when idle**
* **allow access** to the server **only from legitimate client systems** (via the built-in IP layer access restriction functionality), whenever possible
* **enable** the **encryption** functionality (i.e. use HTTPS), whenever possible
* **enable** the **authentication** functionality, whenever possible
  * use random unique strong passwords (e.g. use the built-in random credential generator) 
* this tool was NOT designed for usage over extended time periods
  * may result with stability issues (tool restart required)
* this tool is NOT a full-blown web server
  * will not match the performance of one

Tested environments
----

* [SERVER]
  * Ubuntu 20.04.2 LTS + Python 3.8.5
  * working, but not thoroughly tested:
    * Kali Rolling 2021.1 + Python 3.9.2
    * Arch (May 2021) + Python 3.9.5
    * Debian 10 + Python 3.7.3
* [CLIENT]
  * covered separately (links under "Usage" section, further above)

Updates
----

<p align="justify">No major updates of this tool are planned, at the time of writing.
Any identified issues/bugs will be addressed/solved, when possible (timewise).</p>
<p align="justify">Based on user feedback, additional features may be implemented.</p>
<p align="justify">The Wiki will be updated, when possible (no rough ETA). It is planned to document all supported file upload methods, as well as (currently undocumented) file download methods. These changes will also include updates to current content (text; screenshots).</p>
