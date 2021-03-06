﻿Imaginea de ansamblu
====================

Doriți să încercați Symfony2 și nu aveți la dispoziție decât 10 minute? Această
primă parte din cadrul acestui tutorial a fost scrisă special pentru
dumneavoastră. Ea vă explică cum să începeți rapid cu Symfony2 prezentându-vă
structura unui proiect simplu, gata realizat.

Dacă ați mai utilizat un framework web înainte, cu Symfony2 vă veți simți în
largul dumneavoastră.

.. index::
    pair: Sandbox; Descărcare

Descărcarea și instalarea Symfony2
----------------------------------

În primul rând, verificați dacă aveți instalat PHP 5.3.2 (sau o versiune mai
recentă) și dacă acesta este configurat corect pentru lucrul cu un server web,
precum Apache.

Sunteți gata? Să începem prin a descărca Symfony2. Pentru a începe chiar mai
repede, vom folosi "Symfony2 Sandbox". Acesta este un proiect Symfony2 unde
toate bibliotecile necesare și câteva controlere simple sunt deja incluse;
configurarea de bază este de asemenea realizată. Marele avantaj al sandbox-ului
în comparație cu alte tipuri de instalare este că puteți începe să experimentați
Symfony2 imediat.

Descărcați `sandbox`_-ul și dezarhivați-l în rădăcina web. Acum ar trebui să
aveți un folder ``sandbox/``::

    www/ <- radacina web
        sandbox/ <- folderul dezarhivat
            app/
                cache/
                config/
                logs/
            src/
                Sensio/
                    HelloBundle/
                        Controller/
                        Resources/
            vendor/
                symfony/
                doctrine/
                ...
            web/

.. index::
    single: Instalare; Verificare

Verificarea configurației
-------------------------

Pentru a evita unele probleme nedorite, verificați mai întâi dacă aveți o
configurație capabilă să ruleze un proiect Symfony2 solicitând următorul URL:

    http://localhost/sandbox/web/check.php

Citiți cu atenție rezultatul script-ului și remediati orice problemă pe care
acesta o găsește.

Acum, solicitați prima pagină web "adevărată" Symfony2:

    http://localhost/sandbox/web/app_dev.php/

Symfony2 ar trebui să vă felicite pentru efortul dumneavoastră de până acum!

Crearea primei aplicații
------------------------

Sandbox-ul vine însoțit de o simplă ":term:`aplicație`" Hello World, aceasta
fiind cea pe care o vom folosi pentru a afla mai multe despre Symfony2. Accesați
următorul URL pentru a fi întâmpinați de către Symfony2 (înlocuiți Fabien cu
prenumele dumneavoastră):

    http://localhost/sandbox/web/app_dev.php/hello/Fabien

Ce se întâmplă aici? Să analizăm URL-ul:

.. index:: Controler frontal

* ``app_dev.php``: Acesta este un "controler frontal". El reprezintă punctul
  unic de intrare al aplicației și răspunde tuturor cererilor utilizatorului;

* ``/hello/Fabien``: Aceasta reprezintă calea "virtuală" către resursa pe care
  utilizatorul dorește să o acceseze.

Responsabilitatea dumneavoastră ca programator este aceea de a scrie codul care
leagă cererea utilizatorului (``/hello/Fabien``) de resursa asociată acesteia
(``Hello Fabien!``).

.. index::
    single: Configurare

Configurare
~~~~~~~~~~~

Cum realizează Symfony2 rutarea cererii către codul dumneavoastră? Pur și simplu
citind câteva fișiere de configurare.

Toate fișiere de configurare Symfony2 pot fi scrise fie în PHP, XML sau `YAML`_
(YAML este un format simplu care ușoară face descrierea setărilor de
configurare).

.. tip::

    Sandbox-ul utilizează în mod implicit YAML, dar dumneavoastră puteți comuta
    foarte ușor către XML sau PHP editând fișierul ``app/AppKernel.php``. Puteți
    comuta acum, urmărind instrucțiunile aflate în partea de jos a acestui
    fisier (tutorialele prezintă configurarea în toate formatele suportate).

.. index::
    single: Rutare
    pair: Configurare; Rutare

Rutare
~~~~~~

Symfony2 rutează cererea citind fișierul de configurare al rutelor:

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        homepage:
            pattern:  /
            defaults: { _controller: FrameworkBundle:Default:index }

        hello:
            resource: "@HelloBundle/Resources/config/routing.yml"

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="homepage" pattern="/">
                <default key="_controller">FrameworkBundle:Default:index</default>
            </route>

            <import resource="@HelloBundle/Resources/config/routing.xml" />
        </routes>

    .. code-block:: php

        // app/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('homepage', new Route('/', array(
            '_controller' => 'FrameworkBundle:Default:index',
        )));
        $collection->addCollection($loader->import("@HelloBundle/Resources/config/routing.php"));

        return $collection;

Primele linii ale fișierului de configurare al rutelor definesc codul executat
atunci când utilizatorul solicită resursa "``/``". Mult mai interesantă este
ultima parte, cea care importă un alt fișier de configurare cu următorul
conținut:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/{name}
            defaults: { _controller: HelloBundle:Hello:index }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="hello" pattern="/hello/{name}">
                <default key="_controller">HelloBundle:Hello:index</default>
            </route>
        </routes>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('hello', new Route('/hello/{name}', array(
            '_controller' => 'HelloBundle:Hello:index',
        )));

        return $collection;

