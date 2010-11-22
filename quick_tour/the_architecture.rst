Arhitectura
===========

Sunteți un erou! Cine ar fi crezut că veți ajunge aici după primele trei părți?
Eforturile dumneavoastră vor fi răsplătite în curând, așa cum se cuvine. Primele
trei părți nu prezintă o vedere detaliată asupra arhitecturii framework-ului.
Dar, deaorece ea face ca Symfony2 să se facă remarcat printre celelalte
framework-uri, haideți să aflăm mai multe acum.

.. index::
    single: Structură foldere

Structura de foldere
--------------------

Structura de foldere a unei :term:`aplicații <aplicație>` Symfony2 este destul
de flexibilă, însă structura de foldere a sandbox-ului reflectă forma tipică și
recomandată pentru o aplicație Symfony2:

* ``app/``: Acest folder conține configurația aplicației;

* ``src/``: Întregul cod PHP este stocat în acest folder;

* ``web/``: Acesta ar trebui să fie directorul rădăcină web.

Directorul web
~~~~~~~~~~~~~~

Directorul rădăcină web este gazda tuturor fișierelor publice și statice precum,
imaginile, foile de stil, și fișierele JavaScript. Este de asemenea locul unde
se găsesc controlerele frontale:

.. code-block:: html+php

    <!-- web/app.php -->
    <?php

    require_once __DIR__.'/../app/AppKernel.php';

    $kernel = new AppKernel('prod', false);
    $kernel->handle()->send();

Asemenea oricărui controler frontal, ``app.php`` utilizează o clasă Kernel,
``AppKernel``, pentru procesul de bootstrap al aplicației.

.. index::
    single: Kernel

Directorul aplicației
~~~~~~~~~~~~~~~~~~~~~

Clasa ``AppKernel`` reprezintă principalul punct de intrare pentru configurația
aplicației și, ca atare, este salvată în folderul ``app/``.

Acesta clasă trebuie să implementeze patru metode:

* ``registerRootDir()``: Întoarce folderul rădăcină al configurației;

* ``registerBundles()``: Întoarce un array cu toate bundle-urile necesare
  pentru rularea aplicației (observați referința la
  ``Application\HelloBundle\HelloBundle``);

* ``registerBundleDirs()``: Întoarce un array asociativ cu namespace-urile și
  folderele respective lor;

* ``registerContainerConfiguration()``: Întoarce obiectul principal al
  configurației (mai multe despre acesta veți afla un pic mai târziu);

Aruncați o privire peste implementarea implicită a acestor metode pentru a
înțelege mai bine flexibilitatea framework-ului.

Pentru a face ca lucrurile să funcționeze bine împreună, kernel-ul are nevoie de
un fișier aflat în folderul ``src/``::

    // app/AppKernel.php
    require_once __DIR__.'/../src/autoload.php';

Directorul sursă
~~~~~~~~~~~~~~~~

Fișierul ``src/autoload.php`` este responsabil de încărcarea automată a tuturor
fișierelor depozitate în folderul ``src/``::

    // src/autoload.php
    $vendorDir = __DIR__.'/vendor';

    require_once $vendorDir.'/symfony/src/Symfony/Component/HttpFoundation/UniversalClassLoader.php';

    use Symfony\Component\HttpFoundation\UniversalClassLoader;

    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'                    => $vendorDir.'/symfony/src',
        'Application'                => __DIR__,
        'Bundle'                     => __DIR__,
        'Doctrine\\Common'           => $vendorDir.'/doctrine-common/lib',
        'Doctrine\\DBAL\\Migrations' => $vendorDir.'/doctrine-migrations/lib',
        'Doctrine\\ODM\\MongoDB'     => $vendorDir.'/doctrine-mongodb/lib',
        'Doctrine\\DBAL'             => $vendorDir.'/doctrine-dbal/lib',
        'Doctrine'                   => $vendorDir.'/doctrine/lib',
        'Zend'                       => $vendorDir.'/zend/library',
    ));
    $loader->registerPrefixes(array(
        'Swift_' => $vendorDir.'/swiftmailer/lib/classes',
        'Twig_'  => $vendorDir.'/twig/lib',
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

Această secțiune abia atinge suprafața uneia dintre caracteristicile cele mai
importante și mai puternice ale Symfony2, sistemul sau de
:term:`bundle-uri <bundle>`.

Un bundle este asemenea unui plugin întâlnit în alte programe. Dar atunci de ce
este denumit bundle și nu plugin? Pentru că, în Symfony2, totul este un bundle,
de la caracteristicile de bază ale framework-ului până la codul pe care îl
scrieți pentru aplicația dumneavoastră. Bundle-urile sunt cetățeni de prima
clasă în Symfony2. Aceasta vă oferă flexibilitatea de a folosi facilități
livrate de terți prin intermediul de bundle-uri pre-construite, sau de a
distribui propriile bundle-uri. Este foarte ușor să alegeți ce facilități doriți
să folosiți în cadrul aplicației și să le optimizați după bunul plac.

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
            new Application\AppBundle\AppBundle(),
        );

        if ($this->isDebug()) {
            $bundles[] = new Symfony\Bundle\WebProfilerBundle\WebProfilerBundle();
        }

        return $bundles;
    }

Pe lângă ``HelloBundle`` de care am amintit deja, observați că în cadrul
kernel-ului sunt activate de asemena ``FrameworkBundle``, ``DoctrineBundle``,
``SwiftmailerBundle`` și ``ZendBundle``. Toate fac parte din baza
framework-ului.

