Traduceri
=========

Documentația Symfony2 este scrisă în limba engleză și mulți oameni sunt
implicați în procesul de traducere.

Contribuirea
------------

Mai întâi, deveniți familiar cu :doc:`limbajul de marcare <format>` utilizat
pentru documentație.

Apoi, abonați-vă la `mailing-list-ul documentației`_ Symfony2 deoarece toate
colaborarile se produc acolo.

În cele din urmă, găsiți depozitul *master* pentru limba la care doriți să
contribuiți. Lista depozitelor *master* oficiale este:

* *Engleză*: http://github.com/symfony/symfony-docs
* *Rusă*:    http://github.com/avalanche123/symfony-docs-ru
* *Română*:  http://github.com/sebastian-ionescu/symfony-docs-ro

.. note::

    Dacă doriți să contribuiți cu traduceri pentru o nouă limbă, citiți
    :ref:`secțiunea dedicată <translations-adding-a-new-language>`.

Alăturarea la Echipa de Traduceri
---------------------------------

Dacă doriți să ajutați cu traducerea unor documente în limba dumneavoastră sau
să reparați unele bug-uri, puteți avea în vedere să vă alăturați nouă; este un
proces foarte simplu:

* Prezentați-vă pe `mailing-list-ul documentației`_ Symfony2;
* *(opțional)* Întrebați la ce documente puteți lucra;
* Bifurcați depozitul *master* pentru limba dumneavoastră (faceți click pe
  butonul "Fork" de pe pagina Github);
* Traduceți câteva documente;
* Trimiteți un pull request (faceți click pe "Pull Request" din pagina dvs. pe
  Github);
* Managerul echipei va accepta modificările dvs. și le va alipi la depozitul
  principal;
* Website-ul documentației este actualizat în fiecare noapte din depozitul
  master;

.. _translations-adding-a-new-language:

Adăugarea unei limbi noi
------------------------

Această secțiune vă pune la dispoziție câteva îndrumări pentru începerea
traducerii documentației Symfony2 într-o limbă nouă.

Deaorece începerea unei traduceri presupune un volum mare de muncă, discutați
planul dumneavoastră pe `mailing-list-ul documentației`_ Symfony2 și încercați
să găsiți oameni motivați, dispuși să ofere o mână de ajutor.

Când echipa este formată, nominalizați un manager al echipei; el va fi
responsabil pentru depozitul *master*.

Creați depozitul și copiați documentele din limba *engleză*.

Acum echipa poate începe procesul de traducere.

Când echipa este încrezătoare că depozitul se află într-un stadiu suficient de
consistent și stabil (totul este tradus, sau documentele netraduse au fost
înlăturate din toctree-uri -- fișierele denumite ``index.rst`` și
``map.rst.inc``), managerul echipei poate solicita ca depozitul să fie adăugat
pe lista depozitelor *master* oficiale prin trimiterea unui email către Fabien
(fabien.potencier at symfony-project.org).

Întreținerea
------------

Procesul de traducere nu se termină atunci când totul a fost tradus.
Documentația este o țintă mobilă (adăugarea de noi documente, remediarea
bug-urilor, reorganizarea paragrafelor, ...). Echipa de traducere trebuie să
urmărească în permanență depozitul în limba engleză și să aplice, în cel mai
scurt timp, modificările și documentelor traduse.

.. caution::

    Traducerile neîntreținute vor fi șterse din lista depozitelor oficiale
    deoarece documentația învechită poate fi periculoasă.

.. _mailing-list-ul documentației: http://groups.google.com/group/symfony-docs
