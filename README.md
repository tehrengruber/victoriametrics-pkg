# victoriametrics-pkg

Arch package for [VictoriaMetrics](https://docs.victoriametrics.com/), built
from the upstream release binary.

Installed by the `monitoring` role in the ansible-devbox repository, which
clones this as `<base_url>victoriametrics-pkg.git`, runs `makepkg -rsi` and
checks the result with `pacman -Qi victoriametrics`. Repository name, `pkgname`
and the role's `monitoring_victoriametrics_package` therefore have to stay in
step.

## Why this exists rather than the AUR

The AUR carried three VictoriaMetrics packages and only one was current when
this was written: the from-source `victoriametrics` had been flagged out of date
for a fortnight, `victoriametrics-agent` for ten months, and both still looked
like maintained packages from the outside. The one that was current,
`victoriametrics-bin`, had two votes and a single maintainer.

That is a thin dependency for the store that everything else reports into, and a
package going quiet is silent — the same class of failure the monitoring is
there to catch. Upstream ships a single static binary, so packaging it here
costs a `pkgver` and two checksums per release.

## Contents

    /usr/bin/victoria-metrics
    /usr/lib/systemd/system/victoriametrics.service
    /etc/default/victoriametrics

The unit uses `DynamicUser` with `StateDirectory=victoriametrics`, so the daemon
never runs as root and its data lives at `/var/lib/victoriametrics` — that is
the path to put on its own dataset. Flags go in `/etc/default/victoriametrics`,
which ansible manages on hosts running the monitoring role.

## Updating

    ./update.sh            # latest upstream release
    ./update.sh 1.151.0    # a specific one

Rewrites `pkgver`, `pkgrel` and both checksums from the GitHub release metadata,
which publishes a digest per asset — nothing is downloaded to be hashed. It
exits without touching anything if the version already matches.

`.github/workflows/update.yml` runs the same script daily, builds the result in
an Arch container, asserts the expected files are present, and opens a pull
request. The build is the part that matters: a checksum bump alone would sail
past an upstream that renames or restructures its tarball, and that failure
would otherwise surface on the host at deploy time.

The PR is merged automatically once its checks pass, which — since the build
runs before the PR is opened — is immediately. The pull request stays as the
record of what changed and the thing to revert if a release turns out to break
something.

Two repository settings are required:

- *Settings → Actions → General → Allow GitHub Actions to create and approve
  pull requests*
- *Settings → General → Pull Requests → Allow auto-merge*

Note this is auto-**merge**, not auto-approval: `GITHUB_TOKEN` cannot approve a
pull request it opened itself. If branch protection is ever added here requiring
a review, that step will need a token belonging to a different identity.
