Imaginea de Ansamblu
====================

Doriti sa incercati Symfony2 si nu aveti la dispozitie decat 10 minute? Aceasta
prima parte a acestui tutorial a fost scrisa pentru dumneavoastra. Ea va explica
cum sa incepeti rapid cu Symfony2 aratandu-va structura unui proiect simplu gata
realizat.

Daca ati mai utilizat un framework web inainte, cu Symfony2 va veti simti in
largul dumneavoastra.

.. index::
   pair: Sandbox; Download

Descarcarea si Instalarea
-------------------------

In primul rand, verificati daca aveti instalat PHP 5.3.2 si daca acesta este
configurat corect pentru lucrul cu un server web, precum Apache.

Sunteti gata? Sa incepem prin a descarca Symfony. Pentru a incepe chiar mai
rapid, vom folosi "Symfony sandbox". Acesta este un proiect Symfony unde toate
librariile necesare si cateva controlere simple sunt deja incluse; configurarea
primara este de asemenea realizata. Marele avantaj al sandbox-ului asupra altor
tipuri de instalare este ca puteti incepe sa experimentati cu Symfony imediat.

Descarcati `sandbox`_-ul, si dezarhivati-l in radacina web. Acum ar trebui sa
aveti un folder ``sandbox/``::

    www/ <- radacina web
        sandbox/ <- folderul dezarhivat
            app/
                cache/
                config/
                logs/
            src/
                Application/
                    HelloBundle/
                        Controller/
                        Resources/
                vendor/
                    symfony/
            web/

.. index::
   single: Installation; Check

Verificarea Configuratiei
-------------------------

Pentru a evita unele probleme nedorite, verificati mai intai daca aveti o
configuratie capabila de a rula un proiect Symfony solicitand urmatorul URL:

    http://localhost/sandbox/web/check.php

Cititi cu atentie rezultatul scriptului si remediati orice problema pe care
acesta o gaseste.

Acum, solicitati prima pagina web "adevarata" Symfony:

    http://localhost/sandbox/web/index_dev.php/

Symfony ar trebui sa va felicite pentru efortul dumneavoastra de pana acum!

Prima Aplicatie
---------------

Sandbox-ul vine insotit de o simpla ":term:`aplicatie`" Hello World, aceasta
fiind cea pe care o vom folosi pentru a afla mai multe despre Symfony. Accesati
urmatorul URL pentru a fi intampinati de catre Symfony (inlocuiti Fabien cu
prenumele dumneavoastra):

    http://localhost/sandbox/web/index_dev.php/hello/Fabien

Ce se intampla aici? Sa analizam URL-ul:

.. index:: Front Controller

* ``index_dev.php``: Acesta este un "controler frontal". El reprezinta punctul
  unic de intrare al aplicatiei si raspunde tuturor cererilor utilizatorului;

* ``/hello/Fabien``: Aceasta reprezinta calea "virtuala" catre resursa pe care
  utilizatorul doreste sa o acceseze.

Responsabilitatea dumneavoastra ca programator este aceea de a scrie codul care
leaga cererea utilizatorului (``/hello/Fabien``) de resursa asociata acesteia
(``Hello Fabien!``).

.. index::
   single: Configuration

Configurarea
~~~~~~~~~~~~

Cum realizeaza Symfony rutarea cererii catre codul dumneavoastra? Pur si simplu
citind cateva fisiere de configurare.

Toate fisiere de configurare Symfony2 pot fi scrise fie in PHP, XML sau `YAML`_
(YAML este un format simplu care face descrierea setarilor de configurare foarte
usoara).

.. tip::

   Sandbox-ul utilizeaza in mod implicit YAML, dar dumneavoastra puteti comuta
   foarte usor catre XML sau PHP editand fisierul ``config/AppKernel.php``.
   Puteti comuta acum urmarind instructiunile aflate in partea de jos a
   fisierului ``config/AppKernel.php`` (tutorialele prezinta configurarea in
   toate formatele suportate).

.. index::
   single: Routing
   pair: Configuration; Routing

Rutarea
~~~~~~~

Symfony ruteaza cererea citind fisierul de configurare al rutelor:

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        homepage:
            pattern:  /
            defaults: { _controller: FrameworkBundle:Default:index }

        hello:
            resource: HelloBundle/Resources/config/routing.yml

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="homepage" pattern="/">
                <default key="_controller">FrameworkBundle:Default:index</default>
            </route>

            <import resource="HelloBundle/Resources/config/routing.xml" />
        </routes>

    .. code-block:: php

        // app/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->addRoute('homepage', new Route('/', array(
            '_controller' => 'FrameworkBundle:Default:index',
        )));
        $collection->addCollection($loader->import("HelloBundle/Resources/config/routing.php"));

        return $collection;

Primele linii ale fisierului de configurare al rutelor definesc ce cod trebuie
chemat atunci cand utilizatorul solicita resursa "``/``". Mult mai interesanta
este ultima parte, care importa un alt fisier de configurare cu urmatorul
continut:

.. configuration-block::

    .. code-block:: yaml

        # src/Application/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/:name
            defaults: { _controller: HelloBundle:Hello:index }

    .. code-block:: xml

        <!-- src/Application/HelloBundle/Resources/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="hello" pattern="/hello/:name">
                <default key="_controller">HelloBundle:Hello:index</default>
            </route>
        </routes>

    .. code-block:: php

        // src/Application/HelloBundle/Resources/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->addRoute('hello', new Route('/hello/:name', array(
            '_controller' => 'HelloBundle:Hello:index',
        )));

        return $collection;

