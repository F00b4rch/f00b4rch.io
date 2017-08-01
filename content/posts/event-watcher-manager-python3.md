---
title: "Event Watcher Manager Python3"
date: 2017-07-30T12:53:16Z
tags: [python, dev, pyinotify, linux]
draft: false
---

Dans le cadre d'une demande spécifique, j'ai été amené à travailler sur l'élaboration d'un programme automatisant certaines actions via SFTP.  
Le principal enjeu technique de cette demande repose sur le fait que le protocole SFTP ne dispose pas de système de journalisation.  

J'avais entendu parler de [la librairie pyinotify](https://github.com/seb-m/pyinotify/blob/master/python3/pyinotify.py) alor s c'est vers cette dernière que je me suis lancé.  
**Le projet est présenté dans son mécanisme primaire**, pour plus de détails, je vous invite à prendre connaissance des sources.  

#### Demandes techniques concernant le projet

<u>Détails du contexte de réalisation :</u>  
Le programme doit surveiller les actions d'utilisateurs SFTP. Ces derniers ont leurs HomeDir qui sont des montages NFS.  
L'utilisateur peut déposer n'importe quoi sur son compte mais le programme doit détecter les dépôts de vidéos (sous certains formats) puis effectue r une série d'actions successives.  

<u>Le fonctionnement doit être le suivant :</u>  
Pour chaque dépôt de vidéo détecté, la vidéo doit vérifier le format autorisé puis doit être convertie en .flv, disposer de métadonnées, avoir un scree nshot (miniatures vidéos) et un mail doit être envoyé à l'adresse mail de l'utilisateur SFTP.  

#### Préparation

Plusieurs détails spécifiques sont contraignants pour commencer. Le principal étant l'association du mail de l'utilisateur du compte à son propre mail avec la détection du dépôt vidéo.  
C'est pourquoi j'ai fait le choix de partir sur un fichier statique, connaissant à l'avance la liste des utilisateurs.  

    # Création de la classe
    class Person:
        def __init__(self, name, surname, login, homedir, email, realpath):
            self.name = name
            self.surname = surname
            self.login = login
            self.homedir = homedir
            self.email = email
            self.realpath = realpath
    #
    # Et instanciation des utilisateurs
    #
    user_test = Person(
            'Test',
            'test',
            'test',
            '/test/test/test.fr',
            'test@test.fr',
            '/test.fr/')

