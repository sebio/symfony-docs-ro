Vederea
=======

După ce ați citit prima parte a acestui tutorial, ați decis că Symfony2 merită
încă 10 minute. Foarte bine pentru dumneavoastră. În această a doua parte veți
afla mai multe despre sistemul de șablonare Symfony2. După cum ați observat mai
devreme, Symfony utilizează PHP ca motor de șablonare implicit, adăugând niște
facilități suplimentare pentru al face mai puternic.

În loc de PHP, puteți de asemenea să utilizați `Twig`_ (el face ca șabloanele
dumneavoastră să fie mai concise și mai prietenoase pentru designerii web).
Dacă preferați să utilizați `Twig`, citiți capitolul alternativ
:doc:`Vederea cu Twig <the_view_with_twig>`.

.. index::
    single: Șablonare; Layout
    single: Layout

Decorarea șabloanelor
---------------------

De cele mai multe ori, șabloanele din cadrul unui proiect împărtășesc elemente
comune, precum bine cunoscutul antet și subsol. În Symfony2, ne place să abordăm
problema diferit: un șablon poate fi decorat de un altul.

Șablonul ``index.php`` este decorat de către ``layout.php``, mulțumită apelării
metodei ``extend()``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view->extend('HelloBundle::layout.php') ?>

    Hello <?php echo $name ?>!

Notația ``HelloBundle::layout.php`` vă sună familiar, nu-i așa? Este aceeași
notație ca aceea de referențiere a unui șablon. Partea ``::`` nu înseamnă decât
că elementul controler este gol, prin urmare fișierul corespunzător este stocat
direct în folderul ``views/``.

Acum, să aruncăm o privire asupra fișierului ``layout.php``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/layout.php -->
    <?php $view->extend('::layout.php') ?>

    <h1>Hello Application</h1>

    <?php $view['slots']->output('_content') ?>

Layout-ul însuși este decorat de un alt layout (``::layout.php``). Symfony2
suportă niveluri multiple de decorare: un layout poate să fie decorat de un
altul. Când denumirea bundle-ului lipsește din numele șablonului, vederile sunt
căutate în folderul ``app/views/``. Acest folder stochează vederile globale
pentru întregul proiect:

.. code-block:: html+php

    <!-- app/views/layout.php -->
    <!DOCTYPE html>
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
înlocuită de conținutul șablonului copil, respectiv ``index.php`` și
``layout.php`` (mai multe despre sloturi în secțiunea următoare).

După cum puteți observa, Symfony2 oferă metode prin intermediul misteriosului
obiect ``$view``. Într-un șablon, variabila ``$view`` este mereu disponibilă și
referențiază un obiect special care furnizează un set de metode ce alcătuiesc
motorul de șablonare.

.. index::
    single: Șablonare; Slot
    single: Slot

Lucrul cu sloturi
-----------------

Un slot este un fragment de cod, definit într-un șablon și refolosibil în orice
layout ce decorează șablonul. În șablonul ``index.php``, definiți slotul
``title``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view->extend('HelloBundle::layout.php') ?>

    <?php $view['slots']->set('title', 'Hello World Application') ?>

    Hello <?php echo $name ?>!

Layout-ul de bază conține deja codul necesar afișării titlului în antet:

.. code-block:: html+php

    <!-- app/views/layout.php -->
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title><?php $view['slots']->output('title', 'Hello Application') ?></title>
    </head>

Metoda ``output()`` inserează conținutul unui slot și, opțional, reține o
valoare implicită pentru cazul când slotul nu este definit. ``_content`` nu este
decât un slot special care conține redarea șablonului copil.

Pentru sloturi de dimensiuni mari, există de asemenea o sintaxă extinsă:

.. code-block:: html+php

    <?php $view['slots']->start('title') ?>
        O cantitate mare de HTML
    <?php $view['slots']->stop() ?>

.. index::
    single: Șablonare; Includere

Includerea altor șabloane
-------------------------

Cea mai bună cale de a împărtăși un fragment de cod de șablon este aceea de a
defini un șablon care poate fi inclus în alte șabloane.

Creați șablonul ``hello.php``:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/hello.php -->
    Hello <?php echo $name ?>!

Și modificați șablonul ``index.php`` pentru al include:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php $view->extend('HelloBundle::layout.php') ?>

    <?php echo $view->render('HelloBundle:Hello:hello.php', array('name' => $name)) ?>

Metoda ``render()`` evaluează și întoarce conținutul unui alt șablon (este exact
aceeași metodă ca cea utilizată la nivel de controler).

.. index::
    single: Șablonare; Integrare pagini

Integrarea altor controlere
---------------------------

Ce trebuie făcut dacă dorim să integrăm rezultatul unui alt controler într-un
șablon? Acest lucru este extrem de util când se lucrează cu Ajax, sau când
șablonul integrat necesită anumite variabile indisponibile în șablonul
principal.

