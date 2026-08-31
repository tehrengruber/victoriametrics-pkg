# victoriametrics-pkg

Arch Linux package for [VictoriaMetrics](https://docs.victoriametrics.com/), the
single-node time series database, built from the upstream release binary.

## Why this exists

The AUR packages for VictoriaMetrics have a habit of going quiet. When this was
written the from-source package had been flagged out of date for a fortnight,
the agent package for ten months, and the one that was current had a single
maintainer and two votes. A package that has stopped tracking upstream looks
exactly like one that has not, which is the awkward part.

Upstream publishes a static binary for every release, so packaging it costs a
`pkgver` and two checksums. This repository automates that and keeps itself
current.

## Contents

    /usr/bin/victoria-metrics
    /usr/lib/systemd/system/victoriametrics.service
    /etc/default/victoriametrics

The unit runs under `DynamicUser` with `StateDirectory=victoriametrics`, so the
daemon never runs as root and its data lives in `/var/lib/victoriametrics` —
the path to put on its own dataset if you keep any real amount of history.
Command line flags go in `/etc/default/victoriametrics`.

## Updating

    ./update.sh            # latest upstream release
    ./update.sh 1.151.0    # a specific one

`update.sh` rewrites `pkgver`, `pkgrel` and both checksums from the GitHub
release metadata, which publishes a digest per asset, so nothing is downloaded
in order to be hashed. It exits without touching anything if the version already
matches.

A scheduled workflow runs the same script every day, builds the result in an
Arch container, checks the expected files are present and opens a pull request
with the bump. Building — rather than bumping checksums alone — is the part that
matters: a checksum change would sail straight past an upstream that renamed or
restructured its release assets, and that failure would otherwise turn up at
install time instead of here.
