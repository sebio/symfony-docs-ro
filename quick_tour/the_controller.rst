.. index::
    single: Controler
    single: MVC; Controler

Controlerul
===========

Încă ați rămas cu noi după primele două părți? Sunteți pe cale să deveniți un
dependent de Symfony2! Fără a mai pierde vremea, haideți să descoperim ce poate
face controlerul pentru dumneavoastră.

.. index::
    single: Formate
    single: Controler; Formate
    single: Rutare; Formate
    single: Vedere; Formate

Utilizarea formatelor
---------------------

În zilele noastre, o aplicație web trebuie să fie capabilă să furnizeze mai mult
decât pagini HTML. De la XML-ul pentru feed-urile RSS, până la JSON-ul pentru
cererile Ajax, există o mulțime de formate de unde putem alege. Suportul acestor
formate în Symfony2 este simplu. Editați ``routing.yml`` și adăugați parametrul
``_format`` cu valoarea ``xml``:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/{name}
            defaults: { _controller: HelloBundle:Hello:index, _format: xml }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <route id="hello" pattern="/hello/{name}">
            <default key="_controller">HelloBundle:Hello:index</default>
            <default key="_format">xml</default>
        </route>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        $collection->add('hello', new Route('/hello/{name}', array(
            '_controller' => 'HelloBundle:Hello:index',
            '_format'     => 'xml',
        )));