Fiecare bundle poate fi personalizat prin intermediul fișierelor de configurare
scrise în YAML, XML, sau PHP. Să aruncăm o privire la configurarea implicită:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        app.config:
            charset:       UTF-8
            error_handler: null
            csrf_secret:   xxxxxxxxxx
            router:        { resource: "%kernel.root_dir%/config/routing.yml" }
            validation:    { enabled: true, annotations: true }
            templating:
                escaping:       htmlspecialchars
                #assets_version: SomeVersionScheme
            #user:
            #    default_locale: fr
            #    session:
            #        name:     SYMFONY
            #        type:     Native
            #        lifetime: 3600

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
        <app:config csrf-secret="xxxxxxxxxx" charset="UTF-8" error-handler="null">
            <app:router resource="%kernel.root_dir%/config/routing.xml" />
            <app:validation enabled="true" annotations="true" />
            <app:templating escaping="htmlspecialchars" />
            <!--
            <app:user default-locale="fr">
                <app:session name="SYMFONY" type="Native" lifetime="3600" />
            </app:user>
            //-->
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
            'charset'       => 'UTF-8',
            'error_handler' => null,
            'csrf-secret'   => 'xxxxxxxxxx',
            'router'        => array('resource' => '%kernel.root_dir%/config/routing.php'),
            'validation'    => array('enabled' => true, 'annotations' => true),
            'templating'    => array(
                'escaping'        => 'htmlspecialchars'
                #'assets_version' => "SomeVersionScheme",
            ),
            #'user' => array(
            #    'default_locale' => "fr",
            #    'session' => array(
            #        'name' => "SYMFONY",
            #        'type' => "Native",
            #        'lifetime' => "3600",
            #    )
            #),
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

Fiecare :term:`mediu` poate să suprascrie configurația implicită prin
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
                path:     %kernel.root_dir%/logs/%kernel.environment%.log

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

După cum am putut observa puțin mai devreme, o aplicație este constituită din
bundle-uri așa cum este definit în metoda ``registerBundles()``. Dar de unde
știe Symfony2 unde să caute bundle-urile? Symfony2 este destul de flexibil în
această privință. Metoda ``registerBundleDirs()`` trebuie să întoarcă un array
asociativ care asociază namespace-urile cu folderele corespunzătoare (fie locale
sau globale)::

    public function registerBundleDirs()
    {
        return array(
            'Application'     => __DIR__.'/../src/Application',
            'Bundle'          => __DIR__.'/../src/Bundle',
            'Symfony\\Bundle' => __DIR__.'/../src/vendor/symfony/src/Symfony/Bundle',
        );
    }

Prin urmare, când vă referiți la ``HelloBundle``, în numele unui controler sau
al unui șablon, Symfony2 va căuta în folderele furnizate.

Acum înțelegeți de ce Symfony2 este atât de flexibil? Când doriți partajarea
bundle-urilor între aplicații, le puteți stoca local sau global, alegerea
aparținându-vă.

.. index::
    single: Vendori

Utilizarea vendorilor
---------------------

Este foarte probabil ca aplicația dumneavoastră să depindă de terțe biblioteci.
Acestea trebuie stocate în folderul ``src/vendor/``. Acesta deja conține
bibliotecile Symfony2, biblioteca SwiftMailer, ORM-ul Doctrine, ORM-ul Propel,
sistemul de șablonare Twig, și o selecție de clase ce aparțin Zend Framework.

.. index::
    single: Caching-ul configurației
    single: Jurnale

Cache și jurnale
----------------

Symfony2 este probabil unul dintre cele mai rapide framework-uri full-stack.
Dar cum poate fi atât de rapid dacă analizează și interpretează zeci de fișiere
YAML și XML pentru fiecare cerere? Aceasta se datoareaza parțial sistemului său
de cache. Configurația aplicației este interpretată doar pentru prima cerere și
transformată în cod PHP simplu, stocat în folderul ``cache/`` al aplicației. În
mediul de dezvoltare, Symfony2 este suficient de inteligent să curețe cache-ul
când se aduc modificări unui fișier. Dar în mediul de producție, este
responsabilitatea dumneavoastră să curățați cache-ul atunci când actualizați
codul sau modificați configurația.

Atunci când dezvoltați o aplicație web, pot apărea probleme din multe direcții.
Fișierele jurnal aflate în folderul ``logs/`` al aplicației, vă spun totul
despre cererile efectuate și vă ajută să remediati problemele în cel mai scurt
timp.

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

După lecturarea acestei părți, ar trebui să vă simțiți confortabil cu lucrurile
elementare și să faceți Symfony2 să lucreze pentru dumneavoastră. Orice este
realizat în Symfony2, este astfel gândit să nu vă stea în cale. Deci, nu ezitați
să redenumiți și să mutați folderele după cum credeți de cuviință.

În aceasta a constat turul rapid. De la testarea aplicației până la trimiterea
de email-uri, mai aveți multe de învățat pentru a deveni un expert Symfony2.
Sunteți pregătit să abordați aceste subiecte acum? Nu mai ezitați, mergeți
pe pagina oficială a `ghidurilor`_ și alegeți subiectul dorit.

.. _standardele: http://groups.google.com/group/php-standards/web/psr-0-final-proposal
.. _convenția:   http://pear.php.net/
.. _ghidurilor:  http://www.symfony-reloaded.org/learn
