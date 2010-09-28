Vederea
=======

Dupa ce ati citit prima parte a acestui tutorial, ati decis ca Symfony2 merita
inca 10 minute. Foarte bine pentru dumneavoastra. In aceasta a doua parte veti
afla mai multe despre sistemul de sabloane Symfony2. Dupa cum ati observat mai
devreme, Symfony utilizeaza PHP ca motor de sabloanare implicit, adaugand niste
facilitati suplimentare pentru al face mai puternic.

In loc de PHP, puteti de asemenea sa utilizati `Twig`_ (el face ca sabloanele
dumneavoastra sa fie mai concise si mai prietenoase pentru designerii web).
Daca preferati sa utilizati `Twig`, cititi capitolul alternativ
:doc:`Vederea cu Twig <the_view_with_twig>`.

.. index::
  single: Templating; Layout
  single: Layout

Decorarea sabloanelor
---------------------

De cele mai multe ori, sabloanele din cadrul unui proiect impartasesc elemente
comune, precum bine cunoscutul antet si subsol. In Symfony, ne place sa abordam
problema diferit: un sablon poate fi decorat de un altul.

Sablonul ``index.php`` este decorat de catre ``layout.php``, multumita apelarii
metodei ``extend()``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view->extend('HelloBundle::layout.php') ?>

    Hello <?php echo $name ?>!

Notatia ``HelloBundle::layout.php`` va suna familiar, nu-i asa? Este aceeasi
notatie ca aceea de referentiere a unui sablon. Partea ``::`` nu inseamna decat
ca elementul controler este gol, prin urmare fisierul corespunzator este stocat
direct in ``views/``.

Acum, sa aruncam o privire asupra fisierului ``layout.php``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/layout.php -->
    <?php $view->extend('::layout.php') ?>

    <h1>Hello Application</h1>

    <?php $view['slots']->output('_content') ?>

Layout-ul insusi este decorat de un alt layout (``::layout.php``). Symfony
suporta niveluri multiple de decorare: un layout poate sa fie decorat de un
altul. Cand denumirea bundle-ului lipseste din numele sablonului, vederile sunt
cautate in folderul ``app/views/``. Acest folder stocheaza vederile globale
pentru intregul proiect:

.. code-block:: html+php

    <!-- app/views/layout.php -->
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
            <title><?php $view['slots']->output('title', 'Hello Application') ?></title>
        </head>
        <body>
            <?php $view['slots']->output('_content') ?>
        </body>
    </html>

Pentru ambele layout-uri, expresia ``$view['slots']->output('_content')`` este
inlocuita de continutul sablonului copil, respectiv ``index.php`` si
``layout.php`` (mai multe despre sloturi in sectiunea urmatoare).

Dupa cum puteti observa, Symfony ofera metode prin intermediul misteriosului
obiect ``$view``. Intr-un sablon, variabila ``$view`` este mereu disponibila si
referentiaza un obiect special care furnizeaza un set de metode si proprietati
ce alcatuiesc motorul de sablonare.

.. index::
   single: Templating; Slot
   single: Slot

Sloturi
-------

Un slot este un fragment de cod, definit intr-un sablon si refolosibil in orice
layout ce decoreaza sablonul. In sablonul ``index.php``, definiti slotul
``title``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view->extend('HelloBundle::layout.php') ?>

    <?php $view['slots']->set('title', 'Hello World app') ?>

    Hello <?php echo $name ?>!

Layout-ul de baza contine deja codul necesar afisarii titlului in antet:

.. code-block:: html+php

    <!-- app/views/layout.php -->
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title><?php $view['slots']->output('title', 'Hello Application') ?></title>
    </head>

Metoda ``output()`` insereaza continutul unui slot si, optional, retine o
valoare implicita pentru cazul cand slotul nu este definit. ``_content`` nu este
decat un slot special care contine redarea sablonului copil.

Pentru sloturi de dimensiuni mari, exista de asemenea o sintaxa extinsa:

.. code-block:: html+php

    <?php $view['slots']->start('title') ?>
        O cantitate mare de HTML
    <?php $view['slots']->stop() ?>

.. index::
   single: Templating; Include

Includerea altor sabloane
-------------------------

Cea mai buna cale de a impartasi un fragment de cod intre mai multe sabloane
distincte este aceea de a defini un sablon care poate fi inclus in altul.

Creati sablonul ``hello.php``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/hello.php -->
    Hello <?php echo $name ?>!

Si modificati sablonul ``index.php`` pentru al include:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view->extend('HelloBundle::layout.php') ?>

    <?php echo $view->render('HelloBundle:Hello:hello.php', array('name' => $name)) ?>

Metoda ``render()`` evalueaza si intoarce continutul unui alt sablon (este exact
aceeasi metoda ca cea utilizata in controler).

.. index::
   single: Templating; Embedding Pages

Integrarea altor Actiuni
------------------------

