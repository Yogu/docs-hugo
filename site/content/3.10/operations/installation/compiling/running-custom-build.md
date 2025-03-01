---
title: create data directory
menuTitle: Running Custom Build
weight: 15
description: >-
  You've already built a custom version of ArangoDB and want to run it
archetype: default
---
Once you built a custom version of ArangoDB (see
[Compiling](_index.md)), you may want to run it using
existing data or possibly in isolation from an existing installation.

We assumes that you are in the root directory of the ArangoDB distribution
and compiling has successfully finished.

Note that this guide is for Linux only.

## Running in isolation

This part shows how to run your custom build with an empty database directory

```bash
mkdir /tmp/arangodb

# run
bin/arangod \
    --configuration etc/relative/arangod.conf\
     --database.directory /tmp/arangodb
```

## Running with data

This part shows how to run your custom build with the config and data from a pre-existing stable installation.

{{< danger >}}
ArangoDB's developers may change the db file format and after running with a
changed file format, there may be no way back. Alternatively you can run your
build in isolation and [dump](../../../components/tools/arangodump/_index.md) and
[restore](../../../components/tools/arangorestore/_index.md) the data from the
stable to your custom build.
{{< /danger >}}

When running like this, you must run the db as the arangod user (the default
installed by the package) in order to have write access to the log, database
directory etc. Running as root will likely mess up the file permissions - good
luck fixing that!

```bash
# become root first
su

# now switch to arangod and run
su - arangod
bin/arangod --configuration /etc/arangodb/arangod.conf
```
