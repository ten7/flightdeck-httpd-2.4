![Flightdeck](https://flightdeck.ten7.com/flightdeck.svg)

# Flightdeck HTTPD

Flightdeck Web is a minimalist Apache HTTPD container for Kubernetes and Docker. You can use it both for local development and production.

Features:
* ConfigMap-friendly YAML configuration

## Tags and versions

There are several tags available for this container, each with different software and support:

| Tags | HTTPD Version |
| ---- | ----------------- |
| latest | 2.4 |
| x.y.z | 2.4 |


Where:
 * **x.y.z** is the container version as seen on the [tags page](https://github.com/ten7/flightdeck-httpd-2.4/tags)

## Configuration

Instead of a large number of environment variables, this container relies on a file to perform all runtime configuration, `flightdeck-httpd.yml`. Inside the file, create following:

```yaml
---
flightdeck_httpd: {}
```

All configuration is done as items under the `flightdeck_httpd` variable. See the following sections for details as to particular configurations.

You can provide this file in one of three ways to the container:

* Mount the configuration file at path `/config/httpd/flightdeck-httpd.yml` inside the container using a bind mount, configmap, or secret.
* Mount the config file anywhere in the container, and set the `FLIGHTDECK_CONFIG_FILE` environment variable to the path of the file.
* Encode the contents of `flightdeck-httpd.yml` as base64 and assign the result to the `FLIGHTDECK_CONFIG` environment variable.

### Configuring virtual hosts

This container may have one or more virtual hosts configured to serve. Each is specified as an item under the `vhosts` key:

```yaml
---
flightdeck_httpd:
  vhosts:
    - name: "example.com"
      host: "*:80"
      docroot: "/var/www/html"
      aliases:
        - "docker.test"
        - "docker.localhost"
      env:
        - name: ANSWER_TO_EVERYTHING
          value: "42"
      https:  yes
      certFile: "/etc/ssl/apache2/server.pem"
      keyFile: "/etc/ssl/apache2/server.key"
      extraLines:
        - "SSLProxyEngine On"
```

Where:

* **name** is the fully-qualified domain name of the site. Required.
* **host** is the `hostname:port` on which to accept requests. Optional, defaults to `*:80`.
* **docroot** is the full path inside the container to the site docroot. Optional, defaults to `/var/www/html`.
* **aliases** is a list of domain aliases to treat as alternate domain names for the site. Optional.
* **env** is a list of environment variables to set when serving requests. Optional.
* **https** specifies that this vhost should serve requests through HTTPS. `yes` to enable, `no` to disable. Optional, defaults to `no`.
* **certFile** is the full path inside the container to the SSL certificate chain in `*.pem` format. Optional.
* **keyFile** is the full path inside the container to the SSL certificate private key. Optional.
* **extraLines** is a list of additional custom lines to add to the vhost. Optional.

## Part of Flightdeck

This container is part of the [Flightdeck library](https://github.com/ten7/flight-deck) of containers for Drupal local development and production workloads on Docker, Swarm, and Kubernetes.

Flightdeck is used and supported by [TEN7](https://ten7.com/).

## Debugging

If you need to get verbose output from the entrypoint, set `flightdeck_debug` to `true` or `yes` in the config file.

```yaml
---
flightdeck_debug: yes
```

This container uses [Ansible](https://www.ansible.com/) to perform start-up tasks. To get even more verbose output from the start up scripts, set the `ANSIBLE_VERBOSITY` environment variable to `4`.

If the container will not start due to a failure of the entrypoint, set the `FLIGHTDECK_SKIP_ENTRYPOINT` environment variable to `true` or `1`, then restart the container.

## License

Flightdeck is licensed under GPLv3. See [LICENSE](https://raw.githubusercontent.com/ten7/flight-deck/master/LICENSE) for the complete language.
