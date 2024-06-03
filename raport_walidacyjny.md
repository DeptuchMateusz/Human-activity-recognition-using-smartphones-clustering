# Raport walidacyjny 
## grupy tworzącej projekt "Soccer Players Clustering", czyli Anny Ostrowskiej i Michała Iwaniuka
### Paweł Florek, Mateusz Deptuch

---

## 1. KM1 - EDA
**rady od zespołu walidatrów:**
+ Mógłby się pojawić ogólny opis ramki danych oraz co zawiera. Niekoniecznie wszystkie 127 kolumn, ale kilka zdań z czym macie doczynienia oraz wykresy przedstawiające dane. Będzie łatwiej zrozumieć innym.
> wzięte po uwagę  
+ Brak sprawdzenia czy nie ma outlierów, czy przy pomocy boxplota lub scattera.  
> wzięte po uwagę  
+ Brak biznes planu lub czemu by mogła służyć klasteryzacja.  
> wzięte po uwagę  
+ Mogliście spróbować użyć np. TSNE i pokazać dane oraz czy sprawdzić czy nie widać już jakichś ugropowań  
> wzięte po uwagę w kolejnym etapie  
+ raczej nie powinno się dzielić danych na "train" i "val"  
> wzięte po uwagę  

## 2. KM2
**rady od zespołu walidatrów:**

+ 0.7 to dość niski threshold przy usuwaniu skorelowanych kolumn
> nie wzięte pod uwagę z powodu dużej liczby kolumn
+ czy usunięcie nazwy drużyny to na pewno dobry pomysł?
> rozważone ale nie wzięte pod uwagę
+ wyniki klasteryzacji na danych walidacyjnych podobne, dobra robota


## 3. KM3