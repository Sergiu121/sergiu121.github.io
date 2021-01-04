Conectarea la workstation
=========================

Prima problemă cu care se vor confrunta este cum se vor conecta la mașina. De ce
este asta problematic? Deoarece majoritatea oamenilor nu au acces la o mașina cu
o adresă IP publică

Prima problemă cu care ne vom confrunta este conectarea la mașina virtuală,
deoarece, în majoritatea cazurilor, nu vom lucra în aceeași rețea cu sistemul la
distanță și mașina nu va avea o adresă IP publică, astfel încât să ne putem
conecta direct la mașină folosind un protocol de comunicare la distanță, cum ar
fi SSH fără să facem pași extra.

<insert demo despre adrese private>

Conectarea prin VPN
-------------------

Prima soluție pentru conectarea la o stație din exterior o reprezintă serviciile
de tip VPN. Acestea conectează două stații care în mod fizic nu sunt conectate
la aceeași rețea.

Pentru această soluție avem două moduri de organizare: folosind un server public
pe care îl setăm noi drept server de VPN, sau folosirea unui serviciu public de
VPN cum ar fi Hamachi sau ZeroTier.

Am folosit ca exemplu serviciile Hamachi sau ZeroTier, deoarece acestea pot fi
folosite fără să fie cumpărate și sunt ușor de configurat.

<insert matrice/link cu avantaje și dezavantaje servicii>

Folosirea serviciului Hamachi
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pentru început, noi recomandăm folosirea serviciului Hamachi, deoarece acestea
nu presupune înregistrarea unui cont pentru folosirea aplicației, dar asta vine
cu dezavantajul că putem să conectăm mai puține stații între ele și viteza
conexiunii este mai mică decât dacă am folosii unele servicii plătite, cum ar fi
OpenVPN, sau altele.

Instalarea Hamachi
""""""""""""""""""

Vom urmări instrucțiunile de aici:
https://support.logmeininc.com/central/help/how-to-install-the-client-to-a-local-computer-central-t-hamachi-add-attached-local

Crearea unei rețele private
"""""""""""""""""""""""""""

.. code-block::

    root@workstation:~$ sudo hamachi create nume-prenume 12345667890

Conectarea la o rețea
"""""""""""""""""""""

.. code-block::

    student@uso:~$ sudo hamachi join nume-prenume 12345667890

Folosirea unui VPN privat
^^^^^^^^^^^^^^^^^^^^^^^^^

Le vom da un reminder despre ce înseamnă un VPN privat, hostat de ei și îi vom
trimite să vadă instrucțiunile de la laboratorul de networking pentru a își face
setupul.

Conectarea folosind un tunel SSH
--------------------------------

Le vom lăsa instrucțiuni despre cum să facă un tunel printr-un server. Problema
aici e că nu știm dacă toți au acces la un server terț prin care să facă ssh. Le
putem arăta un demo didactic, iar la facultate pot să folosească fep, dar ceva
practic pentru toți nu există.

Folosirea tmux
--------------

Atunci când ne conectăm la un calculator prin SSH și rulăm
comenzi, acestea vor rula în foreground. Dacă avem o aplicație care rulează mult
timp, cum ar fi un find, și coexiunea SSH se întrerupe, se va întrerupe și
execuția comenzii find. Pentru a rezolva această problemă, vom folosi aplicația
tmux.

Aceasta ne pornește o sesiune de shell care este independentă de terminalul în
care rulează, astfel, putem să ne conectăm și să ne deconectăm de la ea. (nu
știu cum o să fac un demo rezonabil cu tmux, dar vedem)

Crearea unei sesiuni tmux
^^^^^^^^^^^^^^^^^^^^^^^^^

Detașarea de la o sesiune tmux
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Reatașarea la o seiune tmux
^^^^^^^^^^^^^^^^^^^^^^^^^^^
