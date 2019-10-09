# a2dp-sink

This role makes your machine act as Bluetooth audio sink (over A2DP).
Note: All paired devices (i.e. potential audio sources) are automatically trusted.
**This might have severe security implications.**
This is realized by a systemd service running [btrust](https://github.com/haslersn/btrust).
For configuration, read through the role variables documented below.

## Requirements

An apt based system like [Ubuntu](https://www.ubuntu.com/) or [Debian](https://www.debian.org/).

## Updating

This role can also update btrust to a desired version.
To do so, just change the [btrust source specification](#btrust-source-specification) and the
`a2dp_sink_btrust_version` variable accordingly, and then reapply this role.

## Role Variables

| Name                               | Default                                                                 | Description                                              |
| :--------------------------------- | :---------------------------------------------------------------------- | :------------------------------------------------------- |
| a2dp_sink_btrust_binary_url        | _undefined_                                                             | See [source specification](#source-specification)        |
| a2dp_sink_btrust_archive_url       | _undefined_                                                             | See [source specification](#source-specification)        |
| a2dp_sink_btrust_github_repo       | haslersn/btrust                                                         | See [source specification](#source-specification)        |
| a2dp_sink_btrust_checksum          | sha256:960b5dcc90714df217e4c68fdfb1f874f36418655599233310c1a5d3e1837009 | Checksum of the downloaded archive or binary             |
| a2dp_sink_btrust_version           | 0.1.0                                                                   | See [source specification](#source-specification)        |
| a2dp_sink_user                     | a2dp-sink                                                               | The user that runs any service installed by this role.   |
| a2dp_sink_enable_switch_on_connect | True                                                                    | Whether to load the PulseAudio module switch-on-connect. |
| a2dp_sink_a2dp_sink_adapter_name   | A2DP sink                                                               | Name of this Bluetooth device.                           |

### Source Specification

There are multiple ways to specify the source from which btrust should be downloaded.
Chosing among them is done by providing exactly one of the following role variables.
Actually, multiple of those role variables can be provided, but then only the one listed here first
is considered.

* `btrust_binary_url`:
  Download a precompiled binary from that URL.
  **You should anyway set `btrust_version` to a value that's unique for that version.**
  This is because btrust is only re-downloaded, when the `btrust_version` role variable
  changes.
* `btrust_archive_url`:
  Build from source.
  The source is downloaded as an archive from that URL.
  The downloaded archive must contain a folder named `btrust-{{ btrust_version }}` which
  contains in turn the source that can be compiled with `cargo build`.
  The compilation yields a binary named `btrust` which is then installed.
* `btrust_github_repo`: 
  Short form for
  `btrust_archive_url: https://github.com/{{ btrust_github_repo }}/archive/{{ btrust_version }}.tar.gz`.

## Example Playbook

The following playbook works if `~/.cache/stuvus` exists on the localhost.

```yml
- hosts: mpd01
  roles:
    - role: a2dp-sink
      global_cache_dir: "{{ lookup('env', 'HOME') }}/.cache/stuvus"

      a2dp_sink_adapter_name: stuvus-BT
      a2dp_sink_user: stuvus-mpd
      a2dp_sink_btrust_version: 0.1.0
      a2dp_sink_btrust_binary_url: https://github.com/haslersn/btrust/releases/download/{{ a2dp_sink_btrust_version }}/btrust-aarch64-unknown-linux-gnu
      a2dp_sink_btrust_checksum: sha256:ae3cc2894c98e469644bc64763c4fcaa7a16972fa423c61f44f0fd329b1f9f27
```
