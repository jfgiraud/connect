# gorc

Ce programme est un utilitaire de connexion centralisé en ligne de commande (Bash).

Il est configurable, extensible et met à disposition un mini-moteur de recherche.

### installation

```
.
├── bin
│   └── connect
├── .connect
└── .connectrc
```

Le script `connect` est à placer dans le PATH.

Les fichiers `.connect` et `.connectrc` dans sa HOMEDIR.

### exemple de configuration 

Le script `.connect` définit les fonctions réutilisables (certaines fonctions sont déjà renseignées) et le fichier `.connectrc` les configurations (et surchages de fonctions).

Pour le moment, les fonctionnalités de base sont :
- la connexion SSH
- la connexion à une base de donnée MySQL (en passant par un tunnel SSH ou non)

Exemple de fichier `.connectrc` :

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

### exemple d'utilisation

La connexion se fait en recherchant des mots clés dans le nom des fonctions. Si plusieurs items correspondent, un prompt demande de préciser.

Le séparateur des mots clés est le caractère `_`.

Exemples :

```
$ go sql prp
$ go localhost -test
$ go ssh as
1) ssh service as prp
2) ssh service as prod
#? 2
$ go localhost +test
```
