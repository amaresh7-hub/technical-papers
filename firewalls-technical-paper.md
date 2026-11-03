# Firewalls: A Basic Overview

## Introduction

A firewall is a security system that monitors incoming and outgoing
network traffic. It follows a set of security rules to decide which
connections should be allowed and which should be blocked. It acts as a
barrier between a trusted network, such as a home or office network, and
untrusted networks such as the internet. Firewalls help reduce
unauthorized access and protect devices and data.

## How Does a Firewall Work?

When a device sends or receives data, the data travels in small units
called packets. A firewall checks information such as the source IP
address, destination IP address, port number, protocol, and sometimes
the packet's contents. It compares this information with its configured
rules.

-   If the traffic matches an allowed rule, the firewall permits it.
-   If the traffic matches a blocked rule, the firewall rejects it.
-   Stateful firewalls also track active connections and use that
    context when making decisions.

For example, a firewall can allow a web browser to access a website
while blocking an unexpected connection from an unknown source.

## Types of Firewalls

-   **Packet-filtering firewall:** Checks packet details such as IP
    addresses, ports, and protocols. It is fast, but basic versions do
    not inspect the full data content.
-   **Stateful inspection firewall:** Tracks active network connections
    and checks whether packets belong to a valid connection.
-   **Proxy or application firewall:** Acts as an intermediary between a
    user and a service. It can inspect application-level traffic and
    helps prevent direct connections.
-   **Next-generation firewall (NGFW):** Combines traditional firewall
    functions with features such as application control and intrusion
    prevention.

## Hardware and Software Firewalls

-   **Hardware firewall:** A dedicated device, often built into a
    network router or used at an organization's network boundary. It can
    protect multiple devices on a network.
-   **Software firewall:** Runs on an individual computer or server and
    filters that device's network traffic. For example, Windows Firewall
    is built into Windows.

Using both network-level and device-level protection provides additional
layers of security.

## Limitations and Good Practices

A firewall is important, but it cannot prevent every cyberattack. It may
not stop threats caused by unsafe downloads, infected files, stolen
passwords, insider activity, or incorrectly configured rules. Some
attacks can also use traffic that has been allowed through the firewall.

To improve security:

-   Keep firewall rules and software updated.
-   Allow only applications and connections that are necessary and
    trusted.
-   Avoid disabling the firewall without a valid reason.
-   Use antivirus or endpoint protection, strong passwords, and
    multi-factor authentication.
-   Review firewall logs and rules regularly.

## Conclusion

A firewall is an essential layer of network security because it controls
which traffic can enter or leave a device or network. However, it is not
complete protection by itself. It works best when combined with updated
software, safe user practices, and other security controls.

