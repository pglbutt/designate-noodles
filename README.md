Overview
--------

This repo contains request files for use with [pglbutt/noodles](https://github.com/pglbutt/noodles) for talking to [Openstack Designate](http://docs.openstack.org/developer/designate/)

This makes it easier to chain requests together than using curl or postman.

Setup
-----

First install `spag`. Right now, this requires building from source.
Install [rust](http://www.rust-lang.org/install.html)`, and build from source:

    curl -sSf https://static.rust-lang.org/rustup.sh | sh -s -- --channel=nightly
    git clone git@github.com:pglbutt/noodles.git
    cd noodles
    cargo build --release

The executable is build at `target/release/spag`. You can put that directory on
your `PATH` or copy the executable to `/usr/local/bin` or wherever you prefer.

Clone this repo

    $ git clone git@github.com:pglbutt/designate-noodles.git
    $ cd designate-noodles
    $ spag --help

Usage
-----

First set your designate endpoint and requests directory:

    $ spag env set endpoint designate.me.com
    $ spag env set dir "`pwd`/requests"

To see a list of the request files this repository contains, run:

    $ spag request list
    requests/v2/pool/get.yml
    requests/v2/pool/list.yml
    ...

You can invoke any of these requests using a trailing portion of the path, with
or without the extension, as long as the request file is uniquely identified.

All of the following will get a pool:

    $ spag request pool/get
    $ spag request pool/get.yml
    $ spag request v2/pool/get
    $ spag request v2/pool/get.yml


### Pools

Pool requests operate on the id found in the last response body (which may not
be a pool id) if found, or will use the default pool id, `794ccc2c-d751-44fe-b57f-8894c9f5c842`, otherwise.

To use a pool with a specific id use `--with pool_id <my-pool-id>`

##### Getting a pool

    # use the id from the last response body, or the default pool id
    $ spag request pool/get

##### Updating a pool

    $ spag request pool/patch --with hostname ns1.example.com.

    # get the pool you just updated
    $ spag request pool/get


### Zones

Zone requests will use the id found in the previous response body (which may
not be a zone id).

To override that use, `--with zone_id <my-zone-id>`

##### Creating a zone

    # create a zone
    $ spag request zone/post --with name=hello.com.

##### Getting a zone

    # get the zone you just created
    $ spag request zone/get

    # get a zone with a specific id
    $ spag request zone/get --with zone_id <my-zone-id>

##### Listing zones

    $ spag request zone/list


### Recordsets

Recordset requests will require a particular zone_id. The request files are
written to look for the zone id in the environment:

Set the zone id in the environment under the `zone_id` key:

    # use the id from the previous response body
    # any one of these will word
    $ spag env set zone_id @id
    $ spag env set zone_id @body.id
    $ spag env set zone_id '@{{ last.response.body.id }}'

    # use your own supplied id
    $ spag env set zone_id 4328782e-a50e-4490-a3ed-409730eb9ba5

You can also override the zone_id using `--with zone_id 4328782e-a50e-4490-a3ed-409730eb9ba5`
on any recordset requests.

##### Listing recordsets

    # uses the zone_id in the environment
    $ spag request recordset/list

    # give your own zone_id
    $ spag request recordset/list --with zone_id 4328782e-a50e-4490-a3ed-409730eb9ba5`


##### Creating a recordset

The following will create an A recordset

    $ spag request recordset/post  \
        --with name poo.hello.com. \
        --with type A              \
        --with data 127.0.0.1

Optionally, you can override the default description by adding `--with description "My description"`

##### Getting a recordset

    # get the recordset we just created
    $ spag request recordset/get
