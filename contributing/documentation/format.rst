Formatul documentației
======================

Documentația Symfony2 folosește `reStructuredText`_ ca limbaj de marcare și
`Sphinx`_ pentru generarea fișierelor (HTML, PDF, ...).

reStructuredText
----------------

reStructuredText "este un limbaj de marcare și interpretare plaintext, ușor de
citit, având la bază principiul ceea-ce-tu-vezi-este-ceea-ce-primești
(WYSIWYG)".

Puteți învăța mai multe despre sintaxă citind `documentele`_ Symfony2 existente
sau lecturând `reStructuredText Primer`_ pe website-ul Sphinx.

Dacă sunteți familiarizat cu Markdown, fiți prudent deoarece unele lucruri
similare se comportă diferit:

* Listele pornesc de la începutul unei linii (indentația nu este permisă);

* Blocurile de cod în linie se marchează cu dublu-apostrof (````asemenea
  acestuia````).

Sphinx
------

Sphinx este un sistem de interpretare care adaugă câteva unelte pentru crearea
documentației plecând de la documentele reStructuredText. Ca atare, adaugă noi
directive și interpretări textului față de `marcajele`_ reST standard.

Evidențierea sintaxei
~~~~~~~~~~~~~~~~~~~~~

Toate exemplele de cod folosesc în mod implicit PHP ca limbaj cu sintaxa
evidențiată. Puteți să schimbați aceasta utilizând directiva ``code-block``:

.. code-block:: rst

    .. code-block:: yaml

        { foo: bar, bar: { foo: bar, bar: baz } }

Dacă secvența de cod începe cu ``<?php``, trebuie  utilizați ``html+php`` ca
pseudo-limbaj de evidențiere:

.. code-block:: rst

    .. code-block:: html+php

        <?php echo $this->foobar(); ?>

.. note::

    O listă cu limbajele suportate este disponibilă pe website-ul `Pygments`_.

Blocuri de configurare
~~~~~~~~~~~~~~~~~~~~~~

De fiecare dată când este prezentată o configurare, trebuie folosită directiva
``configuration-block`` pentru a afișa configurarea în toate formatele suportate
(``PHP``, ``YAML`` și ``XML``):

.. code-block:: rst

    .. configuration-block::

        .. code-block:: yaml

            # Configuration in YAML

        .. code-block:: xml

            <!-- Configuration in XML //-->

        .. code-block:: php

            // Configuration in PHP

Fragmentul reST prezentat mai sus este redat astfel:

.. configuration-block::

    .. code-block:: yaml

        # Configuration in YAML

    .. code-block:: xml

        <!-- Configuration in XML //-->

    .. code-block:: php

        // Configuration in PHP

Lista formatelor suportate este următoarea:

=============== ===========
Format          Afisat ca
=============== ===========
html            HTML
xml             XML
php             PHP
yaml            YAML
jinja           Twig
html+jinja      Twig
jinja+html      Twig
php+html        PHP
html+php        PHP
ini             INI
php-annotations Annotations
=============== ===========

.. _reStructuredText:        http://docutils.sf.net/rst.html
.. _Sphinx:                  http://sphinx.pocoo.org/
.. _documentele:             http://github.com/symfony/symfony-docs
.. _reStructuredText Primer: http://sphinx.pocoo.org/rest.html
.. _marcajele:               http://sphinx.pocoo.org/markup/
.. _Pygments:                http://pygments.org/languages/