Dacă veți crea acțiunea ``fancy``, și doriți să o includeți în șablonul
``index.php``, nu trebuie decât să utilizați următorul cod:

.. code-block:: html+php

    <!-- src/Application/HelloBundle/Resources/views/Hello/index.php -->
    <?php echo $view['actions']->render('HelloBundle:Hello:fancy', array('name' => $name, 'color' => 'green')) ?>

Aici, șirul de caractere ``HelloBundle:Hello:fancy`` se referă la acțiunea
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
``$view['slots']``, este denumit ajutor de șablon sau helper, iar următoarea
secțiune vă vorbește mai multe despre aceștia.

.. index::
    single: Șablonare; Ajutori

Utilizarea ajutorilor de șablon
-------------------------------

Sistemul de șablonare Symfony2 poate fi ușor extins cu ajutorul helper-ilor.
Ajutorii sunt obiecte PHP care furnizează facilități utile în contextul unui
șablon. ``actions`` și ``slots`` sunt doi dintre ajutorii înglobați în Symfony2.

Crearea legăturilor între pagini
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Când vorbim de aplicații web, crearea legăturilor între pagini este o
necesitate. În loc să folosim hardcoding-ul URL-urilor în șabloane, helper-ul
``router`` știe cum să genereze URL-uri bazate pe configurația rutării. În
acest mod, toate URL-urile dumneavoastră pot fi actualizate ușor modificând
configurarea:

.. code-block:: html+php

    <a href="<?php echo $view['router']->generate('hello', array('name' => 'Thomas')) ?>">
        Greet Thomas!
    </a>

Metoda ``generate()`` preia ca argumente, numele rutei și un array de
parametrii. Numele rutei este cheia principală cu ajutorul căreia se identifică
ruta, iar parametrii conțin valorile substituenților definiți în tiparul rutei:

.. code-block:: yaml

    # src/Application/HelloBundle/Resources/config/routing.yml
    hello: # numele rutei
        pattern:  /hello/:name
        defaults: { _controller: HelloBundle:Hello:index }

Utilizarea activelor: imagini, JavaScript-uri și foi de stil
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ce ar fi Internet-ul fără imagini, JavaScript-uri și foi de stil? Symfony2
furnizează trei helperi pentru a le face față cu ușurință: ``assets``,
``javascripts`` și ``stylesheets``:

.. code-block:: html+php

    <link href="<?php echo $view['assets']->getUrl('css/blog.css') ?>" rel="stylesheet" type="text/css" />

    <img src="<?php echo $view['assets']->getUrl('images/logo.png') ?>" />

Scopul principal al helper-ului ``assets`` este să facă aplicația mai portabila.
Mulțumită acestui helper, puteți să mutați folderul rădăcină al aplicației
oriunde în interiorul rădăcinii web fără a schimba ceva în codul șabloanelor.

În mod similar, puteți să gestionați foile de stil și JavaScript-urile prin
intermediul ajutorilor ``stylesheets`` și ``javascripts``:

.. code-block:: html+php

    <?php $view['javascripts']->add('js/product.js') ?>
    <?php $view['stylesheets']->add('css/product.css') ?>

Metoda ``add()`` definește dependențele. Pentru a afișa aceste active, trebuie
de asemenea să adăugați următorul cod în layout-ul principal:

.. code-block:: html+php

    <?php echo $view['javascripts'] ?>
    <?php echo $view['stylesheets'] ?>

Escape-ul iesirii
-----------------

Când utilizați șabloane PHP, întrebuințați mecanismul de escape al variabilelor
de fiecare dată când acestea sunt afișate către utilizator::

    <?php echo $view->escape($var) ?>

În mod implicit, metoda ``escape()`` presupune că variabilele sunt afișate
într-un context HTML. Cel de-al doilea argument vă permite să schimbați
contextul. De exemplu, pentru a afișa ceva în cadrul unui script JavaScript,
utilizați contextul ``js``::

    <?php echo $view->escape($var, 'js') ?>

Concluzii
---------

Sistemul de șablonare Symfony2 este simplu însă puternic. Mulțumită
layout-urilor, slot-urilor, șablonării și includerii acțiunilor, este foarte
ușor să vă organizați șabloanele într-o manieră logică și extensibilă.

Nu ați lucrat cu Symfony2 decât de aproape 20 de minute și deja puteți realiza
lucruri uimitoare cu el. Aceasta este puterea Symfony2. Învățarea elementelor de
bază este ușoară, și în cele ce urmează veți vedea că această simplitate este
ascunsă sub o arhitectură foarte flexibilă.

Dar să nu ne grăbim. Mai întâi, trebuie să aflați mai multe despre controlere,
iar acesta este exact subiectul următoarei părți a acestui tutorial. Sunteți
pregătit pentru încă 10 minute alături de Symfony2?

.. _Twig: http://www.twig-project.org/
