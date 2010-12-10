Convenții
=========

Documentul :doc:`standarde <standards>` descrie standardele de codare pentru
proiectele Symfony2 și bundle-urile interne și externe. Acest document descrie
standardele de codare și convențiile folosite la baza framework-ului pentu al
face mai consistent și previzibil. Puteți urma aceste convenții în propriul
dumneavoastră cod, dar nu sunteți obligat.

Numele metodelor
----------------

Atunci când un obiect deține o relație "principală" cu diverse "lucruri"
(obiecte, parametrii, ...), numele metodelor este normalizat:

  * ``get()``
  * ``set()``
  * ``has()``
  * ``all()``
  * ``replace()``
  * ``remove()``
  * ``clear()``
  * ``isEmpty()``
  * ``add()``
  * ``register()``
  * ``count()``
  * ``keys()``

Utilizarea acestor metode este permisă numai atunci când este evidentă existența
unei relații principale:

* un ``CookieJar`` are multe ``Cookie``-uri;

* un Service ``Container`` are multe servicii și mulți parametrii (deoarece
  relația principală este pe servicii, folosim convenția de denumire pentru
  această relație);

* un Console ``Input`` are multe argumente și multe opțiuni. Nu există o relație
  "principală", prin urmare convenția nu se aplică.

Pentru multe relații unde convenția nu se aplică, următoarele metode trebuie
folosite în schimb (unde ``XXX`` este denumirea lucrului asociat):

================== =================
Relația principală Alte relații
================== =================
``get()``          ``getXXX()``
``set()``          ``setXXX()``
``has()``          ``hasXXX()``
``all()``          ``getXXXs()``
``replace()``      ``setXXXs()``
``remove()``       ``removeXXX()``
``clear()``        ``clearXXX()``
``isEmpty()``      ``isEmptyXXX()``
``add()``          ``addXXX()``
``register()``     ``registerXXX()``
``count()``        ``countXXX()``
``keys()``         n/a
================== =================
