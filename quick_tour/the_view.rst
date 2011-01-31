Vederea
=======

După ce ați citit prima parte a acestui tutorial, ați decis că Symfony2 a
meritat încă 10 minute. Foarte bine pentru dumneavoastră. În această a doua
parte veți afla mai multe despre motorul de șablonare Symfony2, `Twig`_. Twig
este un motor de șablonare flexibil, rapid și sigur, pentru PHP. El face
șabloanele mult mai concise și ușor de citit; de asemenea le face mult mai
prietenoase pentru designerii web.

.. note::

    În loc de Twig, puteți de asemenea să utilizați
    :doc:`PHP </guides/templating/PHP>` pentru șabloanele dumneavoastră. Ambele
    motoare de șablonare sunt suportate de Symfony2 și beneficiază de același
    nivel de suport.

.. index::
    single: Twig
    single: Vedere; Twig

Twig, o trecere în revistă
--------------------------

.. tip::

    Dacă doriți să învățați Twig, vă recomandăm călduros să citiți
    `documentația`_ oficială. Această secțiune este doar o scurtă trecere în
    revistă a conceptelor de bază.

Un șablon Twig este un fișier text care poate genera orice format bazat pe text
(HTML, XML, CSV, Latex, ...). Twig definește două tipuri de delimitatori:

* ``{{ ... }}``: Afișează o variabilă sau rezultatul unei expesii;

* ``{% ... %}``: O etichetă care aparține de segmentul logic al unui șablon;
  este utilizată, de exemplu, pentru a executa bucle ``for`` sau structuri
  ``if``.

Mai jos este un șablon minim care ilustrează câteva concepte de bază:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            <title>My Webpage</title>
        </head>
        <body>
            <h1>{{ page_title }}</h1>

            <ul id="navigation">
                {% for item in navigation %}
                    <li><a href="{{ item.href }}">{{ item.caption }}</a></li>
                {% endfor %}
            </ul>
        </body>
    </html>

Variabilele trimise unui șabloan pot fi șiruri de caractere, array-uri sau chiar
obiecte. Twig abstractizează diferența dintre ele și vă permite să accesați
"atributele" unei variabile cu ajutorul notației punct (``.``):

