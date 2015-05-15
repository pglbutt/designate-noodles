Overview
--------

This repo contains request files for use with [pglbutt/noodles](https://github.com/pglbutt/noodles) for talking to [Openstack Designate](http://docs.openstack.org/developer/designate/)

Setup
-----

First install `spag`:

    git clone git@github.com:pglbutt/noodles.git
    pip install noodles

Clone this repo

    git clone git@github.com:pglbutt/designate-noodles.git
    cd designate-noodles
  
Then set your designate endpoint and requests dir with spag:

    spag env set -E endpoint=http://<designate-ip>
    spag env set -E dir=./requests
  
Example use
-----------

    # patch the default pool
    spag request pool/patch_default --with nameserver=ns1.example.com.`
  
    # get the pool you just updated
    spag request pool/get
    
    # create a zone
    spag request zone/post --with zone_name=hello.com.
    
    # get the zone you just created
    spag request zone/get
