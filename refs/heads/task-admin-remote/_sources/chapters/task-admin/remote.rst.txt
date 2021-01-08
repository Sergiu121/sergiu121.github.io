Conectarea la workstation
=========================

Prima problemă cu care ne vom confrunta este conectarea la stație, deoarece, în majoritatea cazurilor, nu vom lucra în aceeași rețea cu sistemul la distanță și stația nu va avea o adresă IP publică, astfel încât să ne putem conecta direct la sistem folosind un protocol de comunicare la distanță, cum ar fi SSH fără să facem pași extra.

Câteva soluții de conectare la sistem pe care le vom aborda sunt:
* VPN
* tunel SSH
* DDNS

Problema adreselor IP private
-----------------------------

Adresele IPv4 au fost contruite pentru a fi identificatori unici în Internet pentru stații. Acestea pot să identifice maxim 2^32 stații în Internet. Însă, acum există mai multe dispozitive conectate la Internet decât există adrese IPv4 care se pot asigna pe dispozitive.

Pentru a se rezolva această problemă, au fost alese anumite intervale de IP-uri care pot fi atribuite pe mai multe dispozitive în același timp. Astfel, s-a rezolvat problema suprapunerii IP-urilor. Dezavantajul este că aceste adrese, ne mai fiind unice, nu mai pot să fie accesibile de oriunde din Internet.

Intervalele adreselor IPv4 private sunt următoarele:
* 10.0.0.0 – 10.255.255.255
* 172.16.0.0 – 172.31.255.255
* 192.168.0.0 – 192.168.255.255

Nicio adresă IP din intervalele de mai sunt nu poate fi accesată direct din Internet.

De exemplu, dacă încercăm să ne conectăm la sistemul ``remote`` de pe stația ``local``, trebuie mai întâi să aflăm adresa IP a stației ``local``:

.. code-block::

    root@remote:~# ip a s
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
    190: eth0@if191: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
        link/ether 02:42:0a:0a:0a:01 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 10.10.10.1/24 brd 10.10.10.255 scope global eth0
           valid_lft forever preferred_lft forever

Conform comenzii de mai sus reiese că adresa IP a stației remote este ``10.10.10.1``.

Vom verifica conectivitatea cu stația ``remote`` de pe stația ``local`` folosind comanda ``ping``:

.. code-block::

    root@local:~# ping -c 2 10.10.10.1
    PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.

    --- 10.10.10.1 ping statistics ---
    2 packets transmitted, 0 received, 100% packet loss, time 1010ms

    root@local:~# ping -c 2 141.85.241.99
    PING 141.85.241.99 (141.85.241.99) 56(84) bytes of data.
    64 bytes from 141.85.241.99: icmp_seq=1 ttl=61 time=9.02 ms
    64 bytes from 141.85.241.99: icmp_seq=2 ttl=61 time=8.58 ms

    --- 141.85.241.99 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1001ms
    rtt min/avg/max/mdev = 8.588/8.806/9.024/0.218 ms

Rezultatul comenzii primei rulări a comenzii ping demonstrează faptul că nu avem
conectivitate între stația ``local`` și stația ``remote``.
Totuși, am testat și conectivitatea cu o altă adresă IP din Internet, în cazul acesta folosind IP-ul stației ``fep.grid.pub.ro``, cu care putem să comunicăm, deci nu este o problemă de conectare la Internet.
Pentru a recapitula funcționalitatea utilitarului ``ping``, revizitați capitolul <TODO>

Conectarea prin VPN
-------------------

Prima soluție pentru conectarea la o stație care folosește o adresă IP privată o reprezintă serviciile de tip VPN.
Acestea conectează două stații care în mod fizic nu sunt conectate la aceeași rețea <TODO ref capitol rețea>

Pentru această soluție avem două moduri de organizare: folosind un server public pe care îl setăm noi drept server de VPN, sau folosirea unui serviciu public de VPN cum ar fi Hamachi sau ZeroTier.

Am folosit ca exemplu serviciile Hamachi sau ZeroTier, deoarece acestea pot fi folosite fără să fie cumpărate și sunt ușor de configurat.

<insert matrice/link cu avantaje și dezavantaje servicii>

Folosirea serviciului Hamachi
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pentru început, recomandăm folosirea serviciului Hamachi, deoarece acesta nu presupune înregistrarea unui cont pentru folosirea aplicației, dar Hamachi vine cu dezavantajul că putem să conectăm mai puține stații între ele și viteza conexiunii este mai mică decât dacă am folosi unele servicii plătite, cum ar fi OpenVPN, sau altele.

Instalarea Hamachi
""""""""""""""""""

