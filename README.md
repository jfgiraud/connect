# connect

`connect` is a centralized connection tool written in bash.

It is configurable, expandable and offers a search engine to the configurated connections.


### installation

```
.
├── bin
│   └── connect
├── .connect
└── .connectrc
```

The path to the `connect` script must be added to your `PATH` variable.

The files `.connect` and `.connectrc` must be copied to your `HOMEDIR`.

### examples of configuration

The script `.connect` defines reusable functions.

The script `.connectrc` defines connection configurations (and overrides some functions).

For the moment, the offered functionalities are:
- the ssh connections
- the MySQL database connections (over ssh or not)

Example of `.connectrc` file:

```
function get_sql_version() {
    local tunnel="$1"
    local command="$2"   
    if [ -z "$tunnel" ]; then
        $command --connect-timeout=1 -B -s -e 'select value from _metadatas where name="version"' 2>/dev/null 
      else
          ${tunnel/ -t/} "${command} --connect-timeout=1 -B -s -e 'select value from _metadatas where name=\"version\"' | tail -n 1"
      fi
  }
  function connect_sql_service_module_localhost()        { call_mysql localhost 3306 test "test" SERVICEmodule "$@"; } 
  function connect_sql_service_module_localhost_test()   { call_mysql localhost 3306 test "test" SERVICEmodule_test "$@"; }
  function connect_sql_service_module_prp() { call_mysql "ssh -t prp_service_as.example.com" prp_sql_host.example.com 3306 super "super" SERVICEmodule "$@"; }
  function connect_sql_service_module_prod() { call_mysql "ssh -t prod_service_as.example.com" prod_sql_host.example.com 3306 super "super" SERVICEmodule "$@"; }
  function connect_ssh_service_as_prp()                  { call_ssh '-X' 'prp_service_as.example.com' "$@"; }
  function connect_ssh_service_as_prod()                 { call_ssh '-X' 'prod_service_as.example.com' "$@"; }
```

### example of use

The connection is done by searching for words in function names.

If more than one item match, a prompt is displayed and ask precisions.

In the other case, the connection is directly done.

The defined function names (in file `.connectrc`) must start with `connect_` and the words separator is `_`.

Examples:

```
$ connect sql prp
$ connect localhost -test
$ connect ssh as
1) ssh service as prp
2) ssh service as prod
#? 2
$ connect localhost +test
```