Ce trebuie facut daca dorim sa integram rezultatul unei alte actiuni intr-un
sablon? Acest lucru este extrem de util cand se lucreaza cu Ajax, sau cand
sablonul integrat necesita anunite variabile indisponibile in sablonul
principal.

Daca veti crea actiunea ``fancy``, si doriti sa o includeti in sablonul
``index.php``, nu trebuie decat sa utilizati urmatorul cod:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view['actions']->output('HelloBundle:Hello:fancy', array('name' => $name, 'color' => 'green')) ?>

Aici, sirul de caractere ``HelloBundle:Hello:fancy`` se refera la actiunea
``fancy`` a controlerului ``Hello``::

    // src/Application/HelloBundle/Controller/HelloController.php

    class HelloController extends Controller
    {
        public function fancyAction($name, $color)
        {
            // creati un obiect bazat pe variabila $color
            $object = ...;

            return $this->render('HelloBundle:Hello:fancy.php', array('name' => $name, 'object' => $object));
        }

        // ...
    }

Dar unde este definit elementul de array ``$view['actions']``? Asemena lui
``$view['slots']``, este denumit ajutor de sablon sau helper, iar urmatoarea
sectiune va vorbeste mai multe despre acestia.

.. index::
   single: Templating; Helpers

Ajutorii de Sabloane
--------------------

Sistemul de sablonare Symfony poate fi usor extins cu ajutorul helper-ilor.
Ajutorii de sabloane sunt obiecte PHP care furnizeaza facilitati utile in
contextul unui sablon. ``actions`` si ``slots`` sunt doi dintre helperii
inglobati in Symfony.

Legaturile dintre Pagini
~~~~~~~~~~~~~~~~~~~~~~~~

Cand vorbim de aplicatiile web, crearea legaturilor intre diferite pagini este o
necesitate. In loc sa folosim hardcoding-ul URL-urilor in sabloane, helper-ul
``router`` stie cum sa genereze URL-uri bazate pe configuratia rutarii. In
acest mod, toate URL-urile dumneavoastra pot fi actualizate usor modificand
configuratia:

.. code-block:: html+php

    <a href="<?php echo $view['router']->generate('hello', array('name' => 'Thomas')) ?>">
        Greet Thomas!
    </a>

Metoda ``generate()`` preia numele rutei si un array de valori ca argumente.
Numele rutei este cheia principala cu ajutorul careia se identifica ruta, iar
elementele din array contin valorile substituentilor din tiparul rutei:

.. code-block:: yaml

    # src/Application/HelloBundle/Resources/config/routing.yml
    hello: # numele rutei
        pattern:  /hello/:name
        defaults: { _controller: HelloBundle:Hello:index }

Utilizarea activelor: imagini, JavaScript-uri si foi de stil
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ce ar fi Internet-ul fara imagini, JavaScript-uri si foi de stil? Symfony
furnizeaza trei helperi pentru a le face fata cu usurinta: ``assets``,
``javascripts`` si ``stylesheets``:

.. code-block:: html+php

    <link href="<?php echo $view['assets']->getUrl('css/blog.css') ?>" rel="stylesheet" type="text/css" />

    <img src="<?php echo $view['assets']->getUrl('images/logo.png') ?>" />

Scopul principal al helper-ului ``assets`` este sa faca aplicatia mai portabila.
Multumita acestui helper, puteti sa mutati folderul radacina al aplicatiei
oriunde in interiorul radacinii web fara a schimba ceva in codul sabloanelor.

In mod similar, puteti sa gestionati foile de stil si JavaScript-urile prin
intermediul ajutorilor ``stylesheets`` si ``javascripts``:

.. code-block:: html+php

    <?php $view['javascripts']->add('js/product.js') ?>
    <?php $view['stylesheets']->add('css/product.css') ?>

Metoda ``add()`` defineste dependentele. Pentru a afisa aceste active, trebuie
de asemenea sa adaugati urmatorul cod in layout-ul principal:

.. code-block:: html+php

    <?php echo $view['javascripts'] ?>
    <?php echo $view['stylesheets'] ?>

Ganduri de Final
----------------

Sistemul de sablonare Symfony este simplu insa puternic. Multumita
layout-urilor, slot-urilor, sablonarii si includerii actiunilor, este foarte
usor sa va organizati sabloanele intr-o maniera logica si extensibila.

Nu ati lucrat decat de aproape 20 de minute cu Symfony si deja puteti realiza
lucruri uimitoare cu el. Aceasta este puterea Symfony. Invatarea elementelor de
baza este usoara, si in cele ce urmeaza veti vedea ca aceasta simplicitate este
ascunsa sub o arhitectura foarte flexibila.

Dar sa nu ne grabim. Mai intai, trebuie sa aflati mai multe despre controlere,
iar acesta este exact subiectul urmatoarei parti a acestui tutorial. Sunteti
pregatit pentru inca 10 minute alaturi de Symfony?

.. _Twig: http://www.twig-project.org/