Iata! Dupa cum puteti observa, tiparul resursei "``/hello/:name``" (un sir
de caractere care incepe cu doua puncte, asemena lui ``:name``, reprezinta un
substituent) este atribuit unui controler, referit de valoarea parametrului
``_controller``.

.. index::
   single: Controller
   single: MVC; Controller

Controlere
~~~~~~~~~~

Controlerul este responsabil sa intoarca o reprezentare a resursei (de obicei
HTML) si este definit sub forma de clasa PHP:

.. code-block:: php
   :linenos:

    // src/Application/HelloBundle/Controller/HelloController.php

    namespace Application\HelloBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        public function indexAction($name)
        {
            return $this->render('HelloBundle:Hello:index.php', array('name' => $name));
        }
    }

Codul este destul de intuitiv, dar sa explicam acest cod linie cu linie:

* *linia 3*: Symfony profita de avantajul noilor facilitati PHP 5.3 si, ca
  atare, toate controlerele sunt corect incadrate intr-un namespace
  (namespace-ul este identic cu prima parte a valorii parametrului de rutare
  ``_controller``: ``HelloBundle``).

* *linia 7*: Numele controlerului este reprezentat de concatenarea celei de-a
  doua parti a valorii parametrului de rutare ``_controller`` (``Hello``) si
  ``Controller``. El extinde clasa preexistenta ``Controller``, care ofera
  scurtaturi utile (dupa cum vom vedea mai tarziu in acest tutorial).

* *linia 9*: Fiecare controler este compus din mai multe actiuni. Asa cum este
  specificat in configurare, pagina hello este manipulata de actiunea ``index``
  (a treia parte a parametrului de rutare ``_controller``). Aceasta metoda
  primeste valorile substituentilor ca argumente (in cazul nostru ``$name``).

* *linia 11*: Metoda ``render()`` incarca si reda un sablon
  (``HelloBundle:Hello:index``) cu variabilele trecute prin intermediul celui
  de-al doilea argument.

Dar ce este un :term:`bundle`? Intregul cod pe care il scrieti in cadrul unui
proiect Symfony este organizat in bundle-uri. In vorbirea Symfony, un bundle
reprezinta un set structurat de fisiere (fisiere PHP, stylesheets-uri,
JavaScript-uri, imagini, ...) care poate fi usor impartasit cu alti
dezvoltatori. In exemplul nostru nu avem decat un bundle, ``HelloBundle``.

Sabloane
~~~~~~~~

Controlerul reda sablonul ``HelloBundle:Hello:index.php``. Dar in ce consta
numele sablonului? ``HelloBundle`` reprezinta numele bundle-ului, ``Hello`` este
numele controlerului, iar ``index.php`` numele fisierului sablonului. Sablonul
insusi este alcatuit din cod HTML si expresii simple PHP:

.. code-block:: html+php

    # src/Application/HelloBundle/Resources/views/Hello/index.php
    <?php $view->extend('HelloBundle::layout.php') ?>

    Hello <?php echo $name ?>!

Felicitari! Tocmai ati privit primele secvente de cod ale Symfony. Nu a fost
chiar atat de greu, nu-i asa? Symfony face cu adevarat usoara implementarea
site-urilor web, mult mai bine si mai rapid.

.. index::
   single: Environment
   single: Configuration; Environment

Medii
-----

Acum ca aveti o mai buna intelegere despre modul in care functioneaza Symfony,
puteti arunca o privire in josul paginii; veti remarca o mica bara ce contine
emblemele Symfony si PHP. Aceasta este denumita "Web Debug Toolbar" si este
cel mai bun prieten al dezvoltatorului. Bine inteles, o astfel de unealta nu
trebuie afisata cand lansati aplicatia pe serverele de productie. Din acest
motiv veti gasi un alt controler frontal (``index.php``) in folderul ``web/``,
optimizat pentru mediul de productie:

    http://localhost/sandbox/web/index.php/hello/Fabien

Daca aveti instalat ``mod_rewrite``, puteti sa omiteti partea ``index.php`` a
URL-ului:

    http://localhost/sandbox/web/hello/Fabien

Nu in cele din urma, pe serverele de productie, trebuie sa setati radacina web
pe folderul ``web/``, pentru a securiza aplicatia si pentru a avea un URL mai
aspectuos:

    http://localhost/hello/Fabien

Pentru a face mediul de productie cat se poate de rapid, Symfony mentine un
cache in folderul ``app/cache/``. Cand efectuati modificari, trebuie sa
eliminati manual fisierele din cache. Din acest motiv trebuie sa folositi
intotdeauna controlerul frontal de dezvoltare (``index_dev.php``) atunci cand
lucrati la un proiect.

Ganduri de Final
----------------

Cele 10 minute s-au terminat. De acum, ar trebui sa fiti capabil sa creati
propriile dumneavoastra rute, controlere si sabloane. Ca un exercitiu, incercati
sa creati ceva mult mai util decat o aplicatie de tipul Hello World! Daca
sunteti dornic sa invatati mai multe despre Symfony, puteti citi urmatoarea
parte a acestui tutorial chiar acum, unde vom afla mai multe despre sistemul
de sabloane.

.. _sandbox: http://symfony-reloaded.org/code#sandbox
.. _YAML:    http://www.yaml.org/
