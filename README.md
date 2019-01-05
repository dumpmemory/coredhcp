# coredhcp

[![Build Status](https://travis-ci.org/coredhcp/coredhcp.svg?branch=master)](https://travis-ci.org/coredhcp/coredhcp)
[![codecov](https://codecov.io/gh/coredhcp/coredhcp/branch/master/graph/badge.svg)](https://codecov.io/gh/coredhcp/coredhcp)
[![Go Report Card](https://goreportcard.com/badge/github.com/coredhcp/coredhcp)](https://goreportcard.com/report/github.com/coredhcp/coredhcp)

Fast, multithreaded, modular and extensible DHCP server written in Go

This is still a work-in-progress

## Example configuration

In CoreDHCP almost everything is implemented as a plugin. The order of plugins in the configuration matters: every request is evaluated calling each plugin in order, until one breaks the evaluation and responds to, or drops, the request.

The following configuration runs a DHCPv6-only server, listening on all the interfaces, using a custom DUID-LL as server ID, and reading the leases from a text file.

```
server6:
    listen: '[::]:547'
    plugins:
        - server_id: LL 00:de:ad:be:ef:00
        - file: "leases.txt"
        # - dns: 8.8.8.8 8.8.4.4 2001:4860:4860::8888 2001:4860:4860::8844

#server4:
#    listen: '127.0.0.1:67'
```

See also [config.yml.example](cmds/coredhcp/config.yml.example).

## Build and run

The server is located under [cmds/coredhcp/](cmds/coredhcp/), so enter that
directory first.

Once you have a working configuration in `config.yml` (see [config.yml.example](cmds/coredhcp/config.yml.example)), you can build and run the server:
```
$ cd cmds/coredhcp
$ go build
$ sudo ./coredhcp
INFO[2019-01-05T22:28:07Z] Registering plugin "file"
INFO[2019-01-05T22:28:07Z] Registering plugin "server_id"
INFO[2019-01-05T22:28:07Z] Loading configuration
INFO[2019-01-05T22:28:07Z] Found plugin: `server_id` with 2 args, `[LL 00:de:ad:be:ef:00]`
INFO[2019-01-05T22:28:07Z] Found plugin: `file` with 1 args, `[leases.txt]`
INFO[2019-01-05T22:28:07Z] Loading plugins...
INFO[2019-01-05T22:28:07Z] Loading plugin `server_id`
INFO[2019-01-05T22:28:07Z] plugins/server_id: loading `server_id` plugin
INFO[2019-01-05T22:28:07Z] plugins/server_id: using ll 00:de:ad:be:ef:00
INFO[2019-01-05T22:28:07Z] Loading plugin `file`
INFO[2019-01-05T22:28:07Z] plugins/file: reading leases from leases.txt
INFO[2019-01-05T22:28:07Z] plugins/file: loaded 1 leases from leases.txt
INFO[2019-01-05T22:28:07Z] Starting DHCPv6 listener on [::]:547
INFO[2019-01-05T22:28:07Z] Waiting
2019/01/05 22:28:07 Server listening on [::]:547
2019/01/05 22:28:07 Ready to handle requests
...
```

Then try it with the local test client, that is located under
[cmds/client/](cmds/client):
```
$ cd cmds/client
$ go build
$ sudo ./client
INFO[2019-01-05T22:29:21Z] &{ReadTimeout:3s WriteTimeout:3s LocalAddr:[::1]:546 RemoteAddr:[::1]:547}
INFO[2019-01-05T22:29:21Z] DHCPv6Message
  messageType=SOLICIT
  transactionid=0x6d30ff
  options=[
    OptClientId{cid=DUID{type=DUID-LLT hwtype=Ethernet hwaddr=00:11:22:33:44:55}}
    OptRequestedOption{options=[DNS Recursive Name Server, Domain Search List]}
    OptElapsedTime{elapsedtime=0}
    OptIANA{IAID=[250 206 176 12], t1=3600, t2=5400, options=[]}
  ]
...
```

# Authors

* [Andrea Barberio](https://github.com/insomniacslk)
* [Pablo Mazzini](https://github.com/pmazzini)