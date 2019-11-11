-   [Podsumowanie badań](#podsumowanie-badań)
-   [Wykorzystane biblioteki](#wykorzystane-biblioteki)
-   [Zapewnienie powtarzalności wyników przy każdym uruchomieniu raportu
    na tych samych
    danych](#zapewnienie-powtarzalności-wyników-przy-każdym-uruchomieniu-raportu-na-tych-samych-danych)
-   [Wstęp](#wstęp)
-   [Wczytywanie danych z pliku](#wczytywanie-danych-z-pliku)
-   [Rozmiar zbioru danych i podstawowe
    statystyki](#rozmiar-zbioru-danych-i-podstawowe-statystyki)
-   [Brakujące dane](#brakujące-dane)
-   [Szczegółowa analiza zbiorów
    wartości](#szczegółowa-analiza-zbiorów-wartości)
-   [Korelacja między zmiennymi](#korelacja-między-zmiennymi)
-   [Zmiana rozmiaru śledzia w
    czasie](#zmiana-rozmiaru-śledzia-w-czasie)
-   [Przewidywanie rozmiaru śledzia](#przewidywanie-rozmiaru-śledzia)
    -   [Random Forest](#random-forest)
    -   [Regresja liniowa](#regresja-liniowa)
    -   [kNN](#knn)
    -   [Porównanie modeli](#porównanie-modeli)
-   [Analiza ważnośći atrybutów najlepszego znalezionego modelu
    regresji](#analiza-ważnośći-atrybutów-najlepszego-znalezionego-modelu-regresji)

data wygenerowania: ‘2019-listopad-11’

Podsumowanie badań
==================

***\[TODO\]*** podsumowanie calego raportu

Wykorzystane biblioteki
=======================

Do analizy danych i stworzenia raportu z tej analizy zostały
wykorzystane następujące biblioteki: - datasets  
- corrplot  
- ggplot2  
- gganimate  
- dplyr  
- gridExtra  
- gifski package  
- imputeTS  
- caret

Zapewnienie powtarzalności wyników przy każdym uruchomieniu raportu na tych samych danych
=========================================================================================

Aby zapewnić powtarzalność próbkowań i losowań liczb (m. inn. przy
próbkowaniu zbioru) przy każdym odpaleniu programu ustawiono stałe
ziarno.

    set.seed(23)

Wstęp
=====

W podanym sprawozdaniu użyto zbioru danych sledzie.csv pobranego ze
strony
<a href="http://www.cs.put.poznan.pl/alabijak/emd/projekt/sledzie.csv" class="uri">http://www.cs.put.poznan.pl/alabijak/emd/projekt/sledzie.csv</a>
. Zbiór ten opisuje rozmiary śledzi i warunki, w których żyją od 60-ciu
lat. Kolejne kolumny w zbiorze danych to:  
\* **length**: długość złowionego śledzia \[cm\];  
\* **cfin1**: dostępność planktonu \[zagęszczenie Calanus finmarchicus
gat. 1\];  
\* **cfin2**: dostępność planktonu \[zagęszczenie Calanus finmarchicus
gat. 2\];  
\* **chel1**: dostępność planktonu \[zagęszczenie Calanus helgolandicus
gat. 1\];  
\* **chel2**: dostępność planktonu \[zagęszczenie Calanus helgolandicus
gat. 2\];  
\* **lcop1**: dostępność planktonu \[zagęszczenie widłonogów gat. 1\];  
\* **lcop2**: dostępność planktonu \[zagęszczenie widłonogów gat. 2\];  
\* **fbar**: natężenie połowów w regionie \[ułamek pozostawionego
narybku\];  
\* **recr**: roczny narybek \[liczba śledzi\];  
\* **cumf**: łączne roczne natężenie połowów w regionie \[ułamek
pozostawionego narybku\];  
\* **totaln**: łączna liczba ryb złowionych w ramach połowu \[liczba
śledzi\];  
\* **sst**: temperatura przy powierzchni wody \[°C\];  
\* **sal**: poziom zasolenia wody \[Knudsen ppt\];  
\* **xmonth**: miesiąc połowu \[numer miesiąca\];  
\* **nao**: oscylacja północnoatlantycka \[mb\].  
Wiersze w zbiorze są uporządkowane chronologicznie.

Wczytywanie danych z pliku
==========================

    names <- read.table("sledzie.csv", nrow=1, stringsAsFactors = FALSE, sep = ",")
    data <- read.table("sledzie.csv", header=TRUE, stringsAsFactors = FALSE, sep=",")
    head(data)

    ##   X length   cfin1   cfin2   chel1    chel2   lcop1    lcop2  fbar   recr
    ## 1 0   23.0 0.02778 0.27785 2.46875        ? 2.54787 26.35881 0.356 482831
    ## 2 1   22.5 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 3 2   25.0 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 4 3   25.5 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 5 4   24.0 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 6 5   22.0 0.02778 0.27785 2.46875 21.43548 2.54787        ? 0.356 482831
    ##        cumf   totaln           sst      sal xmonth nao
    ## 1 0.3059879 267380.8 14.3069330186 35.51234      7 2.8
    ## 2 0.3059879 267380.8 14.3069330186 35.51234      7 2.8
    ## 3 0.3059879 267380.8 14.3069330186 35.51234      7 2.8
    ## 4 0.3059879 267380.8 14.3069330186 35.51234      7 2.8
    ## 5 0.3059879 267380.8 14.3069330186 35.51234      7 2.8
    ## 6 0.3059879 267380.8 14.3069330186 35.51234      7 2.8

Jak widać, wczytane dane zawierają znak “?” przy nieznanych danych.

Wyświetlenie klas poszczególnych kolumn:

    sapply(data, class)

    ##           X      length       cfin1       cfin2       chel1       chel2 
    ##   "integer"   "numeric" "character" "character" "character" "character" 
    ##       lcop1       lcop2        fbar        recr        cumf      totaln 
    ## "character" "character"   "numeric"   "integer"   "numeric"   "numeric" 
    ##         sst         sal      xmonth         nao 
    ## "character"   "numeric"   "integer"   "numeric"

Dane zawierające znak “?” zostały zinterpretowane jako tekst.  
Z uwagi na występowanie brakujących danych konieczna była zmiana “?” na
“NA” wraz ze zmianą typu danych z character na numeric:

    data[data=="?"] <- NA

Po zmianie znaku “?” na wartość NA zmianiono typ danych kolumn z
nieznanymi wartościami na wartości numeryczne.

    data$cfin1 <- as.numeric(data$cfin1)
    data$cfin2 <- as.numeric(data$cfin2)
    data$chel1 <- as.numeric(data$chel1)
    data$chel2 <- as.numeric(data$chel2)
    data$lcop1 <- as.numeric(data$lcop1)
    data$lcop2 <- as.numeric(data$lcop2)
    data$sst <- as.numeric(data$sst)

    head(data)

    ##   X length   cfin1   cfin2   chel1    chel2   lcop1    lcop2  fbar   recr
    ## 1 0   23.0 0.02778 0.27785 2.46875       NA 2.54787 26.35881 0.356 482831
    ## 2 1   22.5 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 3 2   25.0 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 4 3   25.5 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 5 4   24.0 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
    ## 6 5   22.0 0.02778 0.27785 2.46875 21.43548 2.54787       NA 0.356 482831
    ##        cumf   totaln      sst      sal xmonth nao
    ## 1 0.3059879 267380.8 14.30693 35.51234      7 2.8
    ## 2 0.3059879 267380.8 14.30693 35.51234      7 2.8
    ## 3 0.3059879 267380.8 14.30693 35.51234      7 2.8
    ## 4 0.3059879 267380.8 14.30693 35.51234      7 2.8
    ## 5 0.3059879 267380.8 14.30693 35.51234      7 2.8
    ## 6 0.3059879 267380.8 14.30693 35.51234      7 2.8

    sapply(data, class)

    ##         X    length     cfin1     cfin2     chel1     chel2     lcop1 
    ## "integer" "numeric" "numeric" "numeric" "numeric" "numeric" "numeric" 
    ##     lcop2      fbar      recr      cumf    totaln       sst       sal 
    ## "numeric" "numeric" "integer" "numeric" "numeric" "numeric" "numeric" 
    ##    xmonth       nao 
    ## "integer" "numeric"

Rozmiar zbioru danych i podstawowe statystyki
=============================================

    paste("Wczytane dane zawierają ", nrow(data), " rekordów oraz ", ncol(data), " kolumn.", sep=" ")

    ## [1] "Wczytane dane zawierają  52582  rekordów oraz  16  kolumn."

Podsumowanie wartości danych:

    summary(data)

    ##        X             length         cfin1             cfin2        
    ##  Min.   :    0   Min.   :19.0   Min.   : 0.0000   Min.   : 0.0000  
    ##  1st Qu.:13145   1st Qu.:24.0   1st Qu.: 0.0000   1st Qu.: 0.2778  
    ##  Median :26291   Median :25.5   Median : 0.1111   Median : 0.7012  
    ##  Mean   :26291   Mean   :25.3   Mean   : 0.4458   Mean   : 2.0248  
    ##  3rd Qu.:39436   3rd Qu.:26.5   3rd Qu.: 0.3333   3rd Qu.: 1.7936  
    ##  Max.   :52581   Max.   :32.5   Max.   :37.6667   Max.   :19.3958  
    ##                                 NA's   :1581      NA's   :1536     
    ##      chel1            chel2            lcop1              lcop2       
    ##  Min.   : 0.000   Min.   : 5.238   Min.   :  0.3074   Min.   : 7.849  
    ##  1st Qu.: 2.469   1st Qu.:13.427   1st Qu.:  2.5479   1st Qu.:17.808  
    ##  Median : 5.750   Median :21.673   Median :  7.0000   Median :24.859  
    ##  Mean   :10.006   Mean   :21.221   Mean   : 12.8108   Mean   :28.419  
    ##  3rd Qu.:11.500   3rd Qu.:27.193   3rd Qu.: 21.2315   3rd Qu.:37.232  
    ##  Max.   :75.000   Max.   :57.706   Max.   :115.5833   Max.   :68.736  
    ##  NA's   :1555     NA's   :1556     NA's   :1653       NA's   :1591    
    ##       fbar             recr              cumf             totaln       
    ##  Min.   :0.0680   Min.   : 140515   Min.   :0.06833   Min.   : 144137  
    ##  1st Qu.:0.2270   1st Qu.: 360061   1st Qu.:0.14809   1st Qu.: 306068  
    ##  Median :0.3320   Median : 421391   Median :0.23191   Median : 539558  
    ##  Mean   :0.3304   Mean   : 520367   Mean   :0.22981   Mean   : 514973  
    ##  3rd Qu.:0.4560   3rd Qu.: 724151   3rd Qu.:0.29803   3rd Qu.: 730351  
    ##  Max.   :0.8490   Max.   :1565890   Max.   :0.39801   Max.   :1015595  
    ##                                                                        
    ##       sst             sal            xmonth            nao          
    ##  Min.   :12.77   Min.   :35.40   Min.   : 1.000   Min.   :-4.89000  
    ##  1st Qu.:13.60   1st Qu.:35.51   1st Qu.: 5.000   1st Qu.:-1.89000  
    ##  Median :13.86   Median :35.51   Median : 8.000   Median : 0.20000  
    ##  Mean   :13.87   Mean   :35.51   Mean   : 7.258   Mean   :-0.09236  
    ##  3rd Qu.:14.16   3rd Qu.:35.52   3rd Qu.: 9.000   3rd Qu.: 1.63000  
    ##  Max.   :14.73   Max.   :35.61   Max.   :12.000   Max.   : 5.08000  
    ##  NA's   :1584

Ilość brakujących danych dla poszczególnych kolumn:

    missingData <- sapply(data, function(x) sum(is.na(x)))
    missingData

    ##      X length  cfin1  cfin2  chel1  chel2  lcop1  lcop2   fbar   recr 
    ##      0      0   1581   1536   1555   1556   1653   1591      0      0 
    ##   cumf totaln    sst    sal xmonth    nao 
    ##      0      0   1584      0      0      0

Wykres ilości brakujących danych w zbiorze:

    plot(x = factor(names(missingData)), y = missingData, main="Wykres ilości brakujących danych w zbiorze", xlab="atrybut", ylab = "ilość brakujących artybutów")

![](README_files/figure-markdown_strict/unnamed-chunk-9-1.png)

Jak widać zbiór danych zawiera znaczną ilość brakujących danych, co może
utrudnić ich późniejszą analizę.

Brakujące dane
==============

    library("imputeTS")
    data$cfin1 <- na_kalman(data$cfin1)
    data$cfin2 <- na_kalman(data$cfin2)
    data$chel1 <- na_kalman(data$chel1)
    data$chel2 <- na_kalman(data$chel2)
    data$lcop1 <- na_kalman(data$lcop1)
    data$lcop2 <- na_kalman(data$lcop2)
    data$sst <- na_kalman(data$sst)

Ilosć brakujących danych w poszczególnych kolumnach po zastosowaniu
filtru Kalman’a:

    missingData <- sapply(data, function(x) sum(is.na(x)))
    missingData

    ##      X length  cfin1  cfin2  chel1  chel2  lcop1  lcop2   fbar   recr 
    ##      0      0      0      0      0      0      0      0      0      0 
    ##   cumf totaln    sst    sal xmonth    nao 
    ##      0      0      0      0      0      0

Z powyższej tabeli wynika, że filtr Kalman’a poradził sobie ze
wszystkimi brakującymi danymi.

Szczegółowa analiza zbiorów wartości
====================================

Poniżej przedstawiono rozkłady wszystkich wartośi w zbiorze danych

    library(ggplot2)
    library(ggExtra)

    lengthDissPlot <- ggplot(data, aes(x=length)) + geom_histogram(binwidth=.5, colour="black", fill="white")
    cfin1DissPlot <- ggplot(data, aes(x=cfin1)) + geom_histogram(binwidth=1, colour="black", fill="white")
    cfin2DissPlot <- ggplot(data, aes(x=cfin2)) + geom_histogram(binwidth=1, colour="black", fill="white")
    chel1DissPlot <- ggplot(data, aes(x=chel1)) + geom_histogram(binwidth=.5, colour="black", fill="white")
    chel2DissPlot <- ggplot(data, aes(x=chel2)) + geom_histogram(binwidth=.5, colour="black", fill="white")
    lcop1DissPlot <- ggplot(data, aes(x=lcop1)) + geom_histogram(binwidth=.5, colour="black", fill="white")
    lcop2DissPlot <- ggplot(data, aes(x=lcop2)) + geom_histogram(binwidth=.5, colour="black", fill="white")
    fbarDissPlot <- ggplot(data, aes(x=fbar)) + geom_histogram(binwidth=.05, colour="black", fill="white")
    recrDissPlot <- ggplot(data, aes(x=recr)) + geom_histogram(binwidth=50000.0, colour="black", fill="white")
    cumfDissPlot <- ggplot(data, aes(x=cumf)) + geom_histogram(binwidth=.02, colour="black", fill="white")
    totalnDissPlot <- ggplot(data, aes(x=totaln)) + geom_histogram(binwidth=1000.0, colour="black", fill="white")
    sstDissPlot <- ggplot(data, aes(x=sst)) + geom_histogram(binwidth=1.0, colour="black", fill="white")
    salDissPlot <- ggplot(data, aes(x=sal)) + geom_histogram(binwidth=.01, colour="black", fill="white")
    xmonthDissPlot <- ggplot(data, aes(x=xmonth)) + geom_histogram(binwidth=1.0, colour="black", fill="white")
    naoDissPlot <- ggplot(data, aes(x=nao)) + geom_histogram(binwidth=.5, colour="black", fill="white")


    require(gridExtra)
    grid.arrange(lengthDissPlot,cfin1DissPlot,cfin2DissPlot ,chel1DissPlot ,chel2DissPlot,lcop1DissPlot,lcop2DissPlot ,fbarDissPlot ,
    recrDissPlot ,cumfDissPlot ,totalnDissPlot ,sstDissPlot ,salDissPlot ,xmonthDissPlot ,naoDissPlot, nrow = 5, ncol=3)

![](README_files/figure-markdown_strict/unnamed-chunk-12-1.png)

Z powyższych wykresów wynika, że jeśli chodzi o rozkład poszczególnych
atrybutów to zbiór nie jest zrównoważony.

Analizując długość śledzia (*length*) można zauważyć, że ten atrybut
przyjmuje rozkład zbliżony do rozkładu normalnego. W zbiorze danych
znajduje się najwięcej śledzi o długości równej 25-26cm. Ze
wcześniejszej analizy podsumowującej dane wynika także, że minimalna
długość śledzia w zbiorze to 19cm, a maksymalna to 32.5cm. Średnia
długość 25.3cm, a mediana to 25,5.

Jeśli chodzi o atrybut jakim jest dostępność planktonu (zarówno
zagęszczenie *Calanus finmarchicus gat. 1* jak i *Calanus finmarchicus
gat. 2*) to można zauważyć że w zbiorze znalazło się znacznie więcej
danych z łowisk o mniejszej dostępności planktonu omawianego gatunku. Ze
wcześniejszej analizy wiemy, że minimalna dostępność pierwszego gatunku
tego planktonu to 0, maksymalna: ok 37,67. Srednia to ok. 0,45 a mediana
to ok. 0,11. Jeśli chodzi o drugi gatunek planktonu *Calanus
finmarchicus* to jego minimalne zagęszczenie wynosi 0, maksymalne - ok.
19,4, średnia to ok. 2,03 a mediana - 0.7.

Kolejnym badanym atrybutem jest dostępność planktonu gatunku *Calanus
helgolandicus gat. 1* i *Calanus helgolandicus gat. 1*. W przypadku
pierwszego z nich w zbiorze znalazło się więcej śledzi złowionych na
łowisku o mniejszej zawartości planktonu. W Przypadku drugiego gatunku
planktonu rozkład ilości złowionych tam śledzi był bardziej rozłożony w
przedziale 0-40. O śledziach złapanych w miejscach gdzie zagęszczenie
omawianego planktonu drugiego gatunku osiąga przedział 40-60 można
powiedzieć że są towartości odstające. Jeśli chodzi o zagęszczenie
pierwszego gatunku to ze wcześniejszej analizy wynika, że atrubut
tenprzyjmuje minimalną wartość równą 0, a maksymalną równą 75. Jego
średnia występowania to ok. 10, a mediana to 5.75. Jeśli chodzi o
minimalną wartość dostępności planktonu drugiego gatunku *Calanus
helgolandicus* to jego minimalna wartość to ok. 5.2, maksymalna: ok.
57.7, średnia to ok. 21.2, a mediana wynosi około 21.7.

Ostatnim z planktonów, których dostępność badaliśmy są widłonogi dwóch
gatunków (*lcop1* i *lcop2*). ***\[TOTO\]*** Kolejnym atrybutem jest
natężenie połowów w regionie (ułamek pozostawionego narybku) - *fbar*.
***\[TOTO\]*** Następnym z znalizowanych atrybutów będzie ilość rocznego
narybku (czyli liczba śledzi) - *recr*. ***\[TOTO\]*** ***cumf: łączne
roczne natężenie połowów w regionie \[ułamek pozostawionego narybku\]; *
totaln: łączna liczba ryb złowionych w ramach połowu \[liczba śledzi\];
\* sst: temperatura przy powierzchni wody \[°C\]; \* sal: poziom
zasolenia wody \[Knudsen ppt\]; \* xmonth: miesiąc połowu \[numer
miesiąca\]; \* nao: oscylacja północnoatlantycka \[mb\].**\*

Korelacja między zmiennymi
==========================

W niniejszej sekcji została przeanalizowana korelacja między
atrybutami.Na początek przedstawiono współczynniki korelacji wraz z
graficzną macierzą korelacji.

Największa korelacja występuje między atrybutem *chel1* i *lcop1* -
czyli między dostępnością planktonu dwóch gatunków i wynosi ona 0.96.
Między *chel2* i *lcop2* - współczynnik korelacji wynosi 0.89, a między
*cumf* ( łączne roczne natężenie połowów w regionie) a *fbar* (natężenie
połowów w regionie): 0.82 (zależność między tymi ostatnimi wynika
prawdopodobnie z tego, że na podstawie jednego z tych atrybutów w
naturalny sposób oblicza się dtugi z nich). Wysoka korelacja występuje
także między *cfin2* a *lcop2*, gdzie współczynnik korelacji jest równy
0.65.

Bardzo niski, jednak ujemny współczynnik korelacji (wynoszący -0.55)
miedzy atrybutem *lcop1* a *neo* (oscylacja północnoatlantycka) świadczy
o wysokiej, ale odwrotnej zaleznosci tych atrybutów. Może to być
wyjaśnione z punktu widzenia przyrody przez to, że plankton może być
przenoszony przez prądy morskie.

Niski ujemny współczynnik korelacji wskazujący na wysoką korelacje
atrybutów można też zauważyć między *cumf* (łączne roczne natężenie
połowów w regionie) a *totaln* (łączna liczba ryb złowionych w ramach
połowu) - wynosi on -0.71, gdzie taka zależność też jest naturalna.

Jeśli chodzi o atrybut, którego będą dotyczyć badania w dalszej części
pracy, czyli ługość śledzia, to jest najbardziej skorelowana z
temperaturą przy powierzchni wody, a współczynnik tej korelacji wynosi
-0.45 (czyli są to wartości odwrotnie zależne)

    res <- cor(data)

    library(corrplot)
    corrplot.mixed(res, tl.col = "black", tl.srt = 45)

![](README_files/figure-markdown_strict/unnamed-chunk-13-1.png)

Żeby lepiej zwizualizować korelacje między omówionymi atrybutami
przedstawiono wykresy zależności tych atrybutów.

    plot(data[,c(5,7,16)])

![](README_files/figure-markdown_strict/unnamed-chunk-14-1.png)

W przypadku zaprezentowanych powyżej danych, zgodnie z wartościami
mieszczącymi się w macierzy korelacji, największą korelacje
(współczynnik korelacji wynoszący 0.96) widać między dostępnością
planktonu pierwszego gatunku Calanus helgolandicus (*chel1*) a
zagęszczeniem widłonogów z gatunku pierwszego (*lcop2*). Z wykresu
wynika, że im więcej jednego plantonu w łowisku, tym spotkać tam można
więcej drugiego z wymienionych planktonów.  
Zależność między *lcop2* a oscylacja północnoatlantycka *neo* nie jest
aż tak widoczna na wykresie (choć też da się ją zauważyć).

    plot(data[,c(6,8,4)])

![](README_files/figure-markdown_strict/unnamed-chunk-15-1.png)

Podobnie jak na poprzednim wykresie, i tu najbardziej widoczna jest
korelacja między dostępnością dwóch z gatunków planktonu.

    plot(data[,c(9,11,12)])

![](README_files/figure-markdown_strict/unnamed-chunk-16-1.png)

Na powyższym wykresie korelacji najbardziej widać odwrotnie
proporcjonalną zależność między *fbar* a *totaln* - współczynnik
korelacjiwynoszący -0.71) i *cumf* a *totaln*. Można wyraźnie zauważyć
że im więcej pierwszego z atrybutów, tym mniej drugiego i odwrotnie.  
Wysoce proporcjonalne są zmienne *fbar* i *cumf*.

Zmiana rozmiaru śledzia w czasie
================================

    library(ggplot2)
    library(gganimate)
    library(dplyr)

    sampled <- sample_n(data,  round(nrow(data)*0.1))

    p <- ggplot(
      sampled, aes(X, length, group = xmonth, color = factor(xmonth))) +
      geom_line() +
      scale_color_viridis_d() +
      labs(x = "Kolejne połowy", y = "Dlugosc sledzia") +
      theme(legend.position = "top")

    p + transition_reveal(X)

![](README_files/figure-markdown_strict/unnamed-chunk-18-1.gif)

Z wykresu można zauważyć delikatny spadek śledzia w czasie. Choć na
początku rozmiar śledzia znaczie rośnie, to po którkim czasie zaczyna
już maleć.

Przewidywanie rozmiaru śledzia
==============================

***\[TODO\]*** wywaliś z danych X!!! W niniejszej sekcji zostanie
podjęta próba stworzenia regresora przewidującego rozmiar śledzia.

W niniejszym rozdziale postaramy się przewidzieć rozmiar śledzia z
wykorzystaniem różnych metod uczenia maszynowego. W tym celu na początek
dane zostaną podzielone na zbiór uczący (stanowiący 75% całego zbioru
danych) i testowy (stanowiący 25% całego zbioru danych).

Parametry zostały domyślnie zoptymalizowane wybierając trzy wartości dla
każdego zdefiniowanego dla modelu parametru optymalizacyjnego.

Do badań zostanie wykorzystana powtarzana ocena krzyżowa.

    library(caret)
    library(ggplot2)
    library(gganimate)
    library(dplyr)

    inTraining <- createDataPartition(y = data$length, p = .75,list = FALSE)

    training <- data[ inTraining, ]
    testing  <- data[-inTraining, ]

    ctrl <- trainControl( method = "repeatedcv", number = 5, repeats = 10)

Random Forest
-------------

Pierwszym z wykorzystanych algorytmów będzie Random Forest z liczbą
drzew w lesie równą 10.

    fitRandomForestF <- train(length ~ ., data = training, method = "rf", importance=T, trControl = ctrl, ntree = 10) 
    fitRandomForestF

    ## Random Forest 
    ## 
    ## 39438 samples
    ##    15 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (5 fold, repeated 10 times) 
    ## Summary of sample sizes: 31549, 31550, 31550, 31552, 31551, 31552, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   mtry  RMSE      Rsquared   MAE      
    ##    2    1.122600  0.5379404  0.8861271
    ##    8    1.111296  0.5521322  0.8722134
    ##   15    1.181804  0.5105627  0.9272984
    ## 
    ## RMSE was used to select the optimal model using the smallest value.
    ## The final value used for the model was mtry = 8.

    rfClasses <- predict(fitRandomForestF, newdata = testing)
    rfClasses <- sapply(rfClasses, round, digits = 0)

    availableValues <- sapply(testing$length, round, digits = 0)
    levels <- unique(c(availableValues, rfClasses))

    confusionMatrix <- confusionMatrix(data = factor(rfClasses, levels = levels),   factor(availableValues, levels = levels))
    confusionMatrix

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction   23   22   24   26   21   20   25   27   28   30   29   32
    ##         23  267  404  530   38   27   14   67    3    1    0    0    0
    ##         22   25   93   43    1   18   10    3    0    0    0    0    0
    ##         24  237  222  978  244    9    1  244   16   10    0    0    0
    ##         26   36    8  439 1772    0    0  439  384  391   10   10    1
    ##         21    0    7    1    0    3    2    0    0    0    0    0    0
    ##         20    0    0    0    0    0    0    0    0    0    0    0    0
    ##         25  102   50 1200 1302    3    0  615  131   76    2    3    1
    ##         27    5    1   82  799    0    0   91  372  726   12   28    2
    ##         28    0    1    8   99    0    0    5   72  260   25   38    0
    ##         30    0    0    0    0    0    0    0    0    0    0    0    0
    ##         29    0    0    1    4    0    0    0    4    9    2    3    0
    ##         32    0    0    0    0    0    0    0    0    0    0    0    0
    ##         31    0    0    0    0    0    0    0    0    0    0    0    0
    ##           Reference
    ## Prediction   31
    ##         23    0
    ##         22    0
    ##         24    0
    ##         26    0
    ##         21    0
    ##         20    0
    ##         25    0
    ##         27    1
    ##         28    1
    ##         30    0
    ##         29    0
    ##         32    0
    ##         31    0
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.3319          
    ##                  95% CI : (0.3239, 0.3401)
    ##     No Information Rate : 0.324           
    ##     P-Value [Acc > NIR] : 0.02705         
    ##                                           
    ##                   Kappa : 0.1899          
    ##                                           
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: 23 Class: 22 Class: 24 Class: 26 Class: 21
    ## Sensitivity            0.39732  0.118321   0.29799    0.4161 0.0500000
    ## Specificity            0.91309  0.991908   0.90032    0.8066 0.9992357
    ## Pos Pred Value         0.19763  0.481865   0.49873    0.5077 0.2307692
    ## Neg Pred Value         0.96566  0.946491   0.79397    0.7424 0.9956591
    ## Prevalence             0.05113  0.059799   0.24970    0.3240 0.0045648
    ## Detection Rate         0.02031  0.007075   0.07441    0.1348 0.0002282
    ## Detection Prevalence   0.10278  0.014684   0.14919    0.2655 0.0009890
    ## Balanced Accuracy      0.65520  0.555114   0.59916    0.6114 0.5246179
    ##                      Class: 20 Class: 25 Class: 27 Class: 28 Class: 30
    ## Sensitivity           0.000000   0.42008   0.37882   0.17651   0.00000
    ## Specificity           1.000000   0.75428   0.85636   0.97867   1.00000
    ## Pos Pred Value             NaN   0.17647   0.17555   0.51081       NaN
    ## Neg Pred Value        0.997946   0.91210   0.94467   0.90400   0.99612
    ## Prevalence            0.002054   0.11138   0.07471   0.11207   0.00388
    ## Detection Rate        0.000000   0.04679   0.02830   0.01978   0.00000
    ## Detection Prevalence  0.000000   0.26514   0.16121   0.03872   0.00000
    ## Balanced Accuracy     0.500000   0.58718   0.61759   0.57759   0.50000
    ##                      Class: 29 Class: 32 Class: 31
    ## Sensitivity          0.0365854 0.0000000 0.0000000
    ## Specificity          0.9984688 1.0000000 1.0000000
    ## Pos Pred Value       0.1304348       NaN       NaN
    ## Neg Pred Value       0.9939791 0.9996957 0.9998478
    ## Prevalence           0.0062386 0.0003043 0.0001522
    ## Detection Rate       0.0002282 0.0000000 0.0000000
    ## Detection Prevalence 0.0017498 0.0000000 0.0000000
    ## Balanced Accuracy    0.5175271 0.5000000 0.5000000

    confusionMatrix$overall

    ##       Accuracy          Kappa  AccuracyLower  AccuracyUpper   AccuracyNull 
    ##     0.33193853     0.18988195     0.32388795     0.34006358     0.32402617 
    ## AccuracyPValue  McnemarPValue 
    ##     0.02705031            NaN

***\[TODO\]*** opisac cos o tym

Regresja liniowa
----------------

Kolejnym przebadanym regresorem będzie regresja liniowa

    fitLR <- train(length ~ ., data = training, method = "lm", importance=T, trControl = ctrl)
    fitLR

    ## Linear Regression 
    ## 
    ## 39438 samples
    ##    15 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (5 fold, repeated 10 times) 
    ## Summary of sample sizes: 31551, 31550, 31551, 31549, 31551, 31552, ... 
    ## Resampling results:
    ## 
    ##   RMSE      Rsquared   MAE     
    ##   1.327042  0.3539804  1.046856
    ## 
    ## Tuning parameter 'intercept' was held constant at a value of TRUE

    lrClasses <- predict(fitLR, newdata = testing)
    lrClasses <- sapply(lrClasses, round, digits = 0)

    availableValues <- sapply(testing$length, round, digits = 0)
    levels <- unique(c(availableValues, lrClasses))

    confusionMatrix <- confusionMatrix(data = factor(lrClasses, levels = levels),   factor(availableValues, levels = levels))
    confusionMatrix

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction   23   22   24   26   21   20   25   27   28   30   29   32
    ##         23  134  208  268   38    7    1   42    2    0    0    0    0
    ##         22    0    0    0    0    0    0    0    0    0    0    0    0
    ##         24  251  284  791  240   19    9  211   12    2    0    0    0
    ##         26   38   20  637 1922    0    0  437  493  870   24   45    2
    ##         21    0    0    0    0    0    0    0    0    0    0    0    0
    ##         20    0    0    0    0    0    0    0    0    0    0    0    0
    ##         25  246  272 1545 1715   34   17  744  284  208    7    7    0
    ##         27    3    1   30  304    0    0   20  173  347   19   27    1
    ##         28    0    1   11   39    0    0   10   18   46    1    3    1
    ##         30    0    0    0    1    0    0    0    0    0    0    0    0
    ##         29    0    0    0    0    0    0    0    0    0    0    0    0
    ##         32    0    0    0    0    0    0    0    0    0    0    0    0
    ##         31    0    0    0    0    0    0    0    0    0    0    0    0
    ##           Reference
    ## Prediction   31
    ##         23    0
    ##         22    0
    ##         24    0
    ##         26    1
    ##         21    0
    ##         20    0
    ##         25    0
    ##         27    0
    ##         28    1
    ##         30    0
    ##         29    0
    ##         32    0
    ##         31    0
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.2899          
    ##                  95% CI : (0.2821, 0.2977)
    ##     No Information Rate : 0.324           
    ##     P-Value [Acc > NIR] : 1               
    ##                                           
    ##                   Kappa : 0.1153          
    ##                                           
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: 23 Class: 22 Class: 24 Class: 26 Class: 21
    ## Sensitivity            0.19940    0.0000   0.24101    0.4513  0.000000
    ## Specificity            0.95462    1.0000   0.89576    0.7111  1.000000
    ## Pos Pred Value         0.19143       NaN   0.43485    0.4282       NaN
    ## Neg Pred Value         0.95677    0.9402   0.78004    0.7300  0.995435
    ## Prevalence             0.05113    0.0598   0.24970    0.3240  0.004565
    ## Detection Rate         0.01019    0.0000   0.06018    0.1462  0.000000
    ## Detection Prevalence   0.05326    0.0000   0.13839    0.3415  0.000000
    ## Balanced Accuracy      0.57701    0.5000   0.56839    0.5812  0.500000
    ##                      Class: 20 Class: 25 Class: 27 Class: 28 Class: 30
    ## Sensitivity           0.000000    0.5082   0.17617  0.031229 0.000e+00
    ## Specificity           1.000000    0.6289   0.93817  0.992717 9.999e-01
    ## Pos Pred Value             NaN    0.1465   0.18703  0.351145 0.000e+00
    ## Neg Pred Value        0.997946    0.9107   0.93379  0.890340 9.961e-01
    ## Prevalence            0.002054    0.1114   0.07471  0.112066 3.880e-03
    ## Detection Rate        0.000000    0.0566   0.01316  0.003500 0.000e+00
    ## Detection Prevalence  0.000000    0.3864   0.07037  0.009967 7.608e-05
    ## Balanced Accuracy     0.500000    0.5685   0.55717  0.511973 5.000e-01
    ##                      Class: 29 Class: 32 Class: 31
    ## Sensitivity           0.000000 0.0000000 0.0000000
    ## Specificity           1.000000 1.0000000 1.0000000
    ## Pos Pred Value             NaN       NaN       NaN
    ## Neg Pred Value        0.993761 0.9996957 0.9998478
    ## Prevalence            0.006239 0.0003043 0.0001522
    ## Detection Rate        0.000000 0.0000000 0.0000000
    ## Detection Prevalence  0.000000 0.0000000 0.0000000
    ## Balanced Accuracy     0.500000 0.5000000 0.5000000

    confusionMatrix$overall

    ##       Accuracy          Kappa  AccuracyLower  AccuracyUpper   AccuracyNull 
    ##      0.2898661      0.1152575      0.2821190      0.2977063      0.3240262 
    ## AccuracyPValue  McnemarPValue 
    ##      1.0000000            NaN

***\[TODO\]*** opisac cos o tym

kNN
---

Ostatnim z przebadanych algorytmów został kNN

    fitKNN <- train(length ~ ., data = training, method = "knn", importance=T, trControl = ctrl)
    fitKNN

    ## k-Nearest Neighbors 
    ## 
    ## 39438 samples
    ##    15 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (5 fold, repeated 10 times) 
    ## Summary of sample sizes: 31551, 31552, 31549, 31550, 31550, 31551, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   k  RMSE      Rsquared   MAE      
    ##   5  1.123669  0.5447638  0.8812479
    ##   7  1.107900  0.5540048  0.8701258
    ##   9  1.100991  0.5579812  0.8652422
    ## 
    ## RMSE was used to select the optimal model using the smallest value.
    ## The final value used for the model was k = 9.

    knnClasses <- predict(fitKNN, newdata = testing)
    knnClasses <- sapply(knnClasses, round, digits = 0)

    availableValues <- sapply(testing$length, round, digits = 0)
    levels <- unique(c(availableValues, knnClasses))

    confusionMatrix <- confusionMatrix(data = factor(knnClasses, levels = levels),   factor(availableValues, levels = levels))
    confusionMatrix

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction   23   22   24   26   21   20   25   27   28   30   29   32
    ##         23  257  386  495   33   26   13   47    3    0    0    0    0
    ##         22   32  108   38    1   21   10    4    0    0    0    0    0
    ##         24  253  224 1050  269   11    2  274    9   13    0    0    0
    ##         26   33    7  469 1888    1    0  467  430  410    9   11    0
    ##         21    1    4    0    0    0    1    0    0    0    0    0    0
    ##         20    0    0    0    0    0    0    0    0    0    0    0    0
    ##         25   92   56 1153 1204    1    1  595  125   64    3    2    1
    ##         27    4    1   69  777    0    0   70  341  694   10   31    2
    ##         28    0    0    7   86    0    0    7   74  288   28   37    1
    ##         30    0    0    0    0    0    0    0    0    0    0    0    0
    ##         29    0    0    1    1    0    0    0    0    4    1    1    0
    ##         32    0    0    0    0    0    0    0    0    0    0    0    0
    ##         31    0    0    0    0    0    0    0    0    0    0    0    0
    ##           Reference
    ## Prediction   31
    ##         23    0
    ##         22    0
    ##         24    0
    ##         26    0
    ##         21    0
    ##         20    0
    ##         25    0
    ##         27    1
    ##         28    1
    ##         30    0
    ##         29    0
    ##         32    0
    ##         31    0
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.3445          
    ##                  95% CI : (0.3364, 0.3527)
    ##     No Information Rate : 0.324           
    ##     P-Value [Acc > NIR] : 3.197e-07       
    ##                                           
    ##                   Kappa : 0.1991          
    ##                                           
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: 23 Class: 22 Class: 24 Class: 26 Class: 21
    ## Sensitivity            0.38244  0.137405   0.31993    0.4433 0.0000000
    ## Specificity            0.91958  0.991423   0.89302    0.7932 0.9995414
    ## Pos Pred Value         0.20397  0.504673   0.49881    0.5068 0.0000000
    ## Neg Pred Value         0.96508  0.947564   0.79781    0.7483 0.9954331
    ## Prevalence             0.05113  0.059799   0.24970    0.3240 0.0045648
    ## Detection Rate         0.01955  0.008217   0.07988    0.1436 0.0000000
    ## Detection Prevalence   0.09586  0.016281   0.16015    0.2834 0.0004565
    ## Balanced Accuracy      0.65101  0.564414   0.60648    0.6183 0.4997707
    ##                      Class: 20 Class: 25 Class: 27 Class: 28 Class: 30
    ## Sensitivity           0.000000   0.40642   0.34725   0.19552   0.00000
    ## Specificity           1.000000   0.76866   0.86359   0.97935   1.00000
    ## Pos Pred Value             NaN   0.18047   0.17050   0.54442       NaN
    ## Neg Pred Value        0.997946   0.91175   0.94248   0.90606   0.99612
    ## Prevalence            0.002054   0.11138   0.07471   0.11207   0.00388
    ## Detection Rate        0.000000   0.04527   0.02594   0.02191   0.00000
    ## Detection Prevalence  0.000000   0.25084   0.15216   0.04025   0.00000
    ## Balanced Accuracy     0.500000   0.58754   0.60542   0.58743   0.50000
    ##                      Class: 29 Class: 32 Class: 31
    ## Sensitivity          1.220e-02 0.0000000 0.0000000
    ## Specificity          9.995e-01 1.0000000 1.0000000
    ## Pos Pred Value       1.250e-01       NaN       NaN
    ## Neg Pred Value       9.938e-01 0.9996957 0.9998478
    ## Prevalence           6.239e-03 0.0003043 0.0001522
    ## Detection Rate       7.608e-05 0.0000000 0.0000000
    ## Detection Prevalence 6.086e-04 0.0000000 0.0000000
    ## Balanced Accuracy    5.058e-01 0.5000000 0.5000000

    plot(fitKNN)

![](README_files/figure-markdown_strict/unnamed-chunk-25-1.png)

    confusionMatrix$overall

    ##       Accuracy          Kappa  AccuracyLower  AccuracyUpper   AccuracyNull 
    ##   3.444918e-01   1.991203e-01   3.363650e-01   3.526874e-01   3.240262e-01 
    ## AccuracyPValue  McnemarPValue 
    ##   3.197132e-07            NaN

***\[TODO\]*** opisac cos o tym

Porównanie modeli
-----------------

Na koniec modele zostały porównane na podstawie miary średniej
kwadratowej błędów RMSE (w przypadku tej miary, im mniejszą wartość
osiągnie algorytm, tym jest lepszy) i współczynnik determinacji
R<sup>2</sup> (dopsowanie modelu jest tym lepsze im wartość
R<sup>2</sup> jest bliższa jedności):

    x <- list(randomForest = fitRandomForestF, linearRegression = fitLR, kNN = fitKNN) %>% resamples()

    dotplot(x, metric = "RMSE")

![](README_files/figure-markdown_strict/unnamed-chunk-26-1.png)

    dotplot(x, metric = "Rsquared")

![](README_files/figure-markdown_strict/unnamed-chunk-26-2.png)

Analiza ważnośći atrybutów najlepszego znalezionego modelu regresji
===================================================================

***\[TODO\]*** Analiza ważności atrybutów najlepszego znalezionego
modelu regresji. Analiza ważności atrybutów powinna stanowić próbę
odpowiedzi na pytanie: co sprawia, że rozmiar śledzi zaczął w pewnym
momencie maleć.

Najlepszym znalezionym modelem regresji okazał się być algorytm kNN, dla
którego przeanalizowaliśmy ważność atrybutów:

    ggplot(varImp(fitKNN))

![](README_files/figure-markdown_strict/unnamed-chunk-27-1.png)
***\[TODO\]*** opisac
