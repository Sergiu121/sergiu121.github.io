Configurarea stației la distanță
================================

După ce am realizat o conexiune către stația pe care vom lucra, trebuie să ne pregătim mediul de lucru.
Configurația mediului de lucru depinde de propriile gusturi și aplicațiile pe care le folosește fiecare.

Configurarea mediului de lucru este recomandată pentru a adapta sistemul la propriile nevoi. De exemplu, o persoană care va lucra foarte mult cu repository-uri de tip git, probabil va avea nevoie de un prompt care să îi afișeze pe ce branch lucrează, astfel încât să gestioneze mai ușor branchurile. Aceste modificări au rolul de a reduce acțiunile repetitive pe care le facem sau de a pregăti aplicații pe care le vom folosi mai departe.

Noi vă vom recomanda niște modificări asupra sistemului la distanță, dar acestea nu sunt în niciun fel obligatorii pentru toți ci sunt mai de grabă sugestii după care vă puteți inspira pentru a vă configura mai departe propriul mediu de lucru.

Configurarea shellului
----------------------

Primul aspect pe care îl vom modifica la mediul de lucru este shellul în care rulăm comenzi, deoarece acesta este cea mai folosită unealtă, fiecă că edităm cod, sau administrăm sisteme, acesta este locul unde rulăm comenzi.

Modificările la nivelul shellului se fac schimbând variabile de mediu sau rulând comenzi înainte de rularea efectivă a shell-ului.

Pentru a modifica mediul shellului, trebuie să facem asta într-un fișier de configurare. Noi vom folosi fișierul ``~/.profile``, deoarece acesta este citit și rulat de toate implementările de shell majorore, cum ar fi ``dash``, ``csh``, ``bash`` sau ``zsh``, astfel oferă intercompatibilitate între shelluri.

Modificarea shellului predefinit
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ne dorim să modificăm shellul predefinit pe care îl folosește sistemul, deoarece fiecare implementare de shell oferă propriile funcționalități.
De exemplu, ``csh``, ``bash`` și ``zsh`` fiecare implementează un mecanism de auto-completion diferit, astfel completarea se face în mod diferit atunci când apăsăm tasta ``TAB``.

Pentru a modifica shellul predefinit al unui utilizator folosim comanda
``usermod`` cu opțiunea ``-s`` în felul următor:

.. code-block::

Rulând comanda de mai sus, am modificat shellul predefinit al utilizatorului ``student`` în ``/bin/csh``. Pentru e verifica că s-a efectuat operați corect, ne-am autentificat ca utilizatorul student și am afișat valoarea variabilei ``SHELL``.

Exercițiu: Modificarea shellului predefinit:
""""""""""""""""""""""""""""""""""""""""""""

Modificați shellul predefinit al utilizatorului ``student`` cu shellul ``/bin/zsh``. Încercați să folosiți funcționalitatea de auto-complete. Ce observați?

Configurarea aliasurilor
^^^^^^^^^^^^^^^^^^^^^^^^

Există comenzi pe care le rulăm frecvent. Totuși, unele comenzi sunt lungi și durează mult timp să le tastăm de fiecare dată atunci când vrem să le rulăm. Pentru a rezolva această problemă, putem folosi aliasuri. Un alias este o prescurtare pentru o comandă.

Vom folosi comanda ``alias`` pentru defini un alias.

.. code-block::

        student@uso

Exercițiu: Configurarea aliasurilor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#) Configurați aliasul ``gcs`` pentru comanda ``git commit --signnoff``.
#) Configurați aliasul ``glog`` pentru comanda ``git log --oneline``.


Modificarea dimensiunii istoricului de comenzi
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fișierul de istoric al unui shell este locul unde sunt salvate comenzile rulate într-ul shell până la delogare.
Acest fișier este folositor pentru repetarea acțiunilor.

De exemplu, funcționalitatea de căutare inversă din shell bazată pe ``Ctrl+r`` caută în istoricul shell-ului, așa că este de preferat să avem un istoric complet în care să căutăm pentru a avea mai multe intrări în care să căutăm.

Pentru a mări dimensiunea istoricului este suficient să setăm variabila de mediu ``HISTSIZE`` într-un fișier de configurare. Noi vom folosi fișierul de configurare ``.profile`` din motivele enumerate mai sus.


Configurarea promptului
^^^^^^^^^^^^^^^^^^^^^^^

Configurarea aplicațiilor de bază
---------------------------------

Configurarea git
^^^^^^^^^^^^^^^^

Configurarea vim
^^^^^^^^^^^^^^^^

Configurarea tmux
^^^^^^^^^^^^^^^^^