Pentru instalarea aplicației Hamachi vom fi nevoiți să descărcăm pachetul.
Nu vom descărca pachetul folosind aplicația ``apt``, cum am învățat până acum, deoarece repository-ul de pachete folosit de Ubuntu nu conține pachetul pentru Hamachi.

Vom folosi comanda ``wget`` pentru a descărca pachetul:

.. code-block::

    root@remote:~# wget https://www.vpn.net/installers/logmein-hamachi_2.1.0.203-1_amd64.deb
    --2021-01-05 15:49:15--  https://www.vpn.net/installers/logmein-hamachi_2.1.0.203-1_amd64.deb
    Resolving www.vpn.net (www.vpn.net)... 64.95.128.197, 64.95.128.199
    Connecting to www.vpn.net (www.vpn.net)|64.95.128.197|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1613102 (1.5M) [application/vnd.debian.binary-package]
    Saving to: 'logmein-hamachi_2.1.0.203-1_amd64.deb'

    logmein-hamachi_2.1.0.203-1_amd64 100%[==========================================================>]   1.54M  4.14MB/s    in 0.4s    

    2021-01-05 15:49:16 (4.14 MB/s) - 'logmein-hamachi_2.1.0.203-1_amd64.deb' saved [1613102/1613102]
    root@remote:~# dpkg -i logmein-hamachi_2.1.0.203-1_amd64.deb
    Selecting previously unselected package logmein-hamachi.
    (Reading database ... 12216 files and directories currently installed.)
    Preparing to unpack logmein-hamachi_2.1.0.203-1_amd64.deb ...
    Unpacking logmein-hamachi (2.1.0.203-1) ...
    Setting up logmein-hamachi (2.1.0.203-1) ...
    mknod: /dev/net/tun: File exists
    Starting LogMeIn Hamachi VPN tunneling engine logmein-hamachi
    starting - success
    Processing triggers for systemd (245.4-4ubuntu3.3) ...

Am folosit comanda ``dpkg`` care gestionează aplicații sub forma pachetelor ``.deb``, specifice distribuțiilor Debian, Ubuntu și Mint.
Opțiunea ``-i`` specifică comenzii ``dpkg`` că vrem să instalăm o aplicație.

Verificăm că am instalat aplicația Hamachi folosind comanda ``hamachi`` în felul următor:

.. code-block::

    root@remote:~# hamachi
      version    : 2.1.0.203
      pid        : 42
      status     : offline
      client id  : 
      address    : 
      nickname   : 
      lmi account: 

Pentru a ne autentifica la serverele Hamachi, folosim comanda ``hamachi login``:

.. code-block::

    root@remote:~# hamachi login
    Logging in .......... ok
    root@remote:~# hamachi
      version    : 2.1.0.203
      pid        : 42
      status     : logged in
      client id  : 253-932-022
      address    : 25.114.254.180    2620:9b::1972:feb4
      nickname   : remote
      lmi account: -



Această comandă ne generează un identificator unic per stație și stabilește un nickname.
Rulând comanda ``hamachi`` ne vor fi afișate identificatorul, nickname-ul sistemului, adresa IP din VLAN și nickname-ul sistemului.

Exercițiu: Instalare Hamachi
""""""""""""""""""""""""""""

Instalați Hamachi pe stația ``local``.

Crearea unei rețele private
"""""""""""""""""""""""""""

Odată ce am instalat Hamachi pe ambele stații pe care vrem să le conectăm, trebuie să creăm o rețea privată prin care acestea două să comunice.

Vom crea rețeaua virtuală de pe stația ``remote``, astfel aceasta va fi administratorul rețelei.
Pentru a crea o rețea virtuală folosim comanda ``hamachi create`` împreună cu numele rețelei și parola acesteia.
Pentru demo-ul pe care îl urmăriți, folosiți în loc de șirul de caractere ``nume-prenume`` numele și prenumele vostru.

.. code-block::

    root@remote:~# hamachi create nume-prenume 12345667890
    Creating nume-prenume .. ok
    root@remote:~# hamachi list
     * [nume-prenume]  capacity: 1/5, subscription type: Free, owner: This computer

Am folosit comanda ``hamachi list`` pentru a verifica faptul că a fost creată rețeaua.
Comanda ``hamachi list`` afișează toate rețelele din care face parte stația.

