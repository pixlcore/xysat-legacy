# xySat Legacy Builds

This repository produces legacy build artifacts for the [xySat](https://github.com/pixlcore/xysat) companion to the [xyOps](https://github.com/pixlcore/xyops) workflow automation and server monitoring platform.

It exists because official Node.js binaries for versions above v16 break on older Linux OSes (e.g. Amazon Linux 2 and CentOS 7).  To keep xySat working on those legacy systems, this repo builds a special Linux x64 bundle using the [unofficial Node.js build](https://github.com/nodejs/unofficial-builds/) that targets GLIBC 2.17.

## How to Use

If you have older Linux x64 servers running Amazon Linux 2 or CentOS 7, here is how to enable legacy support in xyOps.  You can either edit your `/opt/xyops/conf/config.json` file or use the web-based configuration editor.

If you are editing the `config.json` file, locate the top-level `satellite` object in the file, and change these three nested properties as shown:

```json
"list_url": "https://api.github.com/repos/pixlcore/xysat-legacy/releases",
"base_url": "https://github.com/pixlcore/xysat-legacy/releases",
```

If you are using the web-based configuration editor, enter "satellite" as the search string, and change the following fields:

- **Release Metadata URL**: `https://api.github.com/repos/pixlcore/xysat-legacy/releases`
- **Release Base URL**: `https://github.com/pixlcore/xysat-legacy/releases`

Save the changes, and the next time you add a new Linux x64 server, it will use the legacy compatibility build of Node.js LTS.

Please note that xyOps caches satellite tarballs for 1 hour by default, so you may need to wait up to an hour for this change to take effect, or you can reduce the TTL in your configuration.

## What This Repo Does

- Mirrors the official `xySat` release workflow.
- Downloads the latest `xySat` source from the `main` branch at build time.
- Builds the same OS/arch matrix as the official workflow.
- Uses the official Node.js runtime for all targets except Linux x64.
- Uses the unofficial [linux-x64-glibc-217](https://github.com/nodejs/unofficial-builds/) Node.js runtime for Linux x64.

## Source and Runtime Inputs

- `xySat` source: `https://github.com/pixlcore/xysat/archive/refs/heads/main.tar.gz`
- Official Node.js releases: `https://nodejs.org/dist/`
- Unofficial Node.js release (Linux x64 legacy): `https://unofficial-builds.nodejs.org/download/release/v22.22.0/node-v22.22.0-linux-x64-glibc-217.tar.gz`

## How Releases Work

- Push a tag in this repository to trigger the workflow.
- The workflow publishes `satellite-<platform>-<arch>.tar.gz` artifacts to a GitHub Release matching the tag.
- Linux x64 artifacts use the legacy GLIBC-compatible Node.js runtime; all other targets use official Node.js binaries.

## Notes

- This repo does not contain `xySat` source code. It is fetched at build time from the official repository.
- If the Node.js version changes in the official workflow, update the Node version and legacy URL in `.github/workflows/release.yml`.
