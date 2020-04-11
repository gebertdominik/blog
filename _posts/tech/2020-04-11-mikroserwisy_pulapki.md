---
layout: page
title: "Mikroserwisy - co może pójść źle?"
subheadline: "Pułapki mikroserwisów"
teaser: "Firmy decydując się na architekturę mikrousługową nie zawsze zdają sobie sprawę jakie ma ona wady. W wielu przypadkach utworzone usługi są hybrydą monolitu z mikroserwisami. Istnieje szereg rzeczy które mogą doprowadzić nas do takiej sytuacji. Na co zatem należy zwrócić uwagę?"
categories:
    - architektura
tags:
    - architektura
    - mikroserwisy
    - autoryzacja
    - komunikacja
header:
    title: Mikroserwisy - co może pójść źle?
    background-color: "#EFC94C;"
    image_fullwidth: mikroserwisy_title.jpg
    caption: Photo by Alina Grubnyak
    caption_url: https://unsplash.com/@alinnnaaaa
image:
    thumb: mikroserwisy_thumb.jpg
    homepage: mikroserwisy_title.jpg
    caption: Photo by Alina Grubnyak
---
<!--more-->

## Po co mikrosierwisy?

Zacznijmy od odpowiedzi na pytanie, po co tak na prawdę decydujemy się na taką architekturę. Najważniejszą rzeczą jaką wprowadzają mikrosierwisy jest autonomia. Autonomia która pozwala nam na niezależne wdrażanie i testowanie komponentów.
Release komponentu nie musi angażować całego zespołu składającego się ze 100 osób, a jedynie zespół odpowiedzialny za daną część. Pozostali pracownicy mogą w tym czasie kontynuować pracę nad innymi modułami systemu. Poza tym mniejszy obszar, to krótsze testy i  mniej rzeczy może się zepsuć.

## Granice mikroserwisów

Ustalając podział aplikacji na moduły należy oprzeć się na tzw. _Bounded Contextach_, które wyznaczają ich naturalne granice. Mikroserwis zawsze jest zorientowany biznesowo, nie technicznie. Chcemy przede wszystkim osiągnąć sytuację w której serwis skupiony jest w okół jednej domeny i odpowiada za niego jeden ekspert biznesowy.

Gdy granice wyznaczone są w zły sposób tracimy autonomię mikroserwisów. Już nie jedna, a wiele osób odpowiada od strony biznesowej za dany serwis. W architekturze tej, serwisem nie może być np. zależność techniczna jak na przykład generowanie dokumentu, drukowanie czy wysyłanie wiadomości. Do współdzielenia zależności technicznych przeznaczone są biblioteki, pliki dll itp. Sama techniczna zależność nie niesie za sobą bezpośrednio wartości biznesowej, a zatem nie byłoby osoby odpowiedzialnej za ten obszar.

## Dostępność

W każdej umowie projektowej zdefiniowany jest wskaźnik SLA. Mówi nam on o dostępności usługi, która może wynosić np. 99,9% czasu. Znacznie łatwiej zapewnić jest dostępność systemu dla monolitu, niż dla projektu składającego się z wielu modułów. Co dzieje się gdy mamy 100 mikroserwisów komunikujących się między sobą z dostępnością systemu? Jeżeli każdy z nich dostępny jest przez przyjęte 99,9% czasu, to dostępność całego systemu równa się iloczynowi dostępności wszystkich modułów. W ten sposób otrzymujemy:

                P = P(1) * P(2) * … * P(100) = 99,9%^100 ~= 90%

Nagle okazuje się, że nasz nowoczesny system, oparty o mikrousługi nie jest dostępny przez 10% czasu! Wskaźnik ten i powyższe działanie wynika z założenia o synchronicznej komunikacji pomiędzy modułami. Komunikacja asynchroniczna może rozwiązać problem dostępności, jednak nie wszędzie możemy sobie na taką komunikację pozwolić.

## Zapewnienie jakości komunikacji

Druga kwestia to jakość - przede wszystkim jeśli chodzi o komunikację. Zapewnienie niezawodnej komunikacji leży u podstaw dobrze funkcjonującej aplikacji mikroserwisowej.
Chcąc mieć spójne komunikaty, spójny model danych, moglibyśmy wykorzystać Kanoniczny Model Danych. Ale czy na pewno? Posiadając kanoniczny model, zmiana w jednym serwisie pociąga za sobą zmiany w kolejnych. Znów tracimy naszą niezależność…

To może testy end to end? Niestety wykonanie ich na produkcji nie wchodzi w grę (“Testuj na produkcji, będzie szybciej!” - to nigdy nie zadziała). Wykonanie takich testów na środowisku developerskim czy testowym w większości przypadków też nie jest możliwe. Na środowiskach tych ciągle wprowadzamy zmiany, ciągle coś się psuje. Na pewno jest możliwe przeprowadzenie takich testów w małym projekcie, prowadzonym w małym zespole, ale w innym przypadku może to być niewykonalne.