.. code-block:: jinja

    {# array('name' => 'Fabien') #}
    {{ name }}

    {# array('user' => array('name' => 'Fabien')) #}
    {{ user.name }}

    {# force array lookup #}
    {{ user['name'] }}

    {# array('user' => new User('Fabien')) #}
    {{ user.name }}
    {{ user.getName }}

    {# force method name lookup #}
    {{ user.name() }}
    {{ user.getName() }}

    {# pass arguments to a method #}
    {{ user.date('Y-m-d') }}

.. note::

    Este important de știut că acoladele nu sunt parte din variabilă ci din
    declarația de afișare. Dacă accesați variabilele în interiorul etichetelor
    nu puneți acolade în jurul lor.

Decorarea șabloanelor
---------------------

De cele mai multe ori, șabloanele din cadrul unui proiect împărtășesc elemente
comune, precum bine cunoscutul antet și subsol. În Symfony2, ne place să abordăm
problema diferit: un șablon poate fi decorat de un altul. Aceasta funcționează
în mod similar claselor PHP: moștenirea șabloanelor vă permite să construiți
un șablon cu "aspectul" de bază ce conține toate elementele comune ale site-ului
dumneavoastră și definește "blocuri" pe care șabloanele copil le pot suprascrie.

Șablonul ``index.html.twig`` moștenește de la ``layout.html.twig``, mulțumită
etichetei ``extends``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

Notația ``HelloBundle::layout.html.twig`` vă sună familiar, nu-i așa? Este
aceeași notație ca aceea de referire a unui șablon. Partea ``::`` nu înseamnă
decât că elementul controler este gol, prin urmare fișierul corespunzător este
stocat direct în folderul ``views/``.

Acum, să aruncăm o privire asupra fișierului ``layout.html.twig``:

.. code-block:: jinja

    {% extends "::base.html.twig" %}

    {% block body %}
        <h1>Hello Application</h1>

        {% block content %}{% endblock %}
    {% endblock %}

Etichetele ``{% block %}`` definesc două blocuri (``body`` și ``content``) pe
care șabloanele copil le pot umple. Tot ce realizează eticheta block este să îi
comunice motorului de șablonare că un șablon copil poate să suprascrie aceste
porțiuni ale șablonului. Șablonul ``index.html.twig`` suprascrie blocul
``content``. Celălalt bloc este definit într-un aspect de bază deoarece aspectul
este însuși decorat de un altul. Când denumirea bundle-ului lipsește din numele
șablonului (``::base.html.twig``), vederile sunt căutate în folderul
``app/views/``. Acest folder stochează vederile globale pentru întregul proiect:

.. code-block:: jinja

    {# app/views/base.html.twig #}
    <!DOCTYPE html>
    <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
            <title>{% block title %}Hello Application{% endblock %}</title>
        </head>
        <body>
            {% block body '' %}
        </body>
    </html>

Etichete, filtre și funcții
---------------------------

Una dintre cele mai bune caracteristici ale Twig este extensibilitatea prin
intermediul etichetelor, filtrelor și funcțiilor. Symfony2 vine însoțit de
multe dintre acestea, pentru a ușura munca designer-ului web.

Includerea șabloanelor
~~~~~~~~~~~~~~~~~~~~~~

Cea mai bună cale de a partaja un fragment de cod de șablon este aceea de a
defini un șablon care poate fi inclus în alte șabloane.

Creați un șablon ``hello.html.twig``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/hello.html.twig #}
    Hello {{ name }}

Și modificați șablonul ``index.html.twig`` pentru al include:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {# override the body block from index.html.twig #}
    {% block body %}
        {% include "HelloBundle:Hello:hello.html.twig" %}
    {% endblock %}

Integrarea controlerelor
~~~~~~~~~~~~~~~~~~~~~~~~

Ce trebuie făcut dacă dorim să integrăm rezultatul unui alt controler într-un
șablon? Acest lucru este extrem de util când se lucrează cu Ajax, sau când
șablonul integrat necesită anumite variabile indisponibile în șablonul
principal.

Dacă veți crea acțiunea ``fancy``, și doriți să o integrați în șablonul
``index``, folosiți eticheta ``render``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% render "HelloBundle:Hello:fancy" with { 'name': name, 'color': 'green' } %}

Aici, șirul de caractere ``HelloBundle:Hello:fancy`` se referă la acțiunea
``fancy`` a controlerului ``Hello``, iar argumentul conține valorile
variabilelor pentru calea cererii simulate::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    class HelloController extends Controller
    {
        public function fancyAction($name, $color)
        {
            // create some object, based on the $color variable
            $object = ...;

            return $this->render('HelloBundle:Hello:fancy.html.twig', array('name' => $name, 'object' => $object));
        }

        // ...
    }

Crearea legăturilor între pagini
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Când vorbim de aplicații web, crearea legăturilor între pagini este o
necesitate. În loc să folosim hardcoding-ul URL-urilor în șabloane, funcția
``path`` știe cum să genereze URL-uri bazate pe configurarea rutelor. În acest
mod, toate URL-urile pot fi actualizate ușor modificând doar configurarea:

.. code-block:: jinja

    <a href="{{ path('hello', { 'name': 'Thomas' }) }}">Greet Thomas!</a>

Funcția ``path`` preia ca argumente numele rutei și un array de parametrii.
Numele rutei este cheia principală cu ajutorul căreia se identifică ruta, iar
parametrii conțin valorile substituenților definiți în tiparul rutei:

.. code-block:: yaml

    # src/Sensio/HelloBundle/Resources/config/routing.yml
    hello: # The route name
        pattern:  /hello/{name}
        defaults: { _controller: HelloBundle:Hello:index }

.. tip::

    Funția ``url`` generează URL-uri *absolute*: ``{{ url('hello', {
    'name': 'Thomas' }) }}``.

Includerea activelor: imagini, JavaScript-uri și foi de stil
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ce ar fi Internet-ul fără imagini, JavaScript-uri și foi de stil? Symfony2
furnizează funcția ``asset`` pentru a le face față cu ușurință:

.. code-block:: jinja

    <link href="{{ asset('css/blog.css') }}" rel="stylesheet" type="text/css" />

    <img src="{{ asset('images/logo.png') }}" />

Scopul principal al funcției ``asset`` este să facă aplicația mai portabilă.
Mulțumită acestei funcții, puteți să mutați folderul rădăcină al aplicației
oriunde în interiorul rădăcinii web fără a schimba ceva în codul șabloanelor.

Escaping-ul iesirii
-------------------

Twig este configurat în mod implicit să realizeze escaping-ul ieșirii. Citiți
`documentația`_ Twig pentru a afla mai multe despre escaping-ul ieșirii și
despre extensia Escaper.

Concluzii
---------

Twig este simplu dar totuși puternic. Mulțumită aspectelor, blocurilor,
șabloanelor și includerii acțiunilor, este foarte ușor să vă organizați
șabloanele într-o manieră logică și extensibilă.

Nu ați lucrat cu Symfony2 decât de aproape 20 de minute și deja puteți realiza
lucruri uimitoare cu el. Aceasta este puterea Symfony2. Învățarea elementelor de
bază este ușoară, și în cele ce urmează veți vedea că această simplitate este
ascunsă sub o arhitectură foarte flexibilă.

Dar să nu ne grăbim. Mai întâi, trebuie să aflați mai multe despre controlere,
iar acesta este exact subiectul următoarei părți a acestui tutorial. Sunteți
pregătit pentru încă 10 minute alături de Symfony2?

.. _Twig:         http://www.twig-project.org/
.. _documentația: http://www.twig-project.org/documentation
