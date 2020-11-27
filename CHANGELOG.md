# Changelog

All notable changes to this project will be documented in this file, in reverse chronological order by release.

This project follow [semver](https://semver.org/) since version 1.3.2

## 1.5.1 - 2020-11-27

- FIX : Allow provisioning provider without params (for example, for Docker)

## 1.5.0 - 2016-05-19

- Add CHANGELOG.md
- Replace "servers" key by "environments"
- Improves documentation

## 1.4.0 - 2016-05-17

- Fail if vagrant.yml does not exist
- Allow vagrant commands to be run on subfolder
- Add option for nfs synced folder in example

## 1.3.4 - 2016-04-15

- Rename VagrantFile to Vagrantfile

## 1.3.3 - 2016-04-06

- Add missing check for hostmanager

## 1.3.2 - 2016-02-15

- Update README.md

## 1.3.1 - 2016-02-15

- Add missing .editorconfig file

## 1.3.0 - 2016-02-15

- Add SSH forward forward_agent config
- Check if vbguest and hostmanager are installed

## 1.2.1 - 2015-10-17

- FIX : hostmanager is now launched before the other provisioning tools

## 1.2.0 - 2015-10-22

- Add port forwarding support

## 1.1.1 - 2015-09-29

- FIX 'cpus' param in example

## 1.1.0 - 2015-05-06

- [BC break] Change provisioning structure for allowing several of the same type

## 1.0.1 - 2015-03-16

- Fix Global bug if empty


