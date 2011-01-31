Arhitectura
===========

Sunteți un erou! Cine ar fi crezut că veți ajunge aici după primele trei părți?
Eforturile dumneavoastră vor fi răsplătite în curând, așa cum se cuvine. Primele
trei părți nu prezintă o vedere detaliată asupra arhitecturii framework-ului.
Deoarece ea face ca Symfony2 să se remarce printre celelalte framework-uri,
haideți să aflăm mai multe acum.

.. index::
    single: Structură foldere

Structura de foldere
--------------------

Structura de foldere a unei :term:`aplicații <aplicație>` Symfony2 este destul
de flexibilă, însă structura de foldere a sandbox-ului reflectă forma tipică și
recomandată pentru o aplicație Symfony2:

* ``app/``: Configurarea aplicației;
* ``src/``: Codul PHP al proiectului;
* ``vendor/``: Dependențele externe;
* ``web/``: Folderul rădăcină web.

Folderul rădăcină web
~~~~~~~~~~~~~~~~~~~~~

Folderul rădăcină web este gazda tuturor fișierelor publice și statice precum
imaginile, foile de stil și fișierele JavaScript. Este de asemenea locul unde
se găsește fiecare :term:`controler frontal`::

    // web/app.php
    require_once __DIR__.'/../app/bootstrap.php';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('prod', false);
    $kernel->handle(Request::createFromGlobals())->send();

Kernel-ul solicită mai întai fișierul ``bootstrap.php``, care pornește procesul
de bootstrap al framework-ului și inregistrează autoloader-ul (vezi mai jos).

Asemenea oricărui controler frontal, ``app.php`` utilizează o clasă Kernel,
``AppKernel``, pentru procesul de bootstrap al aplicației.

.. index::
    single: Kernel

Folderul aplicație
~~~~~~~~~~~~~~~~~~

Clasa ``AppKernel`` reprezintă principalul punct de intrare pentru configurarea
aplicației și, ca atare, este salvată în folderul ``app/``.

Această clasă trebuie să implementeze trei metode:

* ``registerRootDir()``: Întoarce folderul rădăcină al configurării;

* ``registerBundles()``: Întoarce un array cu toate bundle-urile necesare
  pentru rularea aplicației (observați referirea la
  ``Sensio\HelloBundle\HelloBundle``);

* ``registerContainerConfiguration()``: Încarcă configurarea (mai multe despre
  aceasta veți afla mai târziu);

Aruncați o privire peste implementarea implicită a acestor metode pentru a
înțelege mai bine flexibilitatea framework-ului.

Autoîncărcarea PHP poate fi configurată prin intermediul fișierului
``autoload.php``::

    // src/autoload.php
    use Symfony\Component\ClassLoader\UniversalClassLoader;

    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'                        => __DIR__.'/../vendor/symfony/src',
        'Sensio'                         => __DIR__.'/../src',
        'Doctrine\\Common\\DataFixtures' => __DIR__.'/../vendor/doctrine-data-fixtures/lib',
        'Doctrine\\Common'               => __DIR__.'/../vendor/doctrine-common/lib',
        'Doctrine\\DBAL\\Migrations'     => __DIR__.'/../vendor/doctrine-migrations/lib',
        'Doctrine\\MongoDB'              => __DIR__.'/../vendor/doctrine-mongodb/lib',
        'Doctrine\\ODM\\MongoDB'         => __DIR__.'/../vendor/doctrine-mongodb-odm/lib',
        'Doctrine\\DBAL'                 => __DIR__.'/../vendor/doctrine-dbal/lib',
        'Doctrine'                       => __DIR__.'/../vendor/doctrine/lib',
        'Zend'                           => __DIR__.'/../vendor/zend/library',
    ));
    $loader->registerPrefixes(array(
        'Twig_Extensions_' => __DIR__.'/../vendor/twig-extensions/lib',
        'Twig_'            => __DIR__.'/../vendor/twig/lib',
        'Swift_'           => __DIR__.'/../vendor/swiftmailer/lib/classes',
    ));
    $loader->register();

Clasa ``UniversalClassLoader`` din Symfony2 este utilizată pentru încărcarea
automată a fișierelor ce respectă, fie `standardele`_ tehnice de
interoperabilitate pentru namespace-urile PHP 5.3, fie `convenția`_ PEAR pentru
denumirea claselor. După cum puteți observa, toate dependențele sunt depozitate
în folderul ``vendor/``, aceasta nefiind decât o altă convenție. Puteți să le
depozitați oriunde doriți, global pe server sau local în cadrul proiectelor.

.. index::
    single: Bundle-uri

Sistemul de bundle-uri
----------------------

Această secțiune prezintă una dintre cele mai importante și mai puternice
caracteristici ale Symfony2, sistemul de :term:`bundle-uri <bundle>`.

