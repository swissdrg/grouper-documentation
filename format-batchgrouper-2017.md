# SwissDRG Batchgrouper Format 2017

**Stand:** 05. September 2017

Die SwissDRG AG kann ab Abrechnungsversion 7.0 eine Beta-Version eines kombinierten SwissDRG- und Zusatzentgelt-Groupers zur Verfügung stellen. Dieser weist, wie bis anhin, sowohl DRGs und Kostengewichte aus und zusätzlich Zusatzentgelte und deren kumulierte Preise.

Die Erweiterung des Groupers um die Zusatzentgelte bedingt eine Anpassung des Eingabe- und Ausgabeformats. Ab Abrechnungsversion 7.0 verlangt daher der Grouper ein erweitertes Eingabeformat und gibt eine erweiterte Ausgabe aus. Die Spezifikationen der Eingabe und Ausgabeformate werden in diesem Dokument erläutert.

## Allgemein
Dieses Dokument nimmt die aktuelle Grouper-Dokumentation zur Grundlage und erläutert vor allem die Änderungen gegenüber dem bisherigen Batchgrouper-Format, welches unter <https://grouper.swissdrg.org/grouper-doku-de.pdf> beschrieben ist.

## Eingabeformat
Um Zusatzentgelte berechnen zu können, muss das bisherige Batchgrouperformat um Medikationsinformationen ergänzt werden. Das Batchgrouper Format 2017 zeichnet sich weiterhin dadurch aus, dass Diagnosen und Prozeduren als Listen von flexibler Länge codiert werden, und nicht mehr in einer fixen Anzahl Spalten.

Dies führt dazu, dass pro Zeile mehrere verschiedene Trennzeichen mit unterschiedlicher Bedeutung benutzt werden:

<center>

|Trenner|Beschreibung|Benutzt als|
|---|---|---|
|``;``|Semikolon|Top-level Spaltentrenner|
|<code>&#124;</code>|Pipe-Symbol|Listentrenner|
|``:``|Doppelpunkt|Strukturtrenner|
[Tabelle 1: Bedeutung der Trennzeichen]

</center>

Der Listentrenner wird dafür benutzt, Elemente einer Liste (z.B. Liste der Diagnosesn, Liste der Prozeduren) voneinander abzutrennen. Per Strukturtrenner werden einzelne Felder einer sogenannten Struktur getrennt. Eine Struktur ist zum Beispiel ein Prozedur-Eintrag in der Prozedurenliste, bestehend aus CHOP Code, Seitigkeit und Datum.

#### Allgemeines Format
Das Batchgrouper Format 2017 codiert einen minimalen gruppierbaren Datensatz in 14 Spalten, die durch Semikola voneinander getrennt sind. Das Zeilenformat ist wie folgt definiert:

```text
key;age;age_days;birth_weight;sex;adm_date;adm_mode;exit_date;exit_mode;los;
	resp_hours;diagnoses[icd_code];procedures[chop_code:side:date];
	medications[atc_code:annex:application:dose:unit]
```

##### Erläuterung der Variablen
<center>