Jeśli oba powyższe sposoby zapewnienia jakości komunikacji nie sprawdzają się tutaj, to warto zastosować inne rozwiązanie. Z pomocą przychodzą tutaj testy kontraktowe. Jeśli nasz klient potrafi porozumieć się z czymś co jest namiastką naszego serwera i serwer porozumiewa się z namiastką klienta, to istnieje bardzo duża szansa, że komunikacja pomiędzy klientem a serwerem będzie przebiegała poprawnie. Przetestowanie takiego kontraktu zapewnia nam względną pewność działania naszej aplikacji.

## Niezawodna sieć

Opierając aplikację na mikroserwisach zakładamy często, że sieć jest niezawodna. Oczywiście nie jest to prawdą, a błędy związane z komunikacją zdarzają się nadzwyczaj często. Budujemy system tak by nie dopuścić do wystąpienia żadnego błędu. Dla pewności dodajemy mnóstwo testów,co prowadzi nas do wykonującego się godzinami pipeline'u wdrożeniowego. Podejście dążące do zrobienia niezawodnego systemu warto zmienić na budowanie go w sposób taki, by w przypadku awarii ograniczyć lub wyeliminować jej skutki. Możemy zastosować ponawianie wysyłki komunikatów, jeśli zadbamy by komunikacja była idempotentna. Możemy także wykorzystać wzorzec circuit braker. Dodajemy także _procedury fallback_, które zostaną wykonane kiedy w aplikacji wystąpi błąd. Nawet jeśli biznes nie dopuszcza sytuacji, że coś się nie uda, to w ich kompetencjach leży zdefiniowanie co powinien zrobić system w przypadku awarii. Warto dodać także, że nigdy nie zapominamy o dokładnym przetestowaniu ścieżki krytycznej dla biznesu klienta.

## Wydajność

W architekturze mikroserwisowej, nawet jeśli zapewnialiście klienta inaczej, wydajność jest gorsza niż w klasycznym monolicie. Wynika to z narzutu komunikacyjnego. Oczywiście, jeżeli przepisujemy system, który powstał 15 lat temu i w międzyczasie dostosowywany był do zmieniających się wymagań biznesu, to jest duża szansa, że nowy system będzie wydajniejszy. Nie wynika to jednak z przyjętej atchitektury, a po prostu z możliwości optymalnego zaprojektowania systemu, który uwzględnia wszystkie aktualne wymagania.

Dbając o wydajność chcielibyśmy testować ją w możliwie najlepszy sposób. Niestety testy wydajnościowe są trudne i drogie, a podczas ich przeprowadzania czycha na nas dużo pułapek, które mogą prowadzić do błędnych wniosków. Ponadto ruch produkcyjny mamy jedynie na produkcji, a nie będziemy przecież tam przeprowadzać testów…

Nie ma tutaj bezpośredniego rozwiązania. Chcąc minimalizować negatywny wpływ wprowadzonych modyfikacji, należy dostarczać zmiany w małych paczkach, a wydajność powinna być nieustannie monitorowana na środowisku produkcyjnym.

## Wprowadzanie zmian

Wprowadzenie zmian w środowisku rozproszonym także jest utrudnione, a to co jest łatwe do zmiany na naszym laptopie, nie zawsze łatwo zmienić na środowisku produkcyjnym. Załóżmy, że mamy x serwisów komunikujących się ze sobą. Po wprowadzeniu zmiany konfiguracyjnej, często musimy zaktualizować wszystkie x serwisów. Nawet przy założeniu, że zmiana jest jednolinijkowa, to dalej musimy wprowadzić ją w x projektach. Na ratunek przychodzi tu automatyzacja. Jeżeli możemy coś zautomatyzować, należy to zrobić. Jeśli nie - zastanowić się jak to zmienić. Należy automatyzować wszystko co się da( podejście __infrastruktura jako kod__). Wszystkie joby na Jenkinsie powinny być generowane ze skryptów, deployment powinien być zautomatyzowany, maszyna wirtualna także powinna być konfigurowana przez skrypt. Odpada konieczność backupu maszyn, gdyż w każdej chwili możemy odtworzyć każdą z nich za pomocą skryptu. 

## Podsumowanie

Jak widać architektura mikroserwisowa niesie ze sobą mnóstwo pułapek. Mimo tego tak wiele firm decyduje się na nią w swoich projektach. Czasem wybór podyktowany jest  wyłącznie modą lub wymogami klienta. W tym wpisie przedstawiłem główne rzeczy z którymi musimy zmierzyć się wykorzystując tę architekturę. Mam nadzieję, że po przeczytaniu tego wpisu, w przyszłości będziecie w stanie dokonać bardziej świadomego wyboru.


