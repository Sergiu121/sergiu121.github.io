Gestionarea spațiului de stocare partajat
=========================================

O componentă importantă a mediului de lucru este spațiul de stocare.
Cu toate că noi vom rula aplicații pe serverul de la distanță, noi avem nevoie de acces la spațiul de stocare al acestuia, deoarece vrem ca într-un final să urmărim rezultatul procesării și eventual să îl analizăm folosind diferite utilitate grafice.
O altă nevoie pe care o avem este editarea codului la distanță, deoarece majoritatea programatorilor folosesc IDE-uri în mediu grafic, care nu pot rula mereu eficient de la distanță.
Soluția la această nevoie este să partajăm spațiul de stocare între serverul de la distanță și laptopul sau stația locală de pe care lucrăm.

Stocare partajată folosind SSHFS
--------------------------------

SSHFS este o soluție de stocare partajată care permite montarea unui sistem de fișiere care nu este legat fizic la stația curent ci se folosește de protocolul SSH pentru a transmite operațiile asupra fișierelor prin rețea către un sistem de fișiere conectat prin rețea.

Avantajul folosirii SSHFS este că acesta nu necesită descărcarea sistemului de fișiere de la distanță, deci nu duce la duplicarea fișierelor.
Dezavantajul acestei abordări este faptul că dacă pierdem conexiunea la sistemul de fișiere de la distanță, nu mai avem acces la fișiere.

Montarea temporară a unui sistem de fișiere
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pentru a monta sistemul de fișiere de pe un alt sistem, vom folosi comanda ``sshfs``

.. code-block::

    root@local:~# ls /mnt/
    root@local:~# sshfs root@10.10.10.2:/ /mnt/
    The authenticity of host '10.10.10.2 (10.10.10.2)' can't be established.
    ECDSA key fingerprint is SHA256:xV1orHYj4fhkc5HE91sfh8QhaVqke/AEMa8mYI423HY.
    Are you sure you want to continue connecting (yes/no)? yes
    root@10.10.10.2's password:
    root@local:~# df -h
    Filesystem         Size  Used Avail Use% Mounted on
    overlay             16G   14G  539M  97% /
    tmpfs               64M     0   64M   0% /dev
    tmpfs              2.0G     0  2.0G   0% /sys/fs/cgroup
    /dev/sda5           16G   14G  539M  97% /etc/hosts
    shm                 64M     0   64M   0% /dev/shm
    root@10.10.10.2:/   16G   14G  539M  97% /mnt
    root@local:~# ls /mnt/
    bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

Comanda de mai sus a montat arborele de fișiere cu rădăcina în directorul ``/`` de pe sistemul de la adresa IP ``10.10.10.2`` în directorul ``/mnt`` de pe stația locală, cu numele ``local``, autentificându-se ca utilizatorul ``root``.

Am folosit comanda ``df`` pentru a afișa informații despre toate sistemele de fișiere montate pe stația locală.

.. admonition:: Observație

    Sintaxa comenzii ``sshfs`` se aseamănă cu sintaxa comenzii ``scp``.

Acest mod te montare al sistemului de fișier este temporară. Atunci când vom opri stația sistemul de fișiere va fi demontat.

Exercițiu: Montarea temporară a unui sistem de fișiere
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#) Montați temporar sistemul de fișiere cu rădăcina în directorul ``/`` de pe stația ``10.10.10.4`` în directorul ``/mnt/vol1``.
#) Montați temporar sistemul de fișiere cu rădăcina în directorul ``/home/student`` de pe stația ``10.10.10.2`` în directorul ``/mnt/vol2``.

Montarea persistentă a unui sistem de fișiere
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Deoarece nu ne dorim să rulăm comanda ``sshfs`` atunci când vrem să folosim un sistem de fișiere la distanță, vrem să montăm persistent sistemul de fișiere de la distanța, astfel încât această montare să persiste după oprirea stației locale.

Ca să montăm sistemul de fișiere persistent avem nevoie să ne copiem cheia ssh pe stația de la distanța, deoarece montarea se va face în mod neinteractiv, deci nu vom avea posibilitatea de a introduce parola. <TODO REF NETWORKING>

