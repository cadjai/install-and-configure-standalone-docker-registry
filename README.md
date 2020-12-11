Role Name
=========

Utility role to deploy a docker registry v2 using a docker-distrubution or the container image. 
It can also deploy a secure private registry with a provided PKI certificate or a self generate one as well as use htpasswd for authentication. 

Requirements
------------

This role uses Epel repository to pull the docker-distribution rpm. 
If not connected (disconnected environment) you will need to provide the repository to use. 
Also when deploying on AWS/EC2 the python-fierwall and possibly python modules might be not be available on the host and might need to be installed before proceeding. 
This has been tested on RHEL7 and RHEL8 using both rpm and containerized approached without any other issues.

Role Variables
--------------

- use_containeried_registry: To deploy registry as docker registry v2 container. Default is false
- secure_registry: To create a secure registry with PKI cert and htpasswd for authentication
- registry_host_fqdn: Host FQDN to use. It will also be used in the cert created and related SAN
- registry_host_port: Port to bind the registry to on the host. Default to 5000
- registry_secret: The password used for htpasswd authentication
- registry_container_image: The container image to use when launching a containerized registry. Default to docker.io/library/registry:2
- generate_selfsigned_registry_certfiles: Indicate wether a selfsign cert should be generated or not.
- registry_admin_username: The admin username for the registry. Used for the htpasswd authentication.
- registry_admin_password: The admin password for the registry . Used for the htpasswd authentication.
- cert_country: The country to use when creating a selfsigned certificate. Default to US
- cert_state: The name of the stae or province to use when creating a selfsigned certificate. Default to CO
- cert_locality: The city or locality to use when creating a selfsigned certificate. Default to Denver
- cert_org: The organization to use when creating a selfsigned certificate. Defaults to Red Hat
- cert_org_unit: The organization unit to use when creating a selfsigned certificate. Default to Standalone Registry.
- registry_ca_keyfile: The name of the self signed CA private key file. Default to registry-ca.key
- registry_ca_csrfile:: The name of the self signed CA CSR file. Default to registry-ca.csr
registry_ca_certfile: The name of the self signed CA certificate file. Default to registry-ca.crt
- registry_certkeyfile: The name of the private key file for the registry. Default to registry.key
- registry_certcsrfile: The name of the CSR file for the registry. Default to registry.csr
- registry_certfile: The name of the certificate file for the registry. Default to registry.crt
- registry_authzfile: The name of the htpasswd authentication file. Default to registry_passwd.
- registry_authzrealm: The htpasswd realm to use whe configuring authentication. Default to Registry Realm.
- enable_authz_on_registry: Whether to use htpasswd authentication or not. Default to false.
- registry_container_name: The name of the registry container when running containerized. Default to mirror-registry.
- registry_root_dir: The root directory where registry configuration files are stored. Default to /opt/registry for container and /etc/docker-distribution/registry for docker-distribution.
- registry_certs_dir: The directory where the registry certificates are stored relative to the root. Default to certs.
- registry_data_dir: The directory where any registry related config data (e.g. config.yml) is stored relative to the root. Defaults to data.
- registry_container_dir: The directory on the host to be bond to the container so that container data is persisted to host when mirroring. Defaults to /var/lib/registry.
- registry_auth_dir: The directory where the htpasswd authentication file is stored relative to root. Default to auth.
- registry_container_auth_dir: The directory where the htpasswd is mounted into inside the container. Defaults to /auth.
- registry_container_cert_dir: The directory where the pki certs are mounted into inside the container. Defaults to /certs


Dependencies
------------


Example Playbook
----------------

To run it with unecure registry use
```yaml
    - hosts: registry
      vars:
        registry_host_fqdn: 'registry.example.net'
        secure_registry: false
        enable_authz_on_registry: false
        generate_selfsigned_registry_certfiles: false
        use_containerized_registry: true 
        registry_host_port: 5000
      roles:
         - { role: registry }
```

To create a private registry with htpass auth use this. 
You can add extra vars to control the cert if needed (see default/main.yml for defaults).
```yaml
    - hosts: registry
      vars:
        registry_host_fqdn: 'registry.example.net'
        registry_secret: 'password'
        registry_admin_username: 'admin'
        registry_admin_password: 'password'
        secure_registry: true
        enable_authz_on_registry: true
        generate_selfsigned_registry_certfiles: true
        use_containerized_registry: true 
        registry_host_port: 5000
      roles:
         - { role: registry }
```

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