Iată! După cum puteți observa, tiparul resursei "``/hello/{name}``" (un șir
de caractere încadrat de acolade, asemena lui ``{name}``, reprezintă un
substituent) este atribuit unui controler, menționat de valoarea parametrului
``_controller``.

.. index::
    single: Controler
    single: MVC; Controler

Controlere
~~~~~~~~~~

Controlerul este responsabil să întoarcă o reprezentare a resursei (de obicei
HTML) și este definit sub forma unei clase PHP:

.. code-block:: php
    :linenos:

    // src/Sensio/HelloBundle/Controller/HelloController.php

    namespace Sensio\HelloBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        public function indexAction($name)
        {
            return $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));

            // render a PHP template instead
            // return $this->render('HelloBundle:Hello:index.html.php', array('name' => $name));
        }
    }

Codul este destul de intuitiv, totuși să-l explicăm linie cu linie:

* *linia 3*: Symfony2 profită de avantajul noilor facilități PHP 5.3 și, ca
  atare, toate controlerele sunt corect încadrate într-un namespace
  (namespace-ul este identic cu prima parte a valorii parametrului de rutare
  ``_controller``, în cazul nostru ``HelloBundle``).

* *linia 7*: Numele controlerului este reprezentat de concatenarea celei de-a
  doua părți a valorii parametrului de rutare ``_controller`` (``Hello``) cu
  șirul ``Controller``. El extinde clasa preexistentă ``Controller``, care oferă
  scurtături utile (după cum vom vedea mai târziu în acest tutorial).

* *linia 9*: Fiecare controler este compus din mai multe acțiuni. Așa cum este
  specificat în configurare, pagina de întâmpinare este manipulată de acțiunea
  ``index`` (a treia parte a parametrului de rutare ``_controller``). Această
  metodă primește, ca argumente, valorile substituenților (în cazul nostru
  ``$name``).

* *linia 11*: Metoda ``render()`` încarcă și redă un șablon
  (``HelloBundle:Hello:index.html.twig``) cu variabilele trimise prin
  intermediul celui de-al doilea argument.

Dar ce este un :term:`bundle`? Întregul cod pe care îl scrieți în cadrul unui
proiect Symfony2 este organizat în bundle-uri. În vorbirea Symfony2, un bundle
reprezintă un set structurat de fișiere (PHP, foi de stil, JavaScript-uri,
imagini etc.) care poate fi ușor împărtășit cu alți programatori. În exemplul
nostru nu avem decât un singur bundle, ``HelloBundle``.

Șabloane
~~~~~~~~

Controlerul redă șablonul ``HelloBundle:Hello:index.html.twig``. Dar în ce
constă numele șablonului? ``HelloBundle`` reprezintă numele bundle-ului,
``Hello`` este numele controlerului, iar ``index.html.twig`` numele șablonului.
În mod implicit, sandbox-ul utilizeză Twig ca motor de șablonare:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

Felicitări! Tocmai ați urmărit primele secvențe de cod în Symfony2. Nu a fost
chiar atât de greu, nu-i așa? Symfony2 face cu adevărat ușoară implementarea
site-urilor web, mult mai bine și mai rapid.

.. index::
    single: Mediu
    single: Configurare; Mediu

Lucrul cu medii
---------------

Acum că aveți o mai bună înțelegere despre modul în care funcționează Symfony2,
puteți arunca o privire în josul paginii; veți remarca o mică bară ce conține
emblemele Symfony2 și PHP. Aceasta este denumită "Web Debug Toolbar" și este cel
mai bun prieten al programatorului. Bine înțeles, o astfel de unealtă nu trebuie
afișată când lansați aplicația pe serverele de producție. Din acest motiv veți
găsi un alt controler frontal (``app.php``) în folderul ``web/``, optimizat
pentru mediul de producție:

    http://localhost/sandbox/web/app.php/hello/Fabien

Dacă utilizați Apache cu ``mod_rewrite`` activat, puteți să omiteți partea
``app.php`` a URL-ului:

    http://localhost/sandbox/web/hello/Fabien

Nu în cele din urmă, pe serverele de producție, trebuie să stabiliți rădăcina
web pe folderul ``web/``, pentru a securiza aplicația și pentru a avea un URL
mai aspectuos:

    http://localhost/hello/Fabien

Pentru a face mediul de producție cât se poate de rapid, Symfony2 menține un
cache în folderul ``app/cache/``. Când efectuați modificări asupra codului sau
configurării, trebuie să eliminați manual fișierele din cache. Din acest motiv
trebuie să folosiți întotdeauna controlerul frontal de dezvoltare
(``app_dev.php``) atunci când lucrați la un proiect.

Concluzii
---------

Cele 10 minute s-au terminat. De acum, ar trebui să fiți capabil să creați
propriile dumneavoastră rute, controlere și șabloane. Ca un exercițiu, încercați
să creați ceva mult mai util decât o aplicație de tipul *Hello World*! Dacă
sunteți dornic să învățați mai multe despre Symfony2, puteți citi următoarea
parte a acestui tutorial chiar acum, unde vom afla mai multe despre sistemul
de șablonare.

.. _sandbox: http://symfony-reloaded.org/code#sandbox
.. _YAML:    http://www.yaml.org/
