data wygenerowania: ‘2019-październik-24’

Spis treści
===========

1.  [Podsumowanie badań](#summary)
2.  [Wykorzystane biblioteki](#librarys)
3.  [XXX](#xxx)
4.  [Wczytywanie danych z pliku](#readDataFromFile)
5.  [Brakujące dane](#missingData)
6.  [Rozmiar zbioru i statystyki](#statistics)
7.  [Szczegółowa analiza zbiorów wartości](#analisis)
8.  [Korelacja między zmiennymi](#correlation)
9.  [Zmiana rozmiaru śledzia w czasie](#animation)
10. [Przewidywanie rozmiaru śledzia](#prediction)
11. [Analiza ważnośći atrybutów najlepszego znalezionego modelu
    regresji](#bestModelAnalisis)

Podsumowanie badań <a name="summary"></a>
-----------------------------------------

TODO: rozdział podsumowujący całą analizę

Wykorzystane biblioteki <a name="librarys"></a>
-----------------------------------------------

Kod wyliczający wykorzystane biblioteki.

XXX <a name="xxx"></a>
----------------------

Kod zapewniający powtarzalność wyników przy każdym uruchomieniu raportu
na tych samych danych.

Wczytywanie danych z pliku <a name="readDataFromFile"></a>
----------------------------------------------------------

    data <- read.table("sledzie.csv", header=TRUE, sep=",")
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

Brakujące dane <a name="missingData"></a>
-----------------------------------------

Kod przetwarzający brakujące dane.

Rozmiar zbioru i statystyki <a name="statistics"></a>
-----------------------------------------------------

Sekcja podsumowująca rozmiar zbioru i podstawowe statystyki.

Szczegółowa analiza zbiorów wartości <a name="analisis"></a>
------------------------------------------------------------

Szczegółowa analiza wartości atrybutów (np. poprzez prezentację
rozkładów wartości).

Korelacja między zmiennymi <a name="correlation"></a>
-----------------------------------------------------

Sekcja sprawdzająca korelacje między zmiennymi; sekcja ta powinna
zawierać jakąś formę graficznej prezentacji korelacji.

Zmiana rozmiaru śledzia w czasie <a name="animation"></a>
---------------------------------------------------------

Interaktywny wykres lub animację prezentującą zmianę rozmiaru śledzi w
czasie.

Przewidywanie rozmiaru śledzia <a name="prediction"></a>
--------------------------------------------------------

Sekcję próbującą stworzyć regresor przewidujący rozmiar śledzia (w tej
sekcji należy wykorzystać wiedzę z pozostałych punktów oraz wykonać
dodatkowe czynności, które mogą poprawić trafność predykcji); dobór
parametrów modelu oraz oszacowanie jego skuteczności powinny zostać
wykonane za pomocą techniki podziału zbioru na dane uczące, walidujące i
testowe; trafność regresji powinna zostać oszacowana na podstawie miar
R2 i RMSE.

Analiza ważnośći atrybutów najlepszego znalezionego modelu regresji <a name="bestModelAnalisis"></a>
----------------------------------------------------------------------------------------------------

Analiza ważności atrybutów najlepszego znalezionego modelu regresji.
Analiza ważności atrybutów powinna stanowić próbę odpowiedzi na pytanie:
co sprawia, że rozmiar śledzi zaczął w pewnym momencie maleć.
