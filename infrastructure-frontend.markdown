---
title: Build Infrastructure Frontend
layout: main
categories: infrastructure
---

# Frontend setup

The {{site.experiment}} build infrastructure is exposed via SSO.

Due to limitations of the SSO protocol this happens via single machine which
runs apache and does the reverse proxying to the actual service.

The machine is setup in CERN/IT puppet + OpenStack facility in the hostgroup
`alibuild/frontend`.

# Disaster recovering

## Starting the frontend

The quick recipe to restart the frontend is:

- Login to `aiadm.cern.ch`.
- Set up your OpenStack environment by doing:

      eval $(ai-rc "{{site.experiment}} Release Testing")

- To spawn a machine you need to use the `ai-bs-vm` wrapper, which will take
  care of provisioning the machine and putting it in Foreman, so that it will
  receive from it the Puppet configuration:

      MACHINE_NAME=<{{site.exp_prefix}}build-frontendXX>

      ai-bs-vm -g alibuild/frontend                   \
               --cc 7                                 \
               --nova-sshkey alibuild                 \
               --nova-flavor {{site.openstack_master_flavor}} \
               --landb-mainuser alice-agile-admin \
               --landb-responsible alice-agile-admin \
               $MACHINE_NAME
- Once you have the frontend created, you also need to grant it read-only
  permission to some CERN S3 bucket we use for storing logs. The policy for
  it can be found in the [ali-marathon](https://gitlab.cern.ch/AliceDevOps/ali-marathon)
  repository. The required policies are:
  
  * `s3/alice-build-logs-policy.json`
  
  and they need to have the right Ip Address registered there.
