---
layout: post
title: "Configuring Arch Linux with netctl and eap-tls for eduroam"
date:  2015-08-29
category: guides
tags: 
- arch linux
- netctl
- eduroam
- wireless
- eap-tls
---

## Introduction

Last semester I was taking a course in embedded systems. As a course assignment,
I was working on a project creating a remote controlled robot. We figured that
if the robot had a strong WiFi connection, we would be able to send a video feed
and other bandwidth-demanding data to and from the robot.

We were building the robot on campus at Umeå University. Eduroam has great
coverage all around campus, so we decided to try using Eduroam. However, Umeå
University uses the somewhat cumbersome EAP-TLS security protocol for
connecting, authorizing and encrypting traffic. 

I only found guides for configuring eduroam EAP-TTLS *(notice the extra **T**)*
so I had to take a deep dive through the manuals and find out how to do it
myself.

## Acquiring certificates (Umeå university only)

The instructions here are specific to Umeå University, but other universities
may have a similar process.

You will need two certificates for eap-tls to work. The server certificate are
used to ensure that nobody can pretend to be eduroam (and snoop on your
traffic), and the client certificate is used by the eduroam router to ensure
that you really are authorized to connect.

1. Go to [www.eduroam.umu.se](https://www.eduroam.umu.se) and log in using your
student credentials (CAS ID)
2. Manually download the certificates ***(Important: write down the password
somewhere)***

## Prerequisites

* netctl
* wpa\_supplicant
* server and client certificates

## Setup

*All the below steps need to be performed as root*

1. Place the private certificate (\*.p12) in /etc/netctl/. *(The file
can be placed anywhere, but for clarity we will place it in the netctl catalogue
in this guide)
2. Place the CA certificates (*.crt) in
/etc/ca-certificates/trust-source/anchors/
3. Run `trust --extract compat`
4. Create a file /etc/netctl/eduroam with the following contents:

~~~
Description='Eduroam connection'
Interface=wlan0
Connection=wireless
Security='wpa-configsection'
IP='dhcp'
WPAConfigSection=(
    'ssid="eduroam"'
    'key_mgmt=WPA-EAP'
    'eap=TLS'
    'proto=WPA RSN'
    'identity="ABCD0123@umu.se"'
    'private_key="/etc/netctl/umu_eduroam_ABCD0123.p12"'
    'private_key_passwd="YOURPASSWORD"'
    'ca_cert="/etc/ca-certificates/extracted/cadir/UmU_eduroam_MasterRoot_CA.pem"'
    'ca_cert2="/etc/ca-certificates/extracted/cadir/UmU_eduroam_MasterRoot_CA.pem"'
) 
~~~

You will have to edit some fields in the configuration:

* identity - your student email *(\<CAS_ID\>@umu.se for students at Umeå
University)*
* private_key - filepath to your private key
* private_key_passwd - the password for your private key
* ca_cert - file path to your eduroam master certificate *(does not have to be
edited if you are a student at Umeå University)*
* ca_cert2 - same as ca_cert

## Connect

Try running using `netctl eduroam start` as root. If you want to permanently enable the
connection (as in connecting on boot), use `netctl eduroam enable`. However, I
discovered that netctl only tries to start the connection a few times at reboot,
and never retries. For reliable reconnection, a script should be used, or
manually run `netctl eduroam start` whenever needed.

## More

[Configuring and using netctl](https://wiki.archlinux.org/index.php/Netctl)