Conectarea la o rețea
"""""""""""""""""""""

Pentru a conecta stația ``local`` la rețeaua privată vom folosi comanda ``hamachi join`` urmată de numele rețelei la care vom conecta stația și parola rețelei.

.. code-block::

    root@local:~# hamachi join nume-prenume 12345667890
    Joining nume-prenume .. ok
    root@local:~# hamachi list
     * [nume-prenume]  capacity: 2/5, subscription type: Free, owner: remote (253-932-022)
     * 253-932-022   remote                     25.114.254.180    alias: not set           2620:9b::1972:feb4                          via server  TCP


Rulând comanda ``hamachi list`` am afișat rețelele la care este conectată stația ``local`` și stațiile cu care împarte rețelele.
IP-ul stației ``remote`` din rețeaua privată este ``25.114.254.180``, conform rezultatului comenzii.

Pentru a testa conexiune dintre stațiile ``local`` și ``remote`` vom rula comanda ``ping``.

.. code-block::

    root@local:~# ping -c 2 25.114.254.180
    PING 25.114.254.180 (25.114.254.180) 56(84) bytes of data.
    64 bytes from 25.114.254.180: icmp_seq=1 ttl=64 time=76.5 ms
    64 bytes from 25.114.254.180: icmp_seq=2 ttl=64 time=92.3 ms

    --- 25.114.254.180 ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1003ms
    rtt min/avg/max/mdev = 76.592/84.493/92.394/7.901 ms

Exercițiu: Crearea și folosirea unei rețele private
"""""""""""""""""""""""""""""""""""""""""""""""""""

Creați o nouă rețea privată numită ``prenume-nume``, cu parola de acces
``anaaremere``. Conectați stațiile ``local`` și ``remote`` la noua rețea.

Folosirea unui VPN privat
^^^^^^^^^^^^^^^^^^^^^^^^^

Pentru folosirea unui VPN privat este nevoie să avem o stație în internet la care să avem acces atât din stația de pe care lucrăm, cum ar fi laptopul, dar și din workstation.
Putem obține astfel de stații cumpărân acces la o mașină virtuală, cum ar fi într-un serviciu de hosting, cum ar fi AWS, DigitalOcean, Microsoft Azure sau Linode.

Odată ce am obținut o stație cu un IP public, este nevoie să setăm un serviciu de VPN. Pentru aceasta putem folosi infrastructura pe care am folosit-o deja la laboratorul de rețelistică. Aceasta pornește un server OpenVPN pe sistem.

Conectarea folosind un tunel SSH
--------------------------------

Abordările pe care le-am discutat mai sus presupun accesul la un server central, dar și drepturi de administrator din partea utilizatorului pentru instalarea aplicațiilor cum ar fi Hamachi sau OpenVPN.
Dacă nu avem acces la un cont de administrator, nu putem să ne conectăm la stația de la distanță.

O alternativă care nu presupune drepturi administrative sunt tunelele SSH.
Un tunel SSH reprezintă o conexiune între două stații facilitată de programul SSH.
Astfel, în loc să trimitem comenzi prin SSH către o stație, putem trimite orice tip de mesaj, pentru orice aplicație, nu numai SSH între orice două porturi.

Dezavantajul acestei abordări este că necesită accesul la un server terț care să fie accesibil de ambele stații.

<TODO Insert schemă>

În această subsecțiune vom lucra cu 3 stații care sunt distribuite în felul următor:
* ``ssh-local``, reprezintă stația "locală", adică laptop-ul de pe care ne-am conecta, dacă ar fi vorba de un scenariu real; are o singură interfață de rețea cu adresa IP 10.10.10.3;
* ``ssh-remote``, reprezintă workstation-ul la care vrem să ne conectăm; are o singură interfață cu IP-ul 10.11.11.3;
* ``ssh-server``, reprezintă serverul terț prin care ne vom conecta ca să ajungem la workstation; această stație are două interfețe conectate la ea, cu IP-urile ``10.10.10.2`` și ``10.11.11.3``, dar în realitate aceasta ar avea o singură placă de rețea.

Stațiile ``ssh-local`` și ``ssh-remote`` nu au conectivitate între ele, dar au conectivitate la stația ``ssh-server``, deoarece sunt în aceeași rețea

Inițializarea tunelului SSH
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Primul pas necesar pentru conectarea la o stație prin tunel SSH este inițializarea tunelului.
Atunci când inițializăm tunelul acesta va deschide portul ``6969`` pe stația ``ssh-server`` de la adresa ``10.11.11.2`` astfel încât mesajele trimise către portul ``6969`` vor fi trimise către portul ``22`` de pe stația ``ssh-remote`` , stația de pe care inițializăm tunelul SSH.

Pentru a deschide tunelul vom folosi comanda următoare:

