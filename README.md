# Setting up FreeIPA with Docker and Docker Compose

This guide will walk you through the process of setting up FreeIPA using Docker Compose. FreeIPA is an open-source identity management system that provides centralized authentication, authorization, and account information for Linux and Unix systems.

## Prerequisites

Before you begin, you'll need to have the following installed on your system:

- Docker
- Docker Compose

## Installations

To set up FreeIPA using Docker Compose, follow these steps:

1. Create a `docker-compose.yml` file:

```
version: "2"

services:
  ipa:
    image: freeipa/freeipa-server:rocky-9-4.10.1
    container_name: ipa
    hostname: ipa.yourdomain.local
    restart: always
    volumes:
      - "/sys/fs/cgroup:/sys/fs/cgroup:ro"
      - "ipa-data:/data"
    environment:
      - PASSWORD=YOUR_PASSWORD
      - IPA_SERVER_IP=172.16.30.2
    command: ipa-server-install --domain='your_domain' --realm='YOUR_REAL_NAME' --no-ntp --setup-adtrust --setup-kra --enable-compat --netbios-name=YOUR_BIOS_NAME --setup-dns --forwarder='1.1.1.1' --forward-policy=only --unattended
    ports:
      - "53:53"
      - "80:80"
      - "443:443"
      - "389:389"
      - "636:636"
      - "88:88"
      - "464:464"
      - "88:88/udp"
      - "53:53/udp"
      - "464:464/udp"
    networks:
      ipa_network:
        ipv4_address: 172.16.30.2
    sysctls:
      - net.ipv6.conf.all.disable_ipv6=0
      
networks:
  ipa_network:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.16.30.0/24
          gateway: 172.16.30.3
          aux_addresses:
            dns: 172.16.30.2

volumes:
  ipa-data:
```

   This file defines a single service, `freeipa-server`, which uses the `freeipa/freeipa-server` image and sets various environment variables to configure FreeIPA.

2. Start the FreeIPA container:

```
   docker-compose up -d
```

   This command will start the FreeIPA container using the `docker-compose.yml` file.

3. Access the FreeIPA web interface:

   Open a web browser and navigate to `https://ipa.example.com`. You should see the FreeIPA login page.

   Note: The first time you access the web interface, you'll need to accept the self-signed SSL certificate.

4. Log in to the FreeIPA web interface:

   Use the default administrator credentials to log in:

   - Username: `admin`
   - Password: `YOUR_PASSWORD`

   You'll be prompted to change the password on first login.

5. Configure FreeIPA:

   Follow the prompts to configure FreeIPA, including setting up the domain name, DNS, and other settings.

## Issues, Feature Requests or Support
Please use the [New Issue](https://github.com/akinbicer/docker-freeipa/issues/new) button to submit issues, feature requests or support issues directly to me. You can also send an e-mail to akin.bicer@outlook.com.tr.
