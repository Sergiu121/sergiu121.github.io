Folosirea stației la distanță
=============================

Odată ce am reușit să realizăm o conexiune persistentă între stația noastră cea de toate zilele și workstationul pe care vrem să lucrăm, trebuie să ne configurăm stația pe care vom lucra.

Pentru configurarea stației vom presupune următorul flux de lucru:
* ne conectăm la sistem
* edităm fișiere sursă sau fișiere de configurare
* compilăm un program, sau rulăm o aplicație
* ne deconectăm de la sistem

Folosirea ``tmux``
------------------

Spre deosebire de un laptop, unde dacă închidem ecranul și îl băgăm în ghiozdan, acesta intră în hibernare, stația de lucru poate să lucreze în continuare. Acest lucru ne oferă persistență fluxului de lucru, este necesar mai puțin setup atunci când lucrezi.

Atunci când ne conectăm la un calculator printr-un client SSH și rulăm comenzi, acestea vor rula în foreground.

Dacă avem o aplicație care rulează mult timp, cum ar fi un ``find``, și conexiunea SSH se întrerupe, se va întrerupe și execuția comenzii ``find``.

Pentru a rezolva această problemă și a ne folosi de disponibilitatea oferită de un sistem distanță, vom folosi utilitarul ``tmux``.

Aceasta pornește o sesiune de shell care este independentă de terminalul în care rulează, astfel, putem să ne conectăm și să ne deconectăm de la ea.

Crearea unei sesiuni ``tmux``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fiecare pornire a aplicației ``tmux`` deschide o nouă sesiune.
Putem considera fiecare sesiune ca o fereastră a unui browser.
De regulă nu este nevoie să avem mai multe ferestre de browser, sau de ``tmux`` pornite, deoarece avem alte moduri de organizare a spațiului de lucru cu care putem să lucrăm mai ușor folosind scurtături.
În plus, pentru fiecare sesiune de ``tmux`` ar fi nevoie să pornim un nou client SSH și în funcție de modul de conectare la stație, asta adaugă pași în plus.

<insert gif>

Detașarea și reatașarea la o sesiune ``tmux``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Aplicația ``tmux`` permite detașarea de la o sesiune folosind combinația de
taste ``Ctrl+v d``

<insert gif>

.. admonition:: Observație

        Vom vedea pe parcursul acestei subsecțiuni că toate scurtăturile de taste ale aplicației tmux începu cu combinația de taste ``Ctrl+v``

Pentru a ne reatașa la o sesiune trebuie să ne dăm seama la care sesiune să ne atașăm.
Fiecare sesiune ``tmux`` are asociat un identificator.

Vom afișa toate sesiunile deschise folosind comanda ``tmux ls``:

.. code-block::

    student@uso:~$ tmux ls
    0: 1 windows (created Wed Jan  6 02:44:06 2021)

În momentul de față există o singură sesiune cu identificatorul 0.

Pentru a ne reatașa la sesiune folosim comanda ``tmux attach-session -t 0``.

Taburi în ``tmux``
^^^^^^^^^^^^^^^^^^

Pentru a ne organiza terminalele deschise pe stația de la distanță recomandăm folosirea taburilor, deoarece acestea sunt deschise pe durata sesiunii și permit deschiderea și manipularea facilă.

De exemplu, pentru a lucra la o aplicație, avem nevoie să deschidem într-un tab docul sursă pe care îl manipulăm, într-un tab avem un shell în care compilăm aplicația și într-un tab rulăm comenzi de verificare a aplicației.
Având fiecare tab cu sarcina sa desemnată păstrăm un istoric mai curat și mai ușor de interpretat și reducem numărul de comenzi pe care le rulăm pentru a reporni editorul de text, a schimba directul de lucru etc.

Pentru a porni un nou tab folosim combinația de taste ``Ctrl+v c``.

<insert gif>

.. admonition:: Observație

    În bara de taburi din terminal a apărut un nou tab cu denumirea `` 1:bash*``.

Fiecare tab are propriul identificator și propriul nume.
Atunci când vrem să mutăm tabul activ folosim combinația de taste ``Ctrl+v #``, unde ``#`` este numărul tabului care vrem să devină activ.

<insert gif>

.. admonition:: Observație

    Odată ce se schimbă tabul activ se schimbă și sublinierea tabului activ din bara de taburi.

Exerciții
^^^^^^^^^

#) Creați două sesiuni de ``tmux``.

#) În prima sesiune creată deschideți două taburi. Rulați în primul tab comanda ``htop`` și în al doilea tab deschideți fișierul ``/etc/passwd`` folosind editorul de text ``nano``.

#) În a doua sesiune creată deschideți trei taburi. Rulați în primul tab comanda ``sudo apt-get update``, rulați în al doilea terminal comanda ``iostat -x 2 5`` și în al treilea tab rulați comanda ``tail -f /var/log/syslog``.

Scenarii de folosire a sistemului la distanță
---------------------------------------------

Vom folosi această subsecțiune ca un tutorial pentru a exemplifica un workflow pentru lucrul la distanță