Apoi, adăugați șablonul ``index.xml.twig` alături de ``index.html.twig``:

.. code-block:: xml+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/index.xml.twig -->
    <hello>
        <name>{{ name }}</name>
    </hello>

În cele din urmă, deoarece șablonul trebuie să fie selectat în funcție de
format, realizați următoarele modificari în controler:

.. code-block:: php

    // src/Sensio/HelloBundle/Controller/HelloController.php
    public function indexAction($name, $_format)
    {
        return $this->render(
            'HelloBundle:Hello:index.'.$_format.'.twig',
            array('name' => $name)
        );
    }

Aceasta a fost tot. Pentru formatele standard, Symfony2 va alege în mod automat
cel mai bun header ``Content-Type`` pentru răspuns. Dacă doriți să folosiți
diferite formate pentru o singură acțiune, folosiți în schimb substituentul
``{_format}`` în cadrul tiparului:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:      /hello/{name}.{_format}
            defaults:     { _controller: HelloBundle:Hello:index, _format: html }
            requirements: { _format: (html|xml|json) }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <route id="hello" pattern="/hello/{name}.{_format}">
            <default key="_controller">HelloBundle:Hello:index</default>
            <default key="_format">html</default>
            <requirement key="_format">(html|xml|json)</requirement>
        </route>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        $collection->add('hello', new Route('/hello/{name}.{_format}', array(
            '_controller' => 'HelloBundle:Hello:index',
            '_format'     => 'html',
        ), array(
            '_format' => '(html|xml|json)',
        )));

Controlerul va fi acum solicitat pentru URL-uri ca ``/hello/Fabien.xml`` sau
``/hello/Fabien.json``.

Parametrul ``requirements`` definește expresia regulată cu care substituentul
trebuie să se potrivească. În exemplul dat, dacă încercați să solicitați resursa
``/hello/Fabien.js``, veți primi eroarea HTTP 404, deoarece URL-ul nu
îndeplinește cerința specificată în ``_format``.

.. index::
    single: Răspuns

Obiectul Response
-----------------

Acum, să ne întoarcem la controlerul ``Hello``::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    public function indexAction($name)
    {
        return $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));
    }

Metoda ``render()`` redă un șablon și întoarce un obiect ``Response``. Răspunsul
poate fi optimizat înainte să fie trimis către browser, de exemplu putem schimba
``Content-Type``-ul implicit::

    public function indexAction($name)
    {
        $response = $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));
        $response->headers->set('Content-Type', 'text/plain');

        return $response;
    }

Pentru șabloane simple, puteți chiar să creați manual un obiect ``Response`` și
să salvați câteva milisecunde::

    public function indexAction($name)
    {
        return new Response('Hello '.$name);
    }

Această metodă este deosebit de utilă atunci când controlerul trebuie să
întoarcă un răspuns JSON pentru o cerere Ajax.

.. index::
    single: Excepții

Gestionarea erorilor
---------------------

Când paginile nu sunt găsite, trebuie să vă "jucați" cu protocolul HTTP și să
întoarceți un răspuns 404. Acest lucru se poate realiza foarte ușor prin
aruncarea unei excepții::

    use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

    public function indexAction()
    {
        $product = // recuperati obiectul din baza de date
        if (!$product) {
            throw new NotFoundHttpException('Produsul nu exista.');
        }

        return $this->render(...);
    }

Excepția ``NotFoundHttpException`` va întoarce un răspuns HTTP 404 către
browser.

.. index::
    single: Controler; Redirecționare
    single: Controler; Înaintare

Redirecționare și înaintare
---------------------------

Dacă doriți să redirecționați utilizatorul către altă pagină, folosiți metoda
``redirect()``::

    return $this->redirect($this->generateUrl('hello', array('name' => 'Lucas')));

Metoda ``generateUrl()`` este asemenea metodei ``generate()`` folosită anterior
prin intermediul helper-ului ``router``. Ea preia ca argumente numele rutei și
un array de parametri, întorcând URL-ul asociat.

Puteți de asemenea să înaintați acțiunea curentă către altă acțiune cu ajutorul
metodei ``forward()``. Asemenea helper-ului ``actions``, ea realizează o
sub-cerere internă, dar întoarce obiectul ``Response`` pentru a permite
modificări ulterioare::

    $response = $this->forward('HelloBundle:Hello:fancy', array('name' => $name, 'color' => 'green'));

    // modificati raspunsul sau il intoarceti direct

.. index::
    single: Cerere

Obiectul Request
----------------

Pe lângă valorile substituenților de rutare, controlerul are de asemenea acces
la obiectul ``Request``::

    $request = $this->get('request');

    $request->isXmlHttpRequest(); // este o cerere Ajax?

    $request->getPreferredLanguage(array('en', 'fr'));

    $request->query->get('page'); // obtine un parametru $_GET

    $request->request->get('page'); // obtine un parametru $_POST

Într-un șablon, puteți să accesați obiectul ``Request`` prin intermediul
variabilei ``app.request``:

.. code-block:: html+php

    {{ app.request.query.get('page') }}

    {{ app.request.parameter('page') }}

Sesiunea
--------

Chiar dacă protocolul HTTP este imparțial, Symfony2 furnizează obiectul sesiune
pentru a reprezenta clientul (fie el o persoană reală ce utilizează un browser,
fie un bot sau un serviciu web). Între două cereri, Symfony2 menține atributele
sesiunii prin intermediul unui cookie, utilizând sistemul nativ de sesiuni al
PHP.

Stocarea și recuperarea informației din sesiune poate fi ușor realizată la
nivelul oricărui controler::

    $session = $this->get('request')->getSession();

    // stocheaza un atribut pentru reutilizare in timpul unei cereri ulterioare
    $session->set('foo', 'bar');

    // in alt controler pentru o alta cerere
    $foo = $session->get('foo');

    // seteaza localizarea utilizatorului
    $session->setLocale('fr');

Puteți de asemenea să stocați mesaje de mici dimensiuni care vor fi disponibile
doar pentru cererea imediat următoare::

    // stocheaza un mesaj pentru cererea imediat urmatoare (intr-un controler)
    $session->setFlash('notice', 'Felicitari, actiunea dvs. a reusit!');

    // afiseaza mesajul in urmatoarea cerere (intr-un sablon)
    {{ app.session.flash('notice') }}

Concluzii
---------

Aceasta a fost tot, și nici nu sunt sigur dacă am consumat cele 10 minute
alocate. În prima parte am prezentat pe scurt bundle-urile; iar toate
caracteristicile despre care am învățat până acum fac parte din bundle-ul
principal al framework-ului. Mulțumită bundle-urilor, totul poate fi extins sau
înlocuit în Symfony2. Acesta este subiectul următoarei părți a acestui tutorial.