Un bundle este asemenea unui plugin întâlnit în alte programe. Dar de ce este
denumit *bundle* și nu *plugin*? Pentru că, în Symfony2, totul este un bundle,
de la caracteristicile de bază ale framework-ului până la codul pe care îl
scrieți pentru aplicația dumneavoastră. Bundle-urile sunt cetățeni de primă
clasă în Symfony2. Aceasta vă oferă flexibilitatea de a folosi facilități
livrate de terți prin intermediul bundle-urilor pre-construite sau, de a
distribui propriile bundle-uri. Este foarte ușor să alegeți ce facilități
doriți să folosiți în cadrul aplicației și să le optimizați după bunul plac.

O aplicație este constituită din bundle-uri așa cum este definit în metoda
``registerBundles()`` a clasei ``AppKernel``::

    // app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            new Symfony\Bundle\TwigBundle\TwigBundle(),

            // enable third-party bundles
            new Symfony\Bundle\ZendBundle\ZendBundle(),
            new Symfony\Bundle\SwiftmailerBundle\SwiftmailerBundle(),
            new Symfony\Bundle\DoctrineBundle\DoctrineBundle(),
            //new Symfony\Bundle\DoctrineMigrationsBundle\DoctrineMigrationsBundle(),
            //new Symfony\Bundle\DoctrineMongoDBBundle\DoctrineMongoDBBundle(),

            // register your bundles
            new Sensio\HelloBundle\HelloBundle(),
        );

        if ($this->isDebug()) {
            $bundles[] = new Symfony\Bundle\WebProfilerBundle\WebProfilerBundle();
        }

        return $bundles;
    }

Pe lângă ``HelloBundle`` de care am amintit mai devreme, observați că în cadrul
kernel-ului sunt activate de asemena ``FrameworkBundle``, ``DoctrineBundle``,
``SwiftmailerBundle`` și ``ZendBundle``. Toate fac parte din baza
framework-ului.

Fiecare bundle poate fi personalizat prin intermediul fișierelor de configurare
scrise în YAML, XML sau PHP. Să aruncăm o privire la configurarea implicită:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        app.config:
            charset:       UTF-8
            error_handler: null
            csrf_protection:
                enabled: true
                secret: xxxxxxxxxx
            router:        { resource: "%kernel.root_dir%/config/routing.yml" }
            validation:    { enabled: true, annotations: true }
            templating:
                #assets_version: SomeVersionScheme
            session:
                default_locale: en
                lifetime: 3600

        ## Twig Configuration
        #twig.config:
        #    auto_reload: true

        ## Doctrine Configuration
        #doctrine.dbal:
        #    dbname:   xxxxxxxx
        #    user:     xxxxxxxx
        #    password: ~
        #doctrine.orm: ~

        ## Swiftmailer Configuration
        #swiftmailer.config:
        #    transport:  smtp
        #    encryption: ssl
        #    auth_mode:  login
        #    host:       smtp.gmail.com
        #    username:   xxxxxxxx
        #    password:   xxxxxxxx

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <app:config charset="UTF-8" error-handler="null">
            <app:router resource="%kernel.root_dir%/config/routing.xml" />
            <app:validation enabled="true" annotations="true" />
            <app:session default-locale="en" lifetime="3600" />
            <app:csrf-protection enabled="true" secret="xxxxxxxxxx" />
        </app:config>

        <!-- Twig Configuration -->
        <!--
        <twig:config auto_reload="true" />
        -->

        <!-- Doctrine Configuration -->
        <!--
        <doctrine:dbal dbname="xxxxxxxx" user="xxxxxxxx" password="" />
        <doctrine:orm />
        -->

        <!-- Swiftmailer Configuration -->
        <!--
        <swiftmailer:config
            transport="smtp"
            encryption="ssl"
            auth_mode="login"
            host="smtp.gmail.com"
            username="xxxxxxxx"
            password="xxxxxxxx" />
        -->

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('app', 'config', array(
            'charset'         => 'UTF-8',
            'error_handler'   => null,
            'csrf-protection' => array('enabled' => true, 'secret' => 'xxxxxxxxxx'),
            'router'          => array('resource' => '%kernel.root_dir%/config/routing.php'),
            'validation'      => array('enabled' => true, 'annotations' => true),
            'templating'      => array(
                #'assets_version' => "SomeVersionScheme",
            ),
            'session' => array(
                'default_locale' => "en",
                'lifetime' => "3600",
            ),
        ));

        // Twig Configuration
        /*
        $container->loadFromExtension('twig', 'config', array('auto_reload' => true));
        */

        // Doctrine Configuration
        /*
        $container->loadFromExtension('doctrine', 'dbal', array(
            'dbname'   => 'xxxxxxxx',
            'user'     => 'xxxxxxxx',
            'password' => '',
        ));
        $container->loadFromExtension('doctrine', 'orm');
        */

        // Swiftmailer Configuration
        /*
        $container->loadFromExtension('swiftmailer', 'config', array(
            'transport'  => "smtp",
            'encryption' => "ssl",
            'auth_mode'  => "login",
            'host'       => "smtp.gmail.com",
            'username'   => "xxxxxxxx",
            'password'   => "xxxxxxxx",
        ));
        */