Ensuite j'ai travaillé sur le _cœur_ du programme. Tout d'abord pour récupérer les informations de mon fichier utilisateur et effectuer un regroup ement par liste  

    # Définition des listes
    user_list = []
    user_path = []
    user_mail = []
    user_realpath = []
    # On appelle le fichier utilisateur pour implémenter les listes
    ### LOGIN ###
    for users.obj in gc.get_objects():
        if isinstance(users.obj, users.Person):
            user_list.append(users.obj.login)
    ### DOCUMENT_ROOT ###
    for users.obj in gc.get_objects():
        if isinstance(users.obj, users.Person):
            user_path.append(users.obj.homedir

    ### EMAIL ###
    for users.obj in gc.get_objects():
        if isinstance(users.obj, users.Person):
            user_mail.append(users.obj.email)
    ### REALPATH ###
    for users.obj in gc.get_objects():
        if isinstance(users.obj, users.Person):
            user_realpath.append(users.obj.realpath)

La définition d'une première fonction dont le but sera le call associatif avec un système de position ID.  

    def owner_func():
        for filename in multiple_file_types('*.avi', '*.mov', '*.mp4', '*.mpg', '*.wmv'):
            # On affiche l'utilisateur
            owner = pwd.getpwuid(os.stat(filename).st_uid).pw_name
            # On vérifie que ce dernier est bien présent dans notr liste
            if owner in user_list:
                print('The owner of file is :   ' + owner)
                position = user_list.index(owner)
                print('Owner_login_path :' + user_path[position])
                mail = user_mail[position]
                mailp = print(mail)
                realpath = user_realpath[position]
                return(realpath)
            else:
                print('The owner was not found.')

#### L'introduction avec Pyinotifier

Pyinotifier dispose de fonctions en rapport avec les créations et suppressions de données (IN_CREATE et IN_DELETE).  
Une fois le paramétrage de base effectué, je me suis servi de la définition de base pour implémenter avec la fonction **owner_func()**.  

    class EventHandler(pyinotify.ProcessEvent):
        def process_IN_CREATE(self, event):
            print('\n\n===========================')
            print(time.strftime("%d/%m/%Y %H:%M:%S"))
            evp = print('Path complet :' + '\'' + event.pathname + '\'')
            evn = print('Objet crée : ' + '\'' + event.name + '\'')
            Ouser = event.pathname.split('/')[2]
            print('Utilisateur : ' + Ouser)
            Oplace = [i for i,x in enumerate(user_list) if x == Ouser][0]
            Orelatif = user_realpath[Oplace]
            Omail = user_mail[Oplace]
            print('Chroot Sftp : ' + Orelatif)
            print('Mail : ' + Omail)
            command = 'convert.bash '+str(event.pathname)
            previous_size=0
            upload_finished = False
            try:
                while True:
                    time.sleep(1)
                    size=os.stat(event.pathname).st_size
                    print(previous_size)
                    print(size)
                    if size == previous_size:
                        break
                    else:
                        previous_size = size
            except:
                return False
            print(command)

            os.rename(event.pathname, event.pathname.replace(" ", "_"))
            neventpathname = event.pathname.replace(' ', '_')
            neventname = event.name.replace(' ', '_')
            print(neventpathname + neventname)
            p1 = subprocess.Popen(command, shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
            print('convert.bash', neventpathname)
            print(p1.stdout.read())
            p1.wait()
            p2 = subprocess.Popen(['generatemetadata.bash', neventpathname])
            p2.wait()
            p3 = subprocess.Popen(['extractpng.sh', neventpathname])
            p3.wait()
            if neventname.endswith('.html') :
                p5 = subprocess.Popen(['mail.bash', Omail, Orelatif,  neventpathname])
                print('MAIL SENT')
            print('===========================')
        def process_IN_DELETE(self, event):
            print('\n\n===========================')
            print(time.strftime("%d/%m/%Y %H:%M:%S") + "    Deleting:", event.pathname)
            print('===========================')

**Explications :**  
Le principe de Pyinotify est de créer une action automatique à un événement donné. Prenons le dépôt d'une vidéo correspondant au bon format comme étant l'événement.  
La fonction **IN_CREATE** se déclenche donc et va donc nous communiquer des premiers renseignements dont : la date de création, le chemin complet de l'événement, le chemin relatif (HomeDir de l'utilisateur SFTP), l'utilisateur, son mail...  

Dans un second temps on vient appliquer la conversion au bon format (avec **ffmpeg**). Il nous faut néanmoins nous assurer que la vidéo est bien complèteme t déposée. Cela correspond au bloc :  

            command = 'convert.bash '+str(event.pathname)
            previous_size=0
            upload_finished = False
            try:
                while True:
                    time.sleep(1)
                    size=os.stat(event.pathname).st_size
                    print(previous_size)
                    print(size)
                    if size == previous_size:
                        break
                    else:
                        previous_size = size
            except:
                return False
            print(command)

Suite à quoi on va pouvoir lancer successivement les scripts bash en subprocess.  

#### Intérêts

Les intérêts sont multiples !  
Le premier est évident puisqu'il est à présent possible d'effectuer une journalisation sur un service qui n'en disposait pas nativement. Le second est qu'il est possible très simplement de mettre en place un système de logs et PID.  

    notifier.loop(daemonize=True, callback=on_loop_func, pid_file='logs/pyinotify.pid', stdout='logs/%s.log' % timestr)

Cela ouvre une nouvelle porte très intéressante sur l'automatisation d'event par un sous-programme Python qui sortirait du travail fait par le kernel.  

Je pensais justement à faire une sorte d'API appelant ce type de fonctionnement scalable sur différents environnements.  
Ce projet étant dan s ma fameuse **ToDoList** se rapprocherait beaucoup d'un système en Master-Slaving avec certainement une imitation de ce qui existe déjà avec Puppet ... mais en Python.  

Wait and see de ce qui se fera !
