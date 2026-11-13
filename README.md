# ns8-xibosignage

Module id: `xibosignage`. Images are pinned in `build-images.sh` and updated through reviewed changes.

## Install

```bash
add-module ghcr.io/geniusdynamics/xibosignage:latest 1
```

The command returns the instance id, for example `xibosignage1`. Use the id NS8 returns.

## Configure

```bash
api-cli run configure-module --agent module/xibosignage1 --data - <<'EOF'
{
  "host": "xibosignage.example.org",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```

| Parameter | Required | Type | Description |
|---|---|---|---|
| `host` | yes | string | Host name for the application, like 'xibosignage.domain.org' |
| `lets_encrypt` | yes | boolean | Request a valid Let's Encrypt certificate. |
| `http2https` | yes | boolean | Redirect all the HTTP requests to HTTPS |

## Configuration output

`get-configuration` returns: `host`, `lets_encrypt`, `http2https`.

## Images

| Image | Version |
|---|---|
| `docker.io/mariadb` | `13.0.2` |
| `ghcr.io/xibosignage/xibo-xmr` | `1.5` |
| `ghcr.io/xibosignage/xibo-cms` | `release-4.0.13` |
| `docker.io/memcached` | `alpine` |
| `docker.io/ianw/quickchart` | `v1.8.1` |

## Network and access

- Reserves 1 TCP port(s) on the node. The web application is published through Traefik on the configured host name.
- Runs rootless.
- Declares these NS8 authorizations: `traefik@node:routeadm`.

## Backup

The following paths are included in the NS8 backup (`imageroot/etc/state-include.conf`):

- `state/xibosignage.sql`
- `volumes/xibosignage-app`

## Services

- `mariadb-app.service`
- `memcached-app.service`
- `quickchart-app.service`
- `xibosignage-app.service`
- `xibosignage-xmr.service`
- `xibosignage.service`

## Get the configuration

```bash
api-cli run get-configuration --agent module/xibosignage1
```

## Update and uninstall

```bash
api-cli run update-module --data '{"module_url":"ghcr.io/geniusdynamics/xibosignage:latest","instances":["xibosignage1"],"force":true}'
remove-module --no-preserve xibosignage1
```

## Testing

Continuous integration:

- `Publish images` builds the module image on every push.
- `Test module` installs the image on a QEMU NS8 node and runs the Robot Framework suite in `tests/`. It runs after `Publish images` succeeds on `dev` and on `main`.
- `Build API JSON Schema docs` and the security scan run alongside.

The suite installs the module, configures a host name, checks that the web application answers through Traefik, takes UI screenshots and removes the module.

Run it locally with the NS8 standard infrastructure described in the [ns8-github-actions README](https://github.com/NethServer/ns8-github-actions/blob/v1/README.md#running-tests-locally).

## Open items

Found by the NS8 module standards check. These must be resolved before a stable release:

- The module description is still the template text: write a description of at least 60 characters in ui/public/metadata.json
- tests/xibosignage.robot generated from template: set real configure payload and URL check
- metadata: documentation_url is a placeholder
- metadata: categories empty (use standard categories)
- metadata: description.en needs >= 60 real chars (+ it translation)
- mariadb-app.service: uses %S/state, kickstart uses %E/state
- quickchart-app.service: uses %S/state, kickstart uses %E/state
- xibosignage-xmr.service: uses %S/state, kickstart uses %E/state
- xibosignage-app.service: uses %S/state, kickstart uses %E/state
- memcached-app.service: uses %S/state, kickstart uses %E/state
- xibosignage.service: uses %S/state, kickstart uses %E/state
- discover-smarthost still present: remove if the app does not use SMTP
- README still has the template boilerplate
- mariadb-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- quickchart-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- xibosignage-xmr.service: %S/state changed to %E/state (review the bind mounts that remain)
- xibosignage-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- memcached-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- xibosignage.service: %S/state changed to %E/state (review the bind mounts that remain)
- imageroot/actions/configure-module/80start_services: smarthost lines removed, check the unit still starts
- imageroot/systemd/user/quickchart-app.service: smarthost lines removed, check the unit still starts
- imageroot/systemd/user/xibosignage-xmr.service: smarthost lines removed, check the unit still starts
- imageroot/systemd/user/xibosignage-app.service: smarthost lines removed, check the unit still starts
- imageroot/systemd/user/memcached-app.service: smarthost lines removed, check the unit still starts
- MEMCACHED_IMAGE is used by the module but no image in the label provides it: leftover from the template?
- QUICKCHART_IMAGE is used by the module but no image in the label provides it: leftover from the template?
- XIBO_CMS_IMAGE is used by the module but no image in the label provides it: leftover from the template?
- XIBO_XMR_IMAGE is used by the module but no image in the label provides it: leftover from the template?

## Upstream

Application documentation: https://docs.xibosignage.com/

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