Pentru a monta sistemul de fișiere în mod persistent vom scrie o intrare în fișierul ``/etc/fstab`` care va conține detalii despre sistemul de fișiere pe care vrem să îl montăm. Pentru a monta sistemul de fișiere ``/``` de pe sistemul de la adresa IP ``10.10.10.2`` în directorul ``/mnt`` de pe stația locală, autentificându-ne ca utilizatorul ``root`` vom folosi următoarele comenzi.

.. code-block::

    root@local:~# ssh-keygen
    [...]
    root@local:~# ssh-copy-id root@10.10.10.2
    [...]
    root@local:~# echo root@10.10.10.2:/  /mnt  fuse.sshfs  defaults  0  0 > /etc/fstab
    root@local:~# mount -a

Am scris în fișierul ``/etc/fstab`` folosind comanda ``echo``, iar pentru a monta sistemul de fișiere am folosit comanda ``mount`` cu opțiunea ``-a`` pentru montarea sistemelor de fișiere descrise în fișierul ``/etc/fstab``.

Exercițiu: Montarea persistentă a unui sistem de fișiere
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#) Montați persistent sistemul de fișiere cu rădăcina în directorul ``/`` de pe stația ``10.10.10.4`` în directorul ``/mnt/vol1``.
#) Montați persistent sistemul de fișiere cu rădăcina în directorul ``/home/student`` de pe stația ``10.10.10.2`` în directorul ``/mnt/vol2``.

Stocare partajată folosind aplicații online
-------------------------------------------

SSHFS nu este o soluție bună pentru a face backup fișierelor, deoarece existând o singură replică, ai șters un fișier și au dispărut toate replicile.
Pe lângă asta, dacă ai Internet slab, ai acces greu la fișiere (le vrei și local).
Și, pe lângă asta, trebuie să ai SSH configurat, care poate necesita tunel etc.

O alternativă pentru acest serviciu sunt soluții cum ar fi GoogleDrive, Dropbox, ownCloud sau OneDrive, care for stoca o replică a fișierului pe toate calculatoarele autentificate de pe un anumit cont.
Avantajul aici este că aceste sisteme oferă suport pentru controlul versiunilor pentru a șterge modificarea anterioară.
Cu dezavantajul că trebuie configurate.
Și cu dezavantajul că acum informația este duplicată: dublu spațiu ocupat și pot apărea conflicte la modificări.

Stocarea partajată folosind Dropbox
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Dropbox este o soluție care se folosește de un server în Internet care stochează fișierele noastre, ca apoi acestea să fie replicate pe fiecare calculator client.

Este necesar să ne creăm un cont pentru a folosi serviciul Dropbox și să îl activăm.

Dropbox oferă o aplicație care rulează în linie de comandă pe care o vom descărca pentru a sincroniza sistemul de fișiere de pe serverele Dropbox într-un director local.

Pentru a descărca aplicația Dropbox, pe care o vom descărca folosind comanda ``wget`` și o vom instala folosind comanda ``dpkg`` împreună cu parametrul.

.. code-block::

    student@uso:~$ wget https://www.dropbox.com/download?dl=packages/ubuntu/dropbox_2020.03.04_amd64.deb -O dropbox.deb
    [...]
    student@uso:~$ sudo dpkg -i dropbox.deb 
    Starting Dropbox...
    Dropbox is the easiest way to share and store your files online. Want to learn more? Head to https://www.dropbox.com/

    In order to use Dropbox, you must download the proprietary daemon. [y/n] y
    [...]
    student@uso:~$ dropbox start
    To link this computer to a Dropbox account, visit the following url:
    https://www.dropbox.com/cli_link_nonce?nonce=ffd7d648a2ca2302d1177c0c389e87bd

.. admonition::

    Am folosit opțiunea ``-O`` a comenzii ``wget`` pentru a salva fișierul descărcat cu numele ``dropbox.deb``.

Pentru a instala ultimele componente ale clientului Dropbox este necesar să rulăm comanda ``dropbox start -i``.
După ce am ponit aplicația, este necesar să înregistrăm stația online la contul nostru.
Vom face asta accesând linkul afișat de aplicație.

Exercițiu: Stocarea partajată folosind Dropbox
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#) Conectați-vă la stația ``dropbox`` și porniți aplicația Dropbox pe aceasta.
#) Scrieți un fișier numit ``hello.txt`` mesajul ``Hello from remote`` de pe stația ``dropbox``.
Verificați conținutul fișierului de pe stația ``uso``.

Extra: Stocarea partajată folosind un server privat
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Îi vom pune să instaleze un container cu ownCloud și să îl configureze astfel încât să îl folosească ca o alternativă la Dropbox.
