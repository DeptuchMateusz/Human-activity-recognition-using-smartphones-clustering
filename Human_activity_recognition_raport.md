<head>
  <style>
    img {
      max-height: 250px;
      width: auto;
      display: block;
      margin-left: auto;
      margin-right: auto;
    }
  </style>
</head>


# Co telefony wiedzą o tobie, projekt z klasteryzacji - raport
Autorzy: [Mateusz Deptuch](https://github.com/DeptuchMateusz), [Paweł Florek](https://github.com/FlorekPawel)
## 0. Spis treści
1. [Dane](#1-dane)
2. [EDA](#2-eda)
3. [Preprocessing danych i feature selection](#3-preprocessing-danych-i-feature-selection)
4. [Modelowanie](#4-modelowanie)
5. [Wyniki](#5-wyniki)
6. [Wnioski](#6-wnioski)

## 1. Dane
Dane użyte w projekcie pochodziły ze strony kaggle, https://www.kaggle.com/datasets/uciml/human-activity-recognition-with-smartphones.
Celem projektu była klasteryzacja danych zebranych z czujników znajdujących się w telefonach osób testowych (później *subject*), których było 30. Wykonywali oni codzienne aktywności mając przy sobie swoje telefony z aktywnymi czujnikami (akcelerometr, żyroskop). 
Założeniem projektu na kaggle była klasyfikacja aktywności do jednej z cześciu kategorii, jednakże nie posiadaliśmy takowej informacji z powodu innego celu naszej pracy.

Sygnały jakie przechwytywały czujniki były następujące:
- tBodyAcc
- tGravityAccMag
- tBodyGyroJerkMag
- tBodyAccJerk
- tBodyGyro
- tBodyAccJerkMag
- tBodyGyroJerk
- tBodyAccMag
- tGravityAcc
- tBodyGyroMag
- fBodyAcc
- fBodyBodyGyroMag
- fBodyAccJerk
- fBodyBodyAccJerkMag
- fBodyAccMag
- fBodyGyro
- fBodyBodyGyroJerkMag,

w trzech osiach X, Y, Z. Przedrostek 't' oznacza, że dany sygnał został zapisany w domenie czasowej, natomiast przedrostek 'f' oznacza zapis w domenie frekwencyjną po zastosowaniu Szybkiej Transformacji Fouriera.
Zostały one zapisane przy użyciu funkcji:
- max()
- mad()
- min()
- kurtosis()
- bandsEnergy()
- mean()
- meanFreq()
- arCoeff()
- entropy()
- iqr()
- sma()
- std()
- maxInds()
- skewness()
- energy()
- correlation().

## 2. EDA
Przed rozpoczęciem pracy z przygotowywaniem danych oraz modelowaniem dokonaliśmy Eksploracyjnej Analizy Danych.
Ramka danych po podziale zbioru na zbiór modelarzy oraz zbiór walidatorów posiadała 10299 wierszy oraz 561 kolumn. Nie zawierała żadnych braków wartości oraz żadnych duplikujących się wierszy. 

Dokonaliśmy analizy danych dla dwóch funkcji: mean oraz entropy.

#### Analiza mean
![mean histogram](plots/mean_hist.png)
Dla mean obserwujemy dużo rozkładów.

![mean heatmap](plots/mean_heat.png)
Zauważamy duże korelacje niektórych kolumn. Będziemy mogli rozważyć usunięcię niektórych z nich w celu redukcji wymiarów naszych danych.

![mean pairplot](plots/mean_pair.png)
Pomimo bardzo dużo skorelowanych kolumn, na powyższych pairplotach jesteśmy w stanie zaobserowować dużą ilość podziałów na klastry. 

![mean boxplot](plots/mean_box.png)
Nasze dane były poddane już obróbce, bowiem wszystkie wartości znajdują się w przedziale $[-1, 1]$, co potwierdza również poniższa tabela. 

![describe table](plots/describe_table.png)
Ponadto nie musimy się przejmować wartościami odstającymi, ponieważ takowych nie posiadamy.

#### Analiza entropy
![entropy heatmap](plots/entropy_heat.png)
Ponownie obserwujemy bardzo dużo skorelowanych kolumn. 

![entropy pairplot](plots/entropy_pair.png)
Możemy dostrzec powstaniu paru ugrupowań.

![entropy boxplot](plots\entropy_box.png)

#### Wizualizacja danych - tSNE
Na koniec EDY spójrzmy jak nasze dane wyglądają po zastosowaniu funkcji do redukcji wymiarów TSNE.

![TSNE scatter](plots/TSNE.png)
Łatwo dostrzec utworzenie się trzech klastrów, jednak nie możemy bazować na tej metodziej w celu ustalenia optymalnej liczby klastrów.

## 3. Preprocessing danych i feature selection
Nasze dane były już znormalizowane oraz nie zawierały żadnych braków. Nie było wartości odstających oraz danych kategorycznych. Zatem cały etap przetwarzania danych mogliśmy pominąć

#### Wybór cech
Od razu na początku pracy nad projektem usunęliśmy kolumne z informacją o rodzaju aktywności, ponieważ nie była nam potrzebna i mogła tylko przeszkodzić w pracy nad klasteryzacją. Podobny los spotkał kolumne o ID osoby testowej, gdyż nie niosło to żadnej użytecznej informacji.

Przejdźmy teraz do pozostałych kolumn. Jak ustaliliśmy w części EDA, nasze dane zawierają dużo skorelowanych kolumn. Po sprawdzeniu tego okazało się, że ustawiając nasz górny limit korelacji na poziomie 0.98, mogliśmy pozbyć się 214 kolumn właśnie z powodu wysokiego skorelowania z inną kolumną. Tak też zostało uczynione, co doprowadziło do redukcji wymiaru danych do 347 kolumn.

Następnie zastosowaliśmy metodę PCA do redukcji wymiarów.

![PCA plot](plots/PCA.png)
Zastosowaliśmy parametr *n_components = 0.95*, zachowując 95% wariancji. Dało to nam ostateczny wymiar 89 kolumn.

## 4. Modelowanie
#### KMeans
Pierwszym analizowanym modelem jest metoda KMeans. W celu określenia optymalnej liczby klastrów skorzystaliśmy z paru metod. 

![KMeans WCSS](plots/kmeans_wcss.png)
Na wykresie wyżej przedstawione są odległości wewnątrz danej liczby klastrów. Wykorzystując metodę łokcia ustaliliśmy, że najlepszą liczbą będzie dwa.

![KMeans Silhouette Score](plots/kmeans_silhouette.png)
Chcąc uzyskać jak największy Silhouette Score, ponownie ustaliliśmy liczbę klastrów na dwa. 

![KMeans Calinski-Harabasz Score](plots/kmeans_calinski_harabasz.png)
Ponownie chcemy zmaksymalizować Caliniski-Harabasz Score, co jest osiągane przy wartości *n\_clusters = 2*. 

![KMeans Davies-Bouldin Score](plots/kmeans_davies_bouldin.png)
Tym razem minimalizujemy wyniki. Minimalna wartość  *Davies-Bouldin Score* występuje dla dwóch klastrów.

Po uwzględnieniu powyższych wyników przeprowadziliśmy wizualizację danych przy użyciu t-SNE dla metody KMeans, ustawiając parametr *n\_clusters = 2*.

![KMeans - dwa klastry](plots/kmeans_two_clusters.png)
![KMeans barplot](plots/kmeans_bar.png)
Dostaliśmy widoczny podział na dwa w miarę równe klastry. Jednakże na wizualizacji widać, że niektóre punkty z klastra oznaczone kolorem żółtym znajdują się pomiędzy punktami z klastra fioletowego. Mając to na uwadze sprawdziliśmy jeszcze dwie inne metody: *KMedoids* i *GMM*

![KMedoids](plots/kmedoids.png)
*KMedoids*

![GMM](plots/gmm.png)
*GMM*

#### DBSCAN
W celu ustalenia optymalnego epsilona próbowaliśmy znaleźć moment, dla którego odległości zaczną gwałtowanie rosnąć. Ustaliliśmy wartość 3.4

![DBSCAN optimal eps](plots/dbscan_eps.png)

Następnie dla danego epsilona szukaliśmy dobrej wartości dla *min_samples*.

![DBSCAN Silhouette](plots/dbscan_silhouette.png)

![DBSCAN Calinski](plots/dbscan_calinski.png.png)

![DBSCAN Davies](plots/dbscan_davies.png)
Analizując powyższe wykresy, ustawiliśmy wartość na 12.
Po zastosowaniu powyższych parametrów otrzymaliśmy poniższe wyniki:

![DBSCAN Scatter](plots/dbscan_scatter.png)

![DBSCAN](plots/dscan_results.png)

DBSCAN odrzuca aż 1000 wyników, co nie jest dla nas zadowalające. Są one zaznaczone kolorem fioletowym. Jest to na pewno duży problem, gdybyśmy zdecydowali się użyć modelu DBSCAN.

#### Aglomerative Clustering
Przejdźmy do medoty grupowania aglomeracyjnego. Poniżej znajduję się wizualizacja, jak nasz model radzi sobie bez żadnych ustawiania hiperparametrów.
![Aglo Tree](plots/aglo_tree.png)

Ponownie sprawdziliśmy metryki w celu znalezienia optymalnej liczby klastrów.
![Aglo Silhouette](plots/aglo_slihouette.png)

![Aglo Calinski](plots/aglo_calinski.png)

![Aglo Davies](plots/aglo_davies.png)

O to wyniki dla 2 klastrów, które zostały wybrane na podstawie powyższych wykresów.
![Aglo scatter](plots/aglo_scatter.png)

![Aglo Results](plots/aglo_results.png)

## 5. Wyniki
Mając wstępne wyniki modelowania przyszedł czas na wybór najlepiej sprawującego się modelu dla naszego problemu.
![Porownania](plots/comp_silhouette.png)
![Porownania](plots/comp_calinski.png)
![porownania](plots/comp_davies.png)
Powyższe wykresy jednoznacznie wskazują na słabość DBSCANA. Zatem nie będziemy się już jemu dalej przyglądali, ponieważ psuje nam skalę i porównanie pomiędzy innymi modelami.

![porownanie 2](plots/comp_silhouette2.png)
![porownanie 2](plots/comp_calinski2.png)
![porownanie 2](plots/comp_davies2.png)
Widzimy, że najlepiej działa KMeans. Posiada on najwyższe wyniki w metrykach Silhouette'a i Calinskiego-Harabasza oraz najniższy w metryce Daviesa-Bouldina. Zostaje on zatem wybrany jako nasz główny model.
Przyjrzyjmy się na czym nasz model rozróżnia, który element należy do którego klasta.

![diff clusters](plots/diff_clusters.png)

![diff clusters 2](plots/diff_clusters2.png)

Jak można się było spodziewać, model stwierdza na podstawie sygnałów przyśpieszenia oraz zrywu, który klaster pasuje dla danego elementu. Pokrywa się to z wizualizacjami, które pokazują dwa różne klastry. Mogą one reprezentować aktywny oraz bardziej spokojny tryb życia obiektów testowych

#### Sześć klastrów
Wracając do opisu naszej ramki, autorzy badania przydzielili sześć różnych indeksów danym obserwacjom. Sprawdźmy jak KMeans sprawuje się dla właśnie sześciu klastrów.
![6 clusters](plots/comp_6_cl.png)

![6 clusters](plots/6_clusters_scatter.png)
Obserwujemy znaczące spadki w efektywności modelu. Obserwując wyniki danych metryk, możemy stwiedzić osłabienie nowego modelu oraz słabość konceptu sześciu klastrów.
Na wizualizacji widać, że początkowe dwa klastry zostały wewnętrznie jeszcze bardziej podzielone. Może to cieszyć, ponieważ model nadal rozróżnia bardziej i mniej aktywny tryb życia. Możliwe że jest on w stanie rozróżnić inne aktywności, jednakże wyniki metryk nie są zachęcające.

## 6. Wnioski
Wybrany przez nas model KMeans dla dwóch klastrów efektywnie działa na danej ramce danych. Zalicza wysokie wyniki metryk oraz na wizualizacjach otrzymane klastry są łatwo rozróżnialne i dobrze wyszczególnione. 
Sposób rozróżniania obserwacji pokrywa się z intuicją, na podstawie której odzielilibyśmy aktywności na podstawie sygnałów o przyśpieszeniu i zrywie.
Dla sześciu klastrów model nie spełnia naszych oczekiwań wypadając gorzej od naszej domyślnej wersji, pomimo tego że dane przewidują występowanie aż sześciu różnych aktywności obiektów testowych. Nie jest to jednak odzwierciedlone w naszym przypadku.