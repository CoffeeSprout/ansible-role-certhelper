coffeesprout.certhelper
=======================

Pull certificates / keypairs from one host and optionally combine them for consumption by HAPRoxy loadbalancers.

Example:

Assume we have 2+ loadbalancers and a host that maintains our certs (for example running the excellent acme.sh); This role allows you to periodically pull a list of certs in from this host and place it on all loadbalancers that this role runs on.


Requirements
------------

This role only copies data; You have to have other means to ensure certificates are generated.

Role Variables
--------------

**Global settings:**

    certhelper_vars: "acme_sh_FreeBSD.yml"

Defines the default locations and names to gather the certificates from; See below

    certhelper_destination: "{{ certhelper_destination_folder }}/{{ _cert.name }}-combined"
    certhelper_destination_folder: "/var/db/acme/certs/haproxy_export"

Define where the resulting file should be stored on the loadbalancer

    certhelper_handler: "reload haproxy"

Define which handler to trigger on the loadbalancer. Note you need to define this yourself (see example)

    certhelper_template: "certbundle.j2"

The j2 template to output the correct data (for now we just combine the fullchain and private key as expected by HAProxy)

**Specific settings:**

It's possible to define locations and names; To make sure you don't have to define a whole bunch I've included some templates that work for me.

Example: acme_sh_FreeBSD.yml

    certhelper_fullchain: "{{ certhelper_folder }}/fullchain.cer"

Contains the "fullchain" certificate; This is normally the intermediates and your cert.    

    certhelper_cert: "{{ certhelper_folder }}/{{ _cert.name }}.cer"

Contains the certificate.

    certhelper_key: "{{ certhelper_folder }}/{{ _cert.name }}.key"
    
Contains the private key for the certificate.

    certhelper_folder: "/var/db/acme/certs/{{ _cert.name }}"
    
Defines where to find the files mentioned above

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    #acme.sh on FreeBSD (all defaults)
    - hosts: lbs
      become: True
      serial: 1
      vars:
        certhelper_certs:
        - name: example.com
        certhelper_certhost: "letsencrypt.example.com"
      roles:
      - role: coffeesprout.certhelper

Or using Letsencrypt

    - hosts: lbs
      become: True
      serial: 1
      vars:
        certhelper_certs:
        - name: example.com
        certhelper_certhost: "letsencrypt.example.com"
        certhelper_vars: "letsencrypt_Linux.yml"
      roles:
      - role: coffeesprout.certhelper

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
