Configurarea și administrarea serviciilor
=========================================

Serviciile sunt aplicații care rulează în continuu pe stație, spre deosebire de
comenzi, cum ar fi `find` sau `ls` care rulează cât timp se execută comanda.
Scopul serviciilor este să ruleze în continuu și să primească cereri de la
aplicații client, cum ar fi un client `ssh` și să răspundă la aceste cereri.
Deoarece ne dorim să avem majoritatea timpului conectivitate la mașină, sau ne
dorim să avem acces la paginile web folosite, serverele care execută comenzi
primite de la clienți rulează fără să se oprească.

Vrem să pornim diversele servicii din sistem care sunt instalate pe propria
stație. Avem nevoie de o interfață unică, cu o sintaxă minimală, care ne poate
permite să gestionăm serviciile pe sistem, cum le pornim, oprim și cum putem să
generăm noi propriile servicii.

Interfața oferite de toate distribuțiile moderne este systemd. Comanda folosită
pentru gestionarea serviciilor este `systemctl`.

Lucrul cu serviciile în Linux
-----------------------------

Inițial ne vom uita la servicii predefinite în Linux. Vom preciza cum se
instalează serviciile predefinite.

Vom verifica starea de funcționare a serviciului SSH, deoarece știu și au auzit
deja de el.

Oprirea unui serviciu
^^^^^^^^^^^^^^^^^^^^^

Îi punem să oprească serviciul SSH și testează dacă mai merge SSH-ul

Pornirea unui serviciu
^^^^^^^^^^^^^^^^^^^^^^

Îi punem să pornească serviciul SSH și testează dacă merge SSH-ul.

Repornirea unui serviciu
^^^^^^^^^^^^^^^^^^^^^^

Îi pune să schimbe `PermitRootLogin` din fișierul de configurare și îi punem să
oprească și să îl repornească, pentru a vedea că opțiunea și-a făcut efectul


Definirea unui serviciu personalizat
------------------------------------

Îi vom pune să își facă singuri un serviciu care face proxy ssh într-un
container în care rulează o pagină web care ascultă doar pe localhost. Vor putea
să acceseze acest proxy din browser.
