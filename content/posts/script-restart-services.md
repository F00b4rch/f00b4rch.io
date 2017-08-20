---
title: "Script restart services"
date: 2017-08-18T22:29:53+02:00
draft: false
tags: ['linux', 'bash', 'script']
---

Parfois, on se retrouve à devoir administrer des infrastructure de services supportées par des panneaux de gestion et... entre nous, j'ai une sainte horreur de `Plesk`, `Cpanel`, `Ispconfig`, `Webmin`...
Surtout quand dans certaines circonstances certains composants tombent. 
C'est souvent tellement *mal fait* et *bordelique* qu'il est difficile de s'y retrouver. Entre la gestion de logs juste abobinable (si si, j'ai déjà retrouvé des logs php dans `syslog`), une optimisation serveur inexistante... bon j'arrête ici car ce n'est pas le propos de ce poste.

En fait ce qui s'est passé dernièrement, c'est que j'ai découvert que sur une certaine version d'un certain panneau que je ne citerai pas, `php-fpm` se vautre misérablement et cela, de manière aléatoire. Biensur, pour que cela soit drôle, cet incident amène une coupure de service pour environ 200 utilisateurs, cool non ?

Du coup, un moment donné il faut dire f*ck et piloter le bousin correctement, donc on sort le `bash`.
Alors oui, vous me direz, pourquoi m'embêter, il n'y a qu'à utiliser un service du type `monit` ? Et bien j'ai testé mais non.

Donc, histoire de faire ca bien on commence notre script de la manière suivante.

```
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

#/ Usage: ./main.sh
#/ Description: This script monitore services
#/ Examples: ./main.sh
#/ Options:
#/   --help: Display this help message
usage() { grep '^#/' "$0" | cut -c4- ; exit 0 ; }
expr "$*" : ".*--help" > /dev/null && usage

readonly LOG_FILE="/tmp/$(basename "$0").log"
info()    { echo "$(date -u) [INFO]    $*" | tee -a "$LOG_FILE" >&2 ; }
warning() { echo "$(date -u) [WARNING] $*" | tee -a "$LOG_FILE" >&2 ; }
error()   { echo "$(date -u) [ERROR]   $*" | tee -a "$LOG_FILE" >&2 ; }
fatal()   { echo "$(date -u) [FATAL]   $*" | tee -a "$LOG_FILE" >&2 ; exit 1 ; }

# Debug
# Cleaning if exit
cleanup() {
    rm "$LOG_FILE"
}
trap cleanup EXIT
```

Jusqu'ici rien d'extraordinaire, on a notre shebang, différentes fonctions de logs et un service clean avec trap.

Comme j'aime bien être alerté des actions de mes scripts, on va déclarer une fonction d'envoi de mails :

```
# Func mail
sendMail() {
    echo "$(date) : $1 restarted on $HOSTNAME" | mail -s "$1 restarted on $HOSTNAME" "my@mail"
}
```

On va ensuite déclarer notre fonction pour un service donné (au hasard, un service qui ne tombe jamais...):

```
# Func php-fpm
php-fpm() {
if pgrep php5-fpm > /dev/null
then
    info "Php-FPM service is up."
:
else
       warning "Php-FPM service down, restarting it !"
       if /etc/init.d/php5-fpm restart > /dev/null
       then
           info "Php-FPM service restarted"
           sendMail "php-FPM"
       fi
fi
}
```

Ici, on vérifie si le service dispose bien d'un pid actif, auquel cas il est restart, on log et on envoit un mail.

On répète l'opération avec les autres services concernés si nécessaires et on call le main :

```
if [[ "${BASH_SOURCE[0]}" = "$0" ]]; then
    info "Starting..."

    while true; do

        # Functions call
        php-fpm
        sleep 5
        # vos autres fonctions ici

    done

fi
```

Ce qui donne en version complète : 

```
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

#/ Usage: ./main.sh
#/ Description: This script monitore Plesk Web services
#/ Examples: ./main.sh
#/ Options:
#/   --help: Display this help message
usage() { grep '^#/' "$0" | cut -c4- ; exit 0 ; }
expr "$*" : ".*--help" > /dev/null && usage

readonly LOG_FILE="/tmp/$(basename "$0").log"
info()    { echo "$(date -u) [INFO]    $*" | tee -a "$LOG_FILE" >&2 ; }
warning() { echo "$(date -u) [WARNING] $*" | tee -a "$LOG_FILE" >&2 ; }
error()   { echo "$(date -u) [ERROR]   $*" | tee -a "$LOG_FILE" >&2 ; }
fatal()   { echo "$(date -u) [FATAL]   $*" | tee -a "$LOG_FILE" >&2 ; exit 1 ; }

# Debug
# Cleaning if exit
# cleanup() {
#     rm "$LOG_FILE"
# }
# trap cleanup EXIT


# Functions 

    # Func mail
    sendMail() {
        echo "$(date) : $1 restarted on $HOSTNAME" | mail -s "$1 restarted on $HOSTNAME" "my@mail"
    }

    # Func nginx
    nginx() {
    if pgrep nginx > /dev/null
    then
#        info "Nginx service is up."
    :
    else
            warning "Nginx service down, restarting it !"
            if /etc/init.d/nginx restart > /dev/null
            then
                info "Nginx service restarted"
                sendMail "nginx"
            fi
    fi
    }

    # Func php-fpm
    php-fpm() {
    if pgrep php5-fpm > /dev/null
    then
#        info "Php-FPM service is up."
    :
    else
           warning "Php-FPM service down, restarting it !"
           if /etc/init.d/php5-fpm restart > /dev/null
           then
               info "Php-FPM service restarted"
               sendMail "php-FPM"
           fi
    fi
    }

    # Func mysql
    mysql(){
    if pgrep mysql > /dev/null
    then
#        info "Mysql service is up."
    :
    else
        warning "Mysql service down, restarting it !"
        if /etc/init.d/mysql restart > /dev/null 
        then
            info "Mysql service restarted"
            sendMail "mysql"
        fi
    fi
    }

    # Func apache
    apache(){
    if pgrep apache2 > /dev/null
    then
#        info "Apache2 service is up."
    :
    else
        warning "Apache2 service down, restarting it !"
        if /etc/init.d/apache2 restart > /dev/null 
        then
            info "Apache2 service restarted"
            sendMail "apache2"
        fi
    fi
    }

if [[ "${BASH_SOURCE[0]}" = "$0" ]]; then

    # If root :
	if [[ $EUID -ne 0 ]]; then
    	echo "This script must be run as root"
    	exit 1
    fi

    info "Starting..."

    while true; do

        # Functions call
        nginx    	
        sleep 5
        php-fpm
        sleep 5
        mysql
        sleep 5
        apache
        sleep 5

    done

fi
```

Bon ce n'est rien de très fou, mais je trouve que faire les choses manuellement permet de vraiment cibler exactement ce que l'on souhaite faire.

Comme d'habitude, [les sources sont dispos](https://github.com/F00b4rch/SandBox/blob/master/bash/PleskRestartSVC/main.sh) !

A bientôt !
