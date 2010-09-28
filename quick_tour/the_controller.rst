.. index::
   single: Controller
   single: MVC; Controller

Controlerul
===========

Inca ati ramas cu noi dupa primele doua parti? Sunteti pe cale sa deveniti un
dependent de Symfony2! Fara a mai pierde vremea, haideti sa descoperim ce poate
face controlerul pentru dumneavoastra.

.. index::
   single: Formats
   single: Controller; Formats
   single: Routing; Formats
   single: View; Formats

Formatele
---------

In zilele noastre, o aplicatie web trebuie sa fie capabila sa furnizeze mai mult
decat pagini HTML. De la XML-ul pentru feed-urile RSS, pana la JSON-ul pentru
cererile Ajax, exista o multime de formate de unde putem alege. Suportul acestor
formate in Symfony este simplu. Editati ``routing.yml`` si adaugati parametrul
``_format`` cu valoarea ``xml``:

.. configuration-block::

    .. code-block:: yaml

        # src/Application/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/:name
            defaults: { _controller: HelloBundle:Hello:index, _format: xml }

    .. code-block:: xml

        <!-- src/Application/HelloBundle/Resources/config/routing.xml -->
        <route id="hello" pattern="/hello/:name">
            <default key="_controller">HelloBundle:Hello:index</default>
            <default key="_format">xml</default>
        </route>

    .. code-block:: php

        // src/Application/HelloBundle/Resources/config/routing.php
        $collection->addRoute('hello', new Route('/hello/:name', array(
            '_controller' => 'HelloBundle:Hello:index',
            '_format'     => 'xml',
        )));

Apoi, adaugati sablonul ``index.xml.php`` alaturi de ``index.php``:

.. code-block:: xml+php

    # src/Application/HelloBundle/Resources/views/Hello/index.xml.php
    <hello>
        <name><?php echo $name ?></name>
    </hello>

Asta a fost tot. Nu este evoie sa modificati controlerul. Pentru formatele
standard, Symfony va alege in mod automat cel mai bun header ``Content-Type``
pentru raspuns. Daca doriti sa folositi diferite formate pentru o singura
actiune, folositi in schimb substituentul ``:_format`` in cadrul tiparului:

.. configuration-block::

    .. code-block:: yaml

        # src/Application/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:      /hello/:name.:_format
            defaults:     { _controller: HelloBundle:Hello:index, _format: html }
            requirements: { _format: (html|xml|json) }

    .. code-block:: xml

        <!-- src/Application/HelloBundle/Resources/config/routing.xml -->
        <route id="hello" pattern="/hello/:name.:_format">
            <default key="_controller">HelloBundle:Hello:index</default>
            <default key="_format">html</default>
            <requirement key="_format">(html|xml|json)</requirement>
        </route>

    .. code-block:: php

        // src/Application/HelloBundle/Resources/config/routing.php
        $collection->addRoute('hello', new Route('/hello/:name.:_format', array(
            '_controller' => 'HelloBundle:Hello:index',
            '_format'     => 'html',
        ), array(
            '_format' => '(html|xml|json)',
        )));

Controlerul poate fi acum solicitat pentru URL-uri ca ``/hello/Fabien.xml`` sau
``/hello/Fabien.json``. Deoarece valoarea implicita pentru ``_format`` este
``html``, ``/hello/Fabien`` si ``/hello/Fabien.html`` vor avea ambele formatul
``html``.

Parametrul ``requirements`` defineste expresia regulata cu care substituentul
trebuie sa se potriveasca. In exemplul dat, daca incercati sa solicitati resursa
``/hello/Fabien.js``, veti primi eroarea HTTP 404, deoarece URL-ul nu
indeplineste cerinta specificata in ``_format``.

.. index::
   single: Response

Obiectul Response
-----------------

Acum, sa ne intoarcem la controlerul ``Hello``::

    // src/Application/HelloBundle/Controller/HelloController.php

    public function indexAction($name)
    {
        return $this->render('HelloBundle:Hello:index.php', array('name' => $name));
    }

Metoda ``render()`` reda un sablon si intoarce un obiect ``Response``. Raspunsul
poate fi optimizat inainte sa fie trimis la browser, de exemplu sa fie schimbat
``Content-Type``-ul implicit::

    public function indexAction($name)
    {
        $response = $this->render('HelloBundle:Hello:index.php', array('name' => $name));
        $response->headers->set('Content-Type', 'text/plain');

        return $response;
    }

Pentru sabloane simple, puteti chiar sa creati manual un obiect ``Response`` si
sa salvati cateva milisecunde::

    public function indexAction($name)
    {
        return $this->createResponse('Hello '.$name);
    }

Aceasta cale este deosebit de utila cand controlerul trebuie sa intoarca un
raspuns JSON pentru o cerere Ajax

.. index::
   single: Exceptions

Managementul Erorilor
---------------------

Cand lucrurile nu sunt gasite, trebuie sa va "jucati" cu protocolul HTTP si sa
intoarceti un raspuns 404. Acest lucru se poate realiza foarte usor prin
aruncarea unei exceptii::

    use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

    public function indexAction()
    {
        $product = // recuperati obiectul din baza de date
        if (!$product) {
            throw new NotFoundHttpException('Produsul nu exista.');
        }

        return $this->render(...);
    }

``NotFoundHttpException`` va intoarce un raspuns HTTP 404 inapoi la browser. In
mod similar, ``ForbiddenHttpException`` intoarce eroarea 403 si
``UnauthorizedHttpException`` eroarea 401. Pentru orice alt cod de eroare HTTP,
puteti sa utilizati baza ``HttpException`` si sa specificati eroarea HTTP prin
intermediul argumentelor exceptiei::

    throw new HttpException('Unauthorized access.', 401);

.. index::
   single: Controller; Redirect
   single: Controller; Forward

Redirectionarea si Transmiterea
-------------------------------

Daca doriti sa redirectionati utilizatorul catre alta pagina, folositi metoda
``redirect()``::

    $this->redirect($this->generateUrl('hello', array('name' => 'Lucas')));

Metoda ``generateUrl()`` este aceeasi cu metoda ``generate()`` folosita anterior
prin intermediul helper-ului ``router``. Ea preia ca argumente numele rutei si
un array de parametrii si intoare URL-ul asociat.

Puteti de asemenea sa transmiteti actiunea catre alta actiune cu ajutorul
metodei ``forward()``. Asemenea helper-ului ``actions``, ea realizeaza o
sub-cerere interna, dar intoarce obiectul ``Response`` pentru a permite
modificari ulterioare daca nevoia o va impune::

    $response = $this->forward('HelloBundle:Hello:fancy', array('name' => $name, 'color' => 'green'));

    // modificati raspunsul sau il intoarceti direct

.. index::
   single: Request

Obiectul Request
----------------

Pe langa valorile substituentilor de rutare, controlerul are de asemenea acces
la obiectul ``Request``::

    $request = $this['request'];

    $request->isXmlHttpRequest(); // este o cerere Ajax?

    $request->getPreferredLanguage(array('en', 'fr'));

    $request->query->get('page'); // obtine un parametru $_GET

    $request->request->get('page'); // obtine un parametru $_POST

Intr-un sablon, puteti de asemenea sa accesati obiectul ``Request`` prin
intermediul helper-ului ``request``:

.. code-block:: html+php

    <?php echo $view['request']->getParameter('page') ?>

Sesiunea
--------

Chiar daca protocolul HTTP este apatrid, Symfony furnizeaza obiectul sesiune
care reprezinta clientul (fie el o persoana reala ce utilizeaza un browser, fie
un bot sau un serviciu web). Intre doua cereri, Symfony stocheaza atributele
intr-un cookie utilizand sistemul nativ de sesiuni al PHP.

Stocarea si recuperarea informatiei din sesiune poate fi usor realizata din
orice controler::

    // stocheaza un atribut pentru reutilizare in timpul unei cereri ulterioare
    $this['request']->getSession()->set('foo', 'bar');

    // in alt controler pentru o alta cerere
    $foo = $this['request']->getSession()->get('foo');

    // seteaza localizarea utilizatorului
    $this['request']->getSession()->setLocale('fr');

Puteti de asemenea sa stocati mesaje de mici dimensiuni care vor fi disponibile
doar pentru cererea imediat urmatoare::

    // stocheaza un mesaj pentru cererea imediat urmatoare (intr-un controler)
    $this['session']->setFlash('notice', 'Felicitari, actiunea dvs. a reusit!');

    // afiseaza mesajul in urmatoarea cerere (intr-un sablon)
    <?php echo $view['session']->getFlash('notice') ?>

Ganduri de Final
----------------

Asta a fost tot, si nici nu sunt sigur daca am consumat cele 10 minute alocate.
In partea anterioara, am vazut cum sa extindem sistemul de sablonare cu ajutorul
helper-ilor. Numai ca, in Symfony2, orice poate fi extins sau inlocuit cu
bundle-uri. Acesta este si subiectul urmatoarei parti a acestui tutorial.
