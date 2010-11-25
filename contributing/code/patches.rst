Trimiterea unui patch
=====================

Patch-urile sunt cea mai bună modalitate de a oferi reparații pentru bug-uri
sau de a propune îmbunătățiri pentru Symfony2.

Setup inițial
-------------

Înainte de a lucra la Symfony2, puneți la punct un mediu prietenos, cu
următoarele software-uri:

* Git;

* PHP versiunea 5.3.2 sau mai nouă;

* PHPUnit 3.5.0 sau mai nouă.

Configurați informațiile dvs. de utiliaztor cu numele real și o adresă de email
funcțională:

.. code-block:: bash

    $ git config --global user.name "Your Name"
    $ git config --global user.email you@example.com

.. tip::

    Dacă sunteți novice în materie de Git, vă recomandăm călduros să citiți
    excelenta și gratuita carte `ProGit`_.

Obținerea codului sursă a Symfony2:

* Creați un cont pe `Github`_ și autentificati-vă;

* Bifurcați `depozitul Symfony2`_ (dați click pe butonul "Fork");

* După ce "acțiunea de bifurcare hardcore" s-a încheiat, clonați bifurcația
  local (aceasta va creea un folder `symfony`):

.. code-block:: bash

      $ git clone git@github.com:USERNAME/symfony.git

* Adăugați depozitul de upstream ca ``remote``:

.. code-block:: bash

      $ cd symfony
      $ git remote add upstream git://github.com/fabpot/symfony.git

Acum că Symfony2 este instalat, verificați dacă toate unitățile de test trec
pentru mediul dvs., așa cum este explicat în :doc:`document-ul <tests>` dedicat.

Lucrul la un patch
------------------

De fiecare dată când doriți să lucrați la un patch pentu un bug sau o
îmbunătățire, creați o ramură destinată subiectului:

.. code-block:: bash

    $ git checkout -b BRANCH_NAME

.. tip::

    Folosiți un nume descriptiv pentru ramura dumneavoastră (`ticket_XXX` unde
    `XXX` este numărul tichetului este o convenție bună penru reparațiile
    bug-urilor).

Comanda de mai sus comută automat codul pe ramura abia creată (verificați ramura
pe care lucrați cu `git branch`).

Lucrați la cod și trimiteți commit-uri cât de mult doriți; numai să aveți în
vedere următoarele:

* Urmați :doc:`standardele <standards>` de codare (folosiți `git diff --check`
  pentru a verifica spațiile rămase la sfârșitul liniilor);

* Adăugați unități de test pentru a dovedi că bug-ul a fost reparat sau că noua
  facilitate functioneză;

* Realizați commit-uri separate logic (folosiți puterea lui `git rebase` pentru
  a avea un istoric curat și logic);

* Scrieți mesaje de calitate pentru commit-uri.

.. tip::

    Un mesaj de calitate pentru un commit este compus dintr-un sumar (prima
    linie), opțional urmat de o linie goală și o descriere mai detaliată.
    Sumarul trebuie să înceapă cu numele componentei la care lucrați încadrat de
    paranteze pătrate (`[DependencyInjection]`, `[FrameworkBundle]`, ...).
    Folosiți un verb (`fixed ...`, `added ...`, ...) pentru a începe sumarul și
    nu adăugați punct la sfârșit.

Trimitere unui patch
--------------------

Înainte de a trimite un patch, actulizati ramura (necesar dacă va luat mult timp
să finalizați modificările):

.. code-block:: bash

    $ git checkout master
    $ git fetch upstream
    $ git merge upstream/master
    $ git checkout BRANCH_NAME
    $ git rebase master

Când efectuați comanda `rebase`, este posibil să fie necesar să reparați
conflictele de alipire. `git st` vă indică fișierele care nu au fost *alipite*.
Rezolvați toate conflictele, și pe urmă continuați rebase-ul:

.. code-block:: bash

    $ git add ... # add resolved files
    $ git rebase --continue

Verificați dacă toate testele trec și împingeți ramura mai departe:

.. code-block:: bash

    $ git push origin BRANCH_NAME

Acum puteți să vă faceți cunoscut patch-ul pe `dev mailing-list`_. Email-ul
trebuie să urmeze următoarele convenții:

* Subiectul trebuie să înceapă cu `[PATCH]`, urmat de un scurt sumar al
  patch-ului (cu o referință la tichet dacă este o reparație de bug  - `#XXX`);

* Corpul trebuie să conțină informații despre ramura dumneavoastră
  (`git://github.com/USERNAME/symfony.git BRANCH_NAME`);

* Corpul trebuie să descrie ceea ce realizează patch-ul (o referire la tichet
  sau o copie a mesajului din commit).

În urma feedback-ului primit, este posibil să fie necesar să refaceți patch-ul.
Înainte de a retrimite patch-ul, efectuați un rebase cu ramura master, nu
alipire; și forțați împingerea către origine:

.. code-block:: bash

    $ git push -f origin BRANCH_NAME

.. _ProGit: http://progit.org/
.. _Github: https://github.com/signup/free
.. _depozitul Symfony2: http://www.github.com/fabpot/symfony
.. _dev mailing-list: http://groups.google.com/group/symfony-devs