Fiecare intrare asemenea ``app.config`` definește configurarea pentru un bundle.

Fiecare :term:`mediu` poate să suprascrie configurarea implicită prin
intermediul unui fișier de configurare specific:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_dev.yml
        imports:
            - { resource: config.yml }

        app.config:
            router:   { resource: "%kernel.root_dir%/config/routing_dev.yml" }
            profiler: { only_exceptions: false }

        webprofiler.config:
            toolbar: true
            intercept_redirects: true

        zend.config:
            logger:
                priority: debug
                path:     %kernel.logs_dir%/%kernel.environment%.log

    .. code-block:: xml

        <!-- app/config/config_dev.xml -->
        <imports>
            <import resource="config.xml" />
        </imports>

        <app:config>
            <app:router resource="%kernel.root_dir%/config/routing_dev.xml" />
            <app:profiler only-exceptions="false" />
        </app:config>

        <webprofiler:config
            toolbar="true"
            intercept-redirects="true"
        />

        <zend:config>
            <zend:logger priority="info" path="%kernel.logs_dir%/%kernel.environment%.log" />
        </zend:config>

    .. code-block:: php

        // app/config/config_dev.php
        $loader->import('config.php');

        $container->loadFromExtension('app', 'config', array(
            'router'   => array('resource' => '%kernel.root_dir%/config/routing_dev.php'),
            'profiler' => array('only-exceptions' => false),
        ));

        $container->loadFromExtension('webprofiler', 'config', array(
            'toolbar' => true,
            'intercept-redirects' => true,
        ));

        $container->loadFromExtension('zend', 'config', array(
            'logger' => array(
                'priority' => 'info',
                'path'     => '%kernel.logs_dir%/%kernel.environment%.log',
            ),
        ));

Acum înțelegeți de ce Symfony2 este atât de flexibil? Când doriți partajarea
bundle-urilor între aplicații, le puteți stoca local sau global, alegerea vă
aparține.

.. index::
    single: Vendori

Utilizarea vendorilor
---------------------

Este foarte probabil ca aplicația dumneavoastră să depindă de terțe biblioteci.
Acestea trebuie stocate în folderul ``src/vendor/``. Acest folder deja conține
bibliotecile Symfony2, biblioteca SwiftMailer, ORM-ul Doctrine, sistemul de
șablonare Twig și o selecție de clase ce aparțin Zend Framework.

.. index::
    single: Caching-ul configurării
    single: Jurnale

Cache și jurnale
----------------

Symfony2 este probabil unul dintre cele mai rapide framework-uri full-stack.
Dar cum poate fi atât de rapid dacă analizează și interpretează zeci de fișiere
YAML și XML pentru fiecare cerere? Aceasta se datoareaza parțial sistemului său
de cache. Configurarea aplicației este interpretată doar pentru prima cerere și
transformată în cod PHP simplu, stocat în folderul ``cache/`` al aplicației. În
mediul de dezvoltare, Symfony2 este suficient de inteligent să curețe cache-ul
când se aduc modificări unui fișier. În mediul de producție însă, este
responsabilitatea dumneavoastră să curățați cache-ul atunci când actualizați
codul sau modificați configurarea.

Atunci când dezvoltați o aplicație web, pot apărea probleme din multiple
direcții. Fișierele jurnal aflate în folderul ``logs/`` al aplicației, vă
informează în detaliu cu privire la cererile efectuate și vă ajută să remediati
rapid problemele.

.. index::
    single: CLI
    single: Linie de comandă

Interfața liniei de comandă
---------------------------

Fiecare aplicație vine însoțită de o unealtă în linie de comandă (``consolă``)
care vă ajută în diverse scopuri. Ea vă furnizează comenzi care vă sporesc
productivitatea prin automatizarea sarcinilor plictisitoare și repetitive.

Utilizați această unealtă fără nici un argument pentru a afla mai multe despre
capacitățile sale:

.. code-block:: bash

    $ php app/console

Opțiunea ``--help`` vă ajută să descoperiți modul de utilizare al unei comenzi:

.. code-block:: bash

    $ php app/console router:debug --help

Concluzii
---------

După lecturarea acestei părți ar trebui să vă simțiți confortabil cu lucrurile
elementare și să faceți Symfony2 să lucreze pentru dumneavoastră. Orice este
realizat în Symfony2 este astfel gândit încât să nu vă stea în cale. Deci, nu
ezitați să redenumiți și să mutați folderele după cum credeți de cuviință.

În aceasta a constat turul rapid. De la testarea aplicației până la trimiterea
de email-uri, mai aveți multe de învățat pentru a deveni un expert Symfony2.
Sunteți pregătit să abordați aceste subiecte acum? Nu mai ezitați - navigați
pe pagina oficială a `ghidurilor`_ și alegeți subiectul dorit.

.. _standardele: http://groups.google.com/group/php-standards/web/psr-0-final-proposal
.. _convenția:   http://pear.php.net/
.. _ghidurilor:  http://www.symfony-reloaded.org/learn