.. code-block::

    root@ssh-remote:~# ssh -N -R 6969:localhost:22 root@10.10.10.2
    The authenticity of host '10.10.10.2 (10.10.10.2)' can't be established.
    ECDSA key fingerprint is SHA256:xV1orHYj4fhkc5HE91sfh8QhaVqke/AEMa8mYI423HY.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.10.10.2' (ECDSA) to the list of known hosts.
    root@10.10.10.2's password:

Opțiunile comenzii ``ssh`` folosite sunt următoarele:
* ``-N`` este folosită atunci când deschidem tunele pentru a nu deschide shell-uri în care să dăm comenzi;
* ``6969`` este portul pe care vrem să îl deschidă pe stația ``ssh-server``;
* ``localhost`` este stația către care vor fi trimise mesajele primite pe portul ``6969``. În cazul acesta mesajele vor fi trimise către ``localhost``, adică stația ``ssh-remote``, cea de pe care rulăm comanda de tunelare;
* ``22`` este portul de pe stația ``localhost`` către care vrem să fie trimise mesajele primite pe portul ``6969`` de pe stația ``ssh-server``;
* ``root@10.10.10.2``, utilizatorul și IP-ul stației către care vrem să deschidem tunelul; în cazul acesta este IP-ul stației ``ssh-server``.

Putem să verificăm că a fost deschis portul ``6969`` pe stația ``ssh-server``
rulând comanda ``netstat -tlpn``:

.. code-block::

    root@ssh-server:~# netstat -tlpn
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      24/sshd
    tcp        0      0 127.0.0.1:6969          0.0.0.0:*               LISTEN      99/sshd: root
    tcp        0      0 127.0.0.11:42991        0.0.0.0:*               LISTEN      -
    tcp6       0      0 :::22                   :::*                    LISTEN      24/sshd


Cât timp această fereastră rămâne deschisă, tunelul va fi activ. Vom învăța în secțiunea <TODO> cum să rulăm această comandă în afara terminalului și cum să ne asigurăm că tunelul este mereu deschis.

Folosirea tunelului SSH
^^^^^^^^^^^^^^^^^^^^^^^

Odată creat tunelul, vrem să îl folosim de pe stația ``ssh-local``, pentru a putea rula comenzi pe stația ``ssh-remote``.

Pentru a face asta, trebuie să ne conectăm la stația ``ssh-server``. Serverul ``ssh-server`` poate fi accesat la adresa ``10.11.11.2``.

.. code-block::

    root@ssh-local:~# ssh root@10.11.11.2
    root@10.11.11.2's password:
    Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 5.4.0-52-generic x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    This system has been minimized by removing packages and content that are
    not required on a system that users do not log into.

    To restore this content, you can run the 'unminimize' command.
    Last login: Tue Jan  5 23:08:53 2021 from 10.11.11.3
    root@ssh-server:~#

După cum am observat mai sus, în rezultatul comenzii ``netstat``, tunelul SSH care trimite mesaje către stația ``ssh-remote`` ascultă pe portul ``6969``.

Pentru a ne conecta la acest port folosind clientul SSH, rulăm următoarea comandă:

.. code-block::

    root@ssh-server:~# ssh root@localhost -p 6969
    The authenticity of host '[localhost]:6969 ([127.0.0.1]:6969)' can't be established.
    ECDSA key fingerprint is SHA256:xV1orHYj4fhkc5HE91sfh8QhaVqke/AEMa8mYI423HY.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '[localhost]:6969' (ECDSA) to the list of known hosts.
    root@localhost's password:
    Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 5.4.0-52-generic x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    This system has been minimized by removing packages and content that are
    not required on a system that users do not log into.

    To restore this content, you can run the 'unminimize' command.

    The programs included with the Ubuntu system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
    applicable law.

    root@ssh-remote:~#

Am folosit opțiunea ``-p`` pentru a ne conecta folosind SSH pe un alt port decât portul predefinit (22).
În comanda de mai sus ne-am conectat la stația ``localhost``, adică stația ``ssh-server``, dar deoarece portul ``6969`` este de fapt un tunel, conexiunea a fost trimisă la stația ``ssh-remote``.

Observăm că promptul s-a schimbat în ``root@ssh-remote:~#``, deci ne-am conectat la stația ``ssh-remote``. este de fapt un tunel, conexiunea a fost trimisă la stația ``ssh-remote``.

Exercițiu: Crearea și folosirea tunelelor SSH
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Creați un tunel SSH care să ducă de la stația ``ssh-local`` la ``ssh-server`` folosind portul 6970 și conectați-vă la stația ``ssh-local`` din stația ``ssh-remote`` folosindu-vă de stația ``ssh-server`` ca mai sus.