|Name|Beschreibung|Datentyp|BFS MS Format|
|---|---|---|---|
|`key`|Fallschlüssel (Primary Key)|Text|-|
|`age`|Alter in Jahren|Ganzzahl|`1.1.V03`|
|`age_days`|Alter in Tagen|Ganzzahl|[siehe Grouper-Doku](https://grouper.swissdrg.org/grouper-doku-de.pdf)|
|`birth_weight`|Aufnahmegewicht [Gramm]|Ganzzahl|`2.2.V04` bzw. `4.5.V01`|
|`sex`|Geschlecht|Text: `M`,`W`</br>|`1.1.V01`</br>(`1` $\rightarrow$ `M`, `2` $\rightarrow$ `W`)|
|`adm_date`|Eintrittsdatum|Text (`YYYYMMDD`)|`1.2.V01`|
|`adm_mode`|Aufnahmeart|Text|[siehe Grouper-Doku](https://grouper.swissdrg.org/grouper-doku-de.pdf)|
|`exit_date`|Austrittsdatum|Text (`YYYYMMDD`)|`1.5.V01`|
|`exit_mode`|Entlassart|Text|[siehe Grouper-Doku](https://grouper.swissdrg.org/grouper-doku-de.pdf)|
|`los`|Verweildauer in Tagen|Ganzzahl|[siehe Grouper-Doku](https://grouper.swissdrg.org/grouper-doku-de.pdf)|
|`resp_hours`|Beatmungsdauer in Stunden|Ganzzahl|`4.4.V01`|
|`diagnoses`|Diagnosen|Text (Liste)|Variablen `4.2`|
|`procedures`|Prozeduren|Text (Liste)|Variablen `4.3`|
|`medications`|Medikationen|Text (Liste)|Variablen `4.8.V02`-`4.8.V15`|
[Tabelle 2: Variablen und deren Entsprechung in der Medizinischen Statistik]

</center>

**Wichtig:** Bitte beachten Sie die Ausführungen in der [aktuellen Grouper-Dokumentation](https://grouper.swissdrg.org/grouper-doku-de.pdf) zu den genauen Umrechnungen der einzelnen Variablen aus der Medizinischen Statistik.

##### Beispiel
```text
1234;24;;;M;20160501;01;20160511;00;10;0;I269|E1190|E780;
  992502::|874199::|992813::;L01XC07::IV:400.0:mg|B02BD02:Plas:IV:20000:U
```

#### Diagnosen
Diagnosen werden im neuen Format als Liste in der Spalte `diagnoses` codiert, anstatt in eine vordefinierte Anzahl Spalten eingefügt zu werden. Die **Hauptdiagnose** (4.2.V010) wird immer zuerst kodiert. Anschliessend werden alle Nebendiagnosen angefügt. Die Reihenfolge der Nebendiagnosen ist nicht gruppierungsrelevant. Der MD-Zusatz zur Hauptdiagnose (4.2.V020) wird als normale Nebendiagnose behandelt.

##### Beispiele
* `...;I269;...` (nur Hauptdiagnose)
* `...;I269|E1190;...` (Hauptdiagnose mit einer Nebendiagnose)

#### Prozeduren
Ähnlich wie Diagnosen werden auch die Prozeduren als Liste in der Spalte `procedures` codiert. Dabei werden die einzelnen Prozeduren als sogenannte Struktur codiert. Das Format der Struktur ist `chop_chode:side:date`. Wie im bisherigen Format können damit sowohl die Seitigkeit als auch das Datum der Prozedur optional codiert werden. Die CHOP Kodes werden immer ohne Punkt kodiert. Wenn die Seitigkeit unbekannt ist oder sich die Frage nicht stellt, kann das Feld leer gelassen werden. Hat ein Fall keine Prozeduren, wird die daher leere Liste als zwei aufeinanderfolgende Semikola geschrieben:

* Keine Prozeduren: `...;;...`
* Mehrere Prozeduren: `...;992502::|874199::|992813::;...`

Folgende Codierungen für eine einzelne Prozedur sind gültig:

* Nur CHOP Code: `5423::`
* CHOP Code und Seitigkeit: `5423:B:`
* CHOP Code, Seitigkeit und Datum: `5423:B:20090325` oder `5423::20090325`

#### Medikationen (Verabreichung von Medikamenten)
Im SwissDRG Batchgrouper 2017 Format werden zusätzlich zu Diagnosen und Prozeduren auch Medikationen codiert. Dies ist erforderlich, damit der Grouper Zusatzentgelte berechnen kann. Medikationen werden analog zu den Prozeduren als Liste von Strukturen kodiert. Das Format einer Medikation ist `atc_code:annex:application:dose:unit`.

<center>

|Feld|Bezeichnung|Datentyp|
|---|---|---|
|`atc_code`|7-stelliger ATC Code|Text|
|`annex`|Zusatzangaben|Text oder leer|
|`application`|Verabreichungsart|Text|
|`dose`|Verabreichte Dosis|Dezimalzahl</br>(Punkt als Dezimaltrennzeichen)|
|`unit`|Einheit|Text|
[Tabelle 3: Struktur einer Verabreichung]

</center>

**Achtung:** Die Felder `application` und `annex` dürfen nicht beliebig befüllt werden. Bitte konsultieren Sie das [Technische Begleitblatt zur Erhebung der Medizinischen Statistik](http://www.swissdrg.org/assets/pdf/Erhebung_2017/161216_Technisches_Begleitblatt_2017_d.pdf), Punkt 3.1b und 3.1c.


##### Beispiele
* `...;L01XC07::IV:450.0:mg|B02BD02:Plas:IV:1500:U` (2 Medikamente, das erste ohne Zusatzangabe)
* `...;L04AA04:CFR:IV:200:mg|J02AC04:Susp:O:500:mg`

## Ausgabeformat
Das bisherige Ausgabeformat für die DRG-Gruppierung wird beibehalten. Für die berechneten Zusatzentgelte erstellt der Grouper eine zusätzliche Ausgabedatei.

### DRG-Gruppierung
Die Grouperausgabe für einen SwissDRG-gruppierten Fall sieht folgendermassen aus:

```
ID;DRG;MDC;GAGE;GSEX;GST;PCCL;ECW;CFLAG
```

Die Kürzel sind im Folgenden kurz beschrieben:
<center>

|Spalte|Beschreibung|
|---|---|
|`ID`|Fallschlüssel (Primary Key)|
|`DRG`|DRG Kürzel|
|`MDC`|MDC Kürzel|
|`GAGE`|Alter für die Gruppierung verwendet (0-3)|
|`GSEX`|Geschlecht für die Gruppierung verwendet (0-3)|
|`GST`|Grouperstatus (00-09)|
|`PCCL`|Patientenbezogener Schweregrad (0 bis 4)|
|`ECW`|Effektives Kostengewicht|
|`CFLAG`|Flag für die Berechnung des effektiven Kostengewichtes|

</center>

Für eine genauere Beschreibung der Variablen im Ausgabeformat verweisen wir auch hier auf die [aktuelle Grouper-Dokumentation](https://grouper.swissdrg.org/grouper-doku-de.pdf).

### Zusatzentgelte
Das Zeilenformat für die Ausgabedatei der Zusatzentgelte (ZE) ist wie folgt:

```
ID;TOTAL_AMOUNT;SUPPLEMENTS
```

Die Kürzel sind in der nachfolgenden Tabelle kurz erläutert:

<center>

|Spalte|Beschreibung|
|---|---|
|`ID`|Fallschlüssel (Primary Key)|
|`TOTAL_AMOUNT`|aufsummierter Gesamtbetrag aller berechneten Zusatzentgelte|
|`SUPPLEMENTS`|Liste von ZE-Strukturen|

</center>

Eine einzelne Zusatzentgelt-Struktur ist folgendermassen kodiert: `ze_id:ze_code:ze_amount`.

<center>

|Feld|Beschreibung|
|---|---|
|`ze_id`|Bezeichner des ZE gemäss Fallpauschalenkatalog|
|`ze_code`|Auslösender Code (CHOP oder ATC Code)|
|`ze_amount`|Betrag des ZE gemäss Fallpauschalenkatalog|

</center>

#### Beispiel
```
...
1234;12257.05;ZE-2018-02.02:3995C2:2502.95|
	ZE-2018-21.03:990512:8056.15|ZE-2018-52.06:J06BA02_IV_nr:1697.95
...
```

## Kontakt
Für Rückmeldungen und Anregungen sind wir erreichbar unter:

Lukas Nick </br>
SwissDRG AG, IT Abteilung </br>
[lukas.nick@swissdrg.org](mailto:lukas.nick@swissdrg.org)



