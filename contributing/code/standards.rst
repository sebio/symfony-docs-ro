Standarde de codare
===================

Când contribuiți la codul Symfony2, trebuie să-i urmați standardele de codare.
Pentru a scurta o poveste suficient de lungă, iată regula de aur: *Imitați codul
Symfony2 existent*.

Structură
---------

* Nu utilizați forma prescurtată a etichetei de deschidere (`<?`);

* Nu terminați fișierele ce conțin clase cu eticheta de închidere (`?>`);

* Indentația se realizează în pași de câte patru spații (tab-urile nu sunt
  permise);

* Utilizați caracterul linefeed (`0x0A`) pentru a delimita liniile;

* Adăugați un singur spațiu după fiecare delimitare cu virgulă;

* Nu adăugați spații după o paranteză deschisă și înainte de una închisă;

* Adăugați un singur spațiu în jurul operatorilor (`==`, `&&`, ...);

* Adăugați un singur spațiu înainte de paranteza deschisă a unei structuri de
  control (`if`, `else`, `for`, `while`, ...);

* Lăsați o linie goală înainte de declarațiile `return`;

* Nu lăsați spații goale la capătul liniilor;

* Utilizați acolade pentru încadrarea corpului unei structuri de control,
  indiferent de numărul de declarații pe care acesta le conține;

* Puneți acoladele pe o linie separată la declararea claselor, metodelor și
  funcțiilor;

* Separați declarația condițională de acoladă cu un singur spațiu și nu o linie
  noua;

* Declarați explicit vizibilitatea pentru clase, metode și proprietăți
  (utilizarea `var` este interzisă);

* Utilizați minuscule pentru constantele native PHP: `false`, `true` și `null`.
  Aceeași regulă se aplică și pentru `array()`;

* Utilizați majuscule pentru constante și separați cuvintele cu underscore-uri;

* Definiți o singură clasă per fișier;

* Declarați proprietățile clasei înaintea metodelor;

* Declarați mai întâi metodele publice, apoi pe cele protejate și în cele din
  urmă pe cele private.

Convenții pentru denumiri
-------------------------

* Utilizați formatul camelCase, nu underscore, pentru denumirea variabilelor,
  funcțiilor și metodelor;

* Utilizați underscore-uri pentru denumirea opțiunilor, argumentelor și
  parametrilor;

* Utilizați namespace-uri pentru toate clasele;

* Utilizați `Symfony` ca prim nivel al namespace-ului;

* Sufixați interfețele cu `Interface`;

* Utilizați caractere alfanumerice și underscore-uri pentru denumirea
  fișierelor;

Documentație
------------

* Adăugați blocuri PHPDoc pentru toate clasele, metodele și funcțiile;

* Adnotațiile `@package` și `@subpackage` nu sunt folosite.
