Rularea testelor Symfony2
=========================

Înainte de a trimite un :doc:`patch <patches>` pentru a fi inclus, trebuie să
rulați suita de teste Symfony2 pentru a verifica să nu fi stricat ceva.

PHPUnit
-------

Pentru a rula suita de teste Symfony2, `instalați`_ mai întâi PHPUnit 3.5.0 sau
mai nou:

.. code-block:: bash

    $ pear channel-discover pear.phpunit.de
    $ pear channel-discover components.ez.no
    $ pear channel-discover pear.symfony-project.com
    $ pear install phpunit/PHPUnit

Dependențe (opțional)
---------------------

Pentru a rula întreaga suită de teste, incluzând testele care au dependențe
externe, Symfony2 trebuie să fie capabil să le încarce automat. În mod implicit,
ele sunt încărcate automat din folderul `vendor/` aflat în folderul principal al
rădăcinii (vedeți `autoload.php.dist`).

Suita de teste are nevoie de urmatoatele terțe librării:

* Doctrine
* Doctrine Migrations
* Swiftmailer
* Twig
* Zend Framework

Pentru a le instala pe toate odata, rulați scriptul `install_vendors.sh`:

.. code-block:: bash

    $ sh install_vendors.sh

.. note::

    Rețineți că scriptul ia ceva timp pentru a termina.

După instalare, puteți actualiza oricând vendorii cu ajutorul script-ului
`update_vendors.sh`:

.. code-block:: bash

    $ sh update_vendors.sh

Rularea
-------

Mai întâi, actualizați vendorii (vedeți mai sus).

Apoi, rulați suita de teste din folderul rădăcină Symfony2 cu următoarea
comandă:

.. code-block:: bash

    $ phpunit

Rezultatul ar trebui să afișeze `OK`. Dacă nu, trebuie să vă dați seama ce se
întâmplă și dacă testele nu trec din cauza modificărilor dumneavoastră.

.. tip::

    Rulați suita de teste înainte să aplicați orice modificări pentru a verifica
    dacă funcționează corespunzător pe configurația dumneavoastră.

Codul de acoperire
------------------

Dacă adăugați o caracteristică nouă, trebuie de asemenea să verificați codul de
acoperire utilizând opțiunea `coverage-html`:

.. code-block:: bash

    $ phpunit --coverage-html=cov/

Verificați codul de acoperire deschizând pagina generată `cov/index.html`
într-un browser.

.. tip::

    Codul de acoperire funcționează numai dacă aveți activat XDebug și toate
    dependențele sunt instalate.

.. _instalați: http://www.phpunit.de/manual/current/en/installation.html
