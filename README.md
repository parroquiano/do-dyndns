# Dynamic DNS for DigitalOcean

![DDNS](https://raw.githubusercontent.com/layfellow/do-dyndns/main/ddns.png)

[LÉAME en español](README.es.md)

`do-dyndns` is a simple dynamic DNS client for DigitalOcean. It can update one or
more DNS records on your DigitalOcean-managed domains with the public IP address
of a client host.

For example, let’s assume you have a managed domain `example.com` in your DigitalOcean account
and a host named `foo` in your homelab. If such host has an external public IP provided by your ISP,
then you could run `do-dyndns` periodically on `foo` to keep a subdomain record `foo.example.com`
pointed to the actual public IP.

## Installation

Download the appropriate binary for your platform from [Releases](https://github.com/layfellow/do-dyndns/releases) and copy it to any directory in your `PATH` as `do-dyndns`.

Copy the example configuration file `config.json.example` to `$HOME/.config/do-dyndns/config.json`.
Make sure to create first the `$HOME/.config/do-dyndns` directory or, alternatively, you can use a more traditional `$HOME/.do-dyndnsrc.json` file.

Edit `config.json` and set the following fields:

- `"token"` (mandatory): Your DigitalOcean [Personal Access Token](https://docs.digitalocean.com/reference/api/create-personal-access-token/). It must have *Write* scope.
- `"log"` (optional): the full path to a log file.
- `"records"` array (mandatory): an array of subdomains to be dynamically updated.

`do-dyndns` will log all its activity to the `log` file when run as a cron job.
The log file is not used when running in an interactive shell. It is not used either
when running as a systemd scheduled task, because stdout is logged automatically
in this case. If `log` is not provided, it will default to `$HOME/.cache/do-dyndns/out.log`.

For each item in `records`, you need to set:

- `"type"`: `"A"`, IPv4 DNS “A” record, the only supported type for now.
- `"subdomain"`: a fully qualified subdomain name to be dynamically updated with the current public
  IP of the client host.

## Running as a cron job or systemd timer

You can run `do-dyndns` as a cron job. All activity will be logged to the `log` file.
(see the configuration file above.)

Alternatively, you can install `do-dyndns` as a systemd timer. Note that `do-dyndns` will
*not* log its activity to the `log` file when running as a systemd timer.
This is because systemd itself takes care of logging; use `journalctl` to query it.

For further information on systemd timers, see the [excelent ArchWiki documentation](https://wiki.archlinux.org/title/Systemd/Timers). (Note that this documentation is not specific to Arch Linux—it applies to any systemd-based Linux distro.)

## Tested platforms

I tested it on Ubuntu 22.10, Fedora 37 and macOS Monterey 12.6.7. Note that there are
no special requirements for Linux or macOS, so that it should work on any reasonably
modern Linux distro or macOS release.

### What about Windows?

The Linux x86-64 binary *should* work on Windows 10/11 using
[WSL 2](https://learn.microsoft.com/en-us/windows/wsl/about) (Windows Subsystem for Linux).
Unfortunately, I don’t have access to a Windows system, so I can’t confirm this.

## For developers

`do-dyndns` is written in Go 1.19. The source code is entirely contained in `main.go`.
Pull requests are welcome.

I wrote a small Makefile to help me with routine tasks.

To update all Go dependencies, and update `go.mod` and `go.sum`:

    $ make dependencies

To run `golangci-lint` locally (you need [golangci-lint](https://golangci-lint.run/) installed):

    $ make lint

To build the binary for your development platform:

    $ make build

To install the binary in your default path:

    $ make install

To create a new GitHub Release using the latest tag (this requires the [GitHub CLI](https://cli.github.com/)):

    $ make releases

---

<a href="https://www.flaticon.com/free-icons/ddns" title="ddns icons">DDNS icon by Bogdan Rosu - Flaticon</a>
