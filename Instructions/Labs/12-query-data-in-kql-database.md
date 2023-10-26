---
lab:
  title: Abfragen von Daten aus einer KQL-Datenbank
  module: Query data from a Kusto Query database in Microsoft Fabric
---
# Erste Schritte beim Abfragen einer Kusto-Datenbank in Microsoft Fabric
Ein KQL-Abfrageset ist ein Tool, mit dem Sie Abfragen ausführen, ändern und Abfrageergebnisse aus einer KQL-Datenbank anzeigen können. Sie können jede Registerkarte im KQL-Abfrageset mit einer anderen KQL-Datenbank verknüpfen und Ihre Abfragen für die zukünftige Verwendung speichern oder für die Datenanalyse freigeben. Sie können die KQL-Datenbank auch für eine beliebige Registerkarte wechseln, sodass Sie die Abfrageergebnisse aus verschiedenen Datenquellen vergleichen können.

Das KQL-Abfrageset verwendet die Kusto-Abfragesprache, die mit vielen SQL-Funktionen kompatibel ist, um Abfragen zu erstellen. Um mehr über die [Kusto-Abfragesprache (Kusto Query Language, KQL)](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext) zu erfahren, 

Dieses Lab dauert ungefähr **25** Minuten.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Melden Sie sich bei [Microsoft Fabric](https://app.fabric.microsoft.com) unter `https://app.fabric.microsoft.com` an, und wählen Sie **Power BI** aus.
2. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
3. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
4. Beim Öffnen Ihres neuen Arbeitsbereichs sollte dieser wie im Folgenden gezeigt leer sein:

    ![Screenshot: Leerer Arbeitsbereich in Power BI](./Images/new-workspace.png)

In diesem Lab verwenden Sie Real-Time Analytics (RTA) in Fabric, um eine KQL-Datenbank anhand eines Beispiel-Ereignisstream zu erstellen. Real-Time Analytics bietet ein Beispieldataset, mit dem Sie die Funktionen von RTA erkunden können. Sie verwenden diese Beispieldaten, um die Kusto-Abfragesprache | SQL-Abfragen und -Abfragesets zu erstellen, die Echtzeitdaten analysieren und eine zusätzliche Verwendung in nachgelagerten Prozessen ermöglichen.

## Erstellen einer KQL-Datenbank

1. Wählen Sie unter **Echtzeitanalyse** das Feld **KQL-Datenbank** aus.

   ![Abbildung der Auswahl der kqldatabase](./Images/select-kqldatabase.png)

2. Sie werden aufgefordert, einen **Namen** für die KQL-Datenbank festzulegen.

   ![Abbildung des Namens der kqldatabase](./Images/name-kqldatabase.png)

3. Geben Sie der KQL-Datenbank einen Namen, den Sie sich gut merken können (z. B. **MyStockData**), und klicken Sie auf **Erstellen**.

4. Wählen Sie im Bereich **Datenbankdetails** das Bleistiftsymbol aus, um die Verfügbarkeit in OneLake zu aktivieren.

   ![Abbildung der Aktivierung von OneLake](./Images/enable-onelake-availability.png)

5. Wählen Sie das Feld mit **Beispieldaten** aus den Optionen von ***Rufen Sie zunächst Daten ab*** aus.
 
   ![Abbildung der Auswahloptionen mit hervorgehobenen Beispieldaten](./Images/load-sample-data.png)

6. Wählen Sie das Feld **Metrikanalysen für Automobilbranche** aus den Optionen für Beispieldaten aus.

   ![Abbildung der Auswahl von Analysedaten für das Lab](./Images/create-sample-data.png)

7. Nachdem die Daten geladen wurden, können wir überprüfen, ob die KQL-Datenbank aufgefüllt ist.

   ![Daten werden in die KQL-Datenbank geladen](./Images/choose-automotive-operations-analytics.png)

7. Sobald die Daten geladen wurden, überprüfen Sie, ob die Daten in die KQL-Datenbank geladen wurden. Wählen Sie dazu die Auslassungspunkte rechts neben der Tabelle aus, navigieren Sie zur **Abfragetabelle**, und wählen Sie **Alle 100 Datensätze anzeigen** aus.

    ![Abbildung der Auswahl der obersten 100 Dateien aus der Tabelle RawServerMetrics](./Images/rawservermetrics-top-100.png)

   > **HINWEIS**: Wenn Sie dies zum ersten Mal ausführen, kann die Zuordnung von Computeressourcen mehrere Sekunden dauern.

    ![Abbildung der 100 Datensätze aus den Daten](./Images/explore-with-kql-take-100.png)


## Szenario
In diesem Szenario sind Sie ein Analyst, der mit der Abfrage eines Beispieldatensatzes roher Metriken zu NYC-Taxifahrten beauftragt ist, aus dem Sie zusammenfassende Statistiken (Profiling) der Daten aus der Fabric-Umgebung abrufen werden. Sie verwenden KQL, um diese Daten abzufragen und Informationen zu sammeln, um Informationen zu den Daten zu erhalten.

## Einführung in Kusto-Abfragesprache (Kusto Query Language, KQL) und deren Syntax

Kusto-Abfragesprache (KQL) ist eine Abfragesprache, die zum Analysieren von Daten im Microsoft Azure Data Explorer verwendet wird, der Teil von Azure Fabric ist. KQL ist einfach und intuitiv konzipiert, sodass Anfänger sie leicht lernen und verwenden können. Gleichzeitig ist sie auch sehr flexibel und anpassbar, sodass fortgeschrittene Benutzer komplexe Abfragen und Analysen durchführen können.

KQL basiert auf einer Syntax ähnlich wie SQL, aber mit einigen wichtigen Unterschieden. KQL verwendet beispielsweise einen Pipeoperator (|) anstelle eines Semikolons (;) zum Trennen von Befehlen und verwendet einen anderen Satz von Funktionen und Operatoren zum Filtern und Bearbeiten von Daten.

Eines der wichtigsten Features von KQL ist die Fähigkeit, große Datenmengen schnell und effizient zu verarbeiten. Dies macht sie ideal für die Analyse von Protokollen, Telemetriedaten und anderen Arten von Big Data. KQL unterstützt auch eine Vielzahl von Datenquellen, einschließlich strukturierter und unstrukturierter Daten, was sie zu einem vielseitigen Tool für die Datenanalyse macht.

Im Kontext von Microsoft Fabric kann KQL zum Abfragen und Analysieren von Daten aus verschiedenen Quellen wie Anwendungsprotokollen, Leistungsmetriken und Systemereignissen verwendet werden. Dies kann Ihnen helfen, Einblicke in die Integrität und Leistung Ihrer Anwendungen und Infrastruktur zu erhalten und Probleme und Optimierungsmöglichkeiten zu identifizieren.

Insgesamt ist KQL eine leistungsstarke und flexible Abfragesprache, mit der Sie schnell und einfach Einblicke in Ihre Daten gewinnen können, unabhängig davon, ob Sie mit Microsoft Fabric oder anderen Datenquellen arbeiten. Mit seiner intuitiven Syntax und leistungsstarken Funktionen ist KQL auf jeden Fall eine nähere Erkundung wert.

In diesem Modul konzentrieren wir uns auf die Grundlagen der Abfragen an die KQL-Datenbank Sie werden schnell sehen, dass es in KQL kein ```SELECT``` gibt. Wir können einfach den Tabellennamen verwenden und „Ausführen“ drücken. Wir behandeln zuerst die Schritte einer einfachen Analyse mit KQL und dann SQL für dieselbe KQL-Datenbank, die auf Azure Data Explorer basiert.

**SELECT**-Abfragen, die zum Abrufen von Daten aus einer oder mehreren Tabellen verwendet werden. Beispielsweise können Sie eine SELECT-Abfrage verwenden, um die Namen und Gehälter aller Mitarbeiter in einem Unternehmen abzurufen.

**WHERE**-Abfragen, die verwendet werden, um die Daten basierend auf bestimmten Bedingungen zu filtern. Beispielsweise können Sie eine WHERE-Abfrage verwenden, um die Namen von Mitarbeitern abzurufen, die in einer bestimmten Abteilung arbeiten oder ein Gehalt haben, das über einem bestimmten Betrag liegt.

**GROUP BY**-Abfragen, die verwendet werden, um die Daten anhand einer oder mehrerer Spalten zu gruppieren und Aggregatfunktionen für sie auszuführen. Sie können beispielsweise eine GROUP BY-Abfrage verwenden, um das durchschnittliche Gehalt der Mitarbeiter nach Abteilung oder Land zu erhalten.

**ORDER BY**-Abfragen, die verwendet werden, um die Daten nach einer oder mehreren Spalten in aufsteigender oder absteigender Reihenfolge zu sortieren. Beispielsweise können Sie eine ORDER BY-Abfrage verwenden, um die Namen der Mitarbeiter nach ihren Gehältern oder nach ihren Nachnamen sortiert abzurufen.

   > **ACHTUNG:** Sie können keine Power BI-Berichte aus Abfragesets mit **T-SQL** erstellen, da Power BI T-SQL nicht als Datenquelle unterstützt. **Power BI unterstützt nur KQL als native Abfragesprache für Abfragesets**. Wenn Sie T-SQL zum Abfragen Ihrer Daten in Microsoft Fabric verwenden möchten, müssen Sie den T-SQL-Endpunkt verwenden, der Microsoft SQL Server emuliert und Ihnen das Ausführen von T-SQL-Abfragen an Ihren Daten ermöglicht. Der T-SQL-Endpunkt weist jedoch einige Einschränkungen und Unterschiede zum nativen SQL Server auf und unterstützt das Erstellen oder Veröffentlichen von Berichten in Power BI nicht.

## ```SELECT``` Daten aus unserem Beispieldataset mithilfe von KQL

1. In dieser Abfrage werden 100 Datensätze aus der Tabelle „Trips“ (Fahrten) abgerufen. Wir verwenden das Schlüsselwort ```take```, um das Modul aufzufordern, 100 Datensätze zurückzugeben.

```kql
Trips
| take 100
```
  > **HINWEIS:** Das Pipe-Zeichen (```|```) wird für zwei Zwecke in KQL verwendet, einschließlich der Trennung von Abfrageoperatoren in einer tabellarischen Ausdrucksanweisung. Er wird auch als logischer OR-Operator in eckigen oder runden Klammern verwendet, um anzugeben, dass Sie eines der durch das Pipe-Zeichen getrennten Elemente angeben können. 
    
2. Wir können präziser sein, indem wir einfach bestimmte Attribute hinzufügen, die wir mithilfe des ```project```-Schlüsselworts abfragen möchten, und dann das ```take```-Schlüsselwort verwenden, um dem Modul mitzuteilen, wie viele Datensätze zurückgegeben werden sollen.

> **HINWEIS:** Die Verwendung von ```//``` kennzeichnet Kommentare, die im Microsoft Fabric-Abfragetool ***Untersuchen Ihrer Daten*** verwendet werden.

```
// Use 'project' and 'take' to view a sample number of records in the table and check the data.
Trips 
| project vendor_id, trip_distance
| take 10
```

3. Eine weitere gängige Methode in der Analyse ist das Umbenennen von Spalten in unserem Abfrageset, um sie benutzerfreundlicher zu gestalten. Dies kann erreicht werden, indem Sie den neuen Spaltennamen gefolgt von dem Gleichheitszeichen und der Spalte verwenden, die wir umbenennen möchten.

```
Trips 
| project vendor_id, ["Trip Distance"] = trip_distance
| take 10
```

4. Eventuell möchten wir auch die Fahrten zusammenfassen, um zu sehen, wie viele Meilen zurückgelegt wurden:

```
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance)
```
## ```GROUP BY```-Daten aus unserem Beispieldataset mithilfe von KQL

1. Dann möchten wir eventuell nach dem Abholort ***gruppieren***, was wir mit dem ```summarize```-Operator tun. Wir können auch den ```project```-Operator verwenden, der es uns ermöglicht, die Spalten auszuwählen und umzubenennen, die Sie in Ihre Ausgabe aufnehmen möchten. In diesem Fall gruppieren wir nach Bezirk innerhalb des NY Taxi-Systems, um unseren Benutzern die von den einzelnen Bezirken zurückgelegte Gesamtdistanz bereitzustellen.

```
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = pickup_boroname, ["Total Trip Distance"]
```

2. Sie werden feststellen, dass wir einen leeren Wert haben, was für die Analyse nie gut ist, und wir können die ```case```-Funktion zusammen mit den Funktionen ```isempty``` und ```isnull``` verwenden, um diese in unseren 
```
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
```

## ```ORDER BY```-Daten aus unserem Beispieldataset mithilfe von KQL zu kategorisieren

1. Um unsere Daten sinnvoller zu machen, sortieren wir sie in der Regel nach einer Spalte, und dies geschieht in KQL entweder mit einem ```sort by```- oder ```order by```-Operator, und sie beide agieren auf die gleiche Weise.
 
```
// using the sort by operators
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 

// order by operator has the same result as sort by
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 
```

## ```WHERE``` -Klausel zum Filtern von Daten in unserer KQL-Beispielabfrage

1. Im Gegensatz zu SQL wird die WHERE-Klausel sofort in unserer KQL-Abfrage aufgerufen. Wir können weiterhin sowohl die ```and``` als auch die ```or``` logischen Operatoren in Ihrer WHERE-Klausel verwenden. Dies wird für die Tabelle zu true oder false ausgewertet und kann ein einfacher oder komplexer Ausdruck sein, der mehrere Spalten, Operatoren und Funktionen umfassen kann.

```
// let's filter our dataset immediately from the source by applying a filter directly after the table.
Trips
| where pickup_boroname == "Manhattan"
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc

```

## Verwenden von T-SQL zum Abfragen von Zusammenfassungsinformationen

KQL-Datenbank unterstützt T-SQL nicht nativ, bietet jedoch einen T-SQL-Endpunkt, der Microsoft SQL Server emuliert und das Ausführen von T-SQL-Abfragen an Ihren Daten ermöglicht. Der T-SQL-Endpunkt weist jedoch einige Einschränkungen und Unterschiede zum nativen SQL Server auf. Beispielsweise wird das Erstellen, Ändern oder Verwerfen von Tabellen oder das Einfügen, Aktualisieren oder Löschen von Daten nicht unterstützt. Außerdem werden einige T-SQL-Funktionen und -Syntax, die nicht mit KQL kompatibel sind, nicht unterstützt. Es wurde erstellt, um Systemen, die KQL nicht unterstützt haben, die Verwendung von T-SQL zum Abfragen der Daten in einer KQL-Datenbank zu ermöglichen. Daher wird empfohlen, KQL als primäre Abfragesprache für KQL-Datenbanken zu verwenden, da es mehr Funktionen und Leistung als T-SQL bietet. Sie können auch einige SQL-Funktionen verwenden, die von KQL unterstützt werden, z. B. count, sum, avg, min, max usw. 

## ```SELECT```-Daten aus unserem Beispieldataset mithilfe von T-SQL
1.

```
SELECT * FROM Trips

// We can also use the TOP keyword to limit the number of records returned

SELECT TOP 10 * from Trips
```

2. Wenn Sie den ```//``` verwenden, wobei es sich um einen Kommentar im Tool ***Untersuchen Ihrer Daten** innerhalb der KQL-Datenbank handelt, können Sie ihn beim Ausführen von T-SQL-Abfragen nicht hervorheben. Stattdessen sollten Sie die standardmäßige ```--```-SQL-Kommentarnotation verwenden. Dadurch wird auch die KQL-Engine angewiesen, T-SQL in Azure Data Explorer zu erwarten.

```
-- instead of using the 'project' and 'take' keywords we simply use a standard SQL Query
SELECT TOP 10 vendor_id, trip_distance
FROM Trips
```

3. Auch hier sehen Sie, dass T-SQL-Standardfeatures mit der Abfrage einwandfrei funktionieren, bei der wir trip_distance in einen benutzerfreundlicheren Namen umbenennen.

-- Keine Notwendigkeit, die Operatoren „project“ oder „take“ als Standard T-SQL Works SELECT TOP 10 vendor_id, trip_distance als [Trip Distance] von Trips zu verwenden

## Bereinigen von Ressourcen

In dieser Übung haben Sie eine KQL-Datenbank erstellt und ein Beispieldataset für Abfragen eingerichtet. Danach haben Sie die Daten mit KQL und SQL abgefragt. Wenn Sie die Untersuchung Ihrer KQL-Datenbank abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.
1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus.
2. Wählen Sie im Menü „...“ auf der Symbolleiste die Option „Arbeitsbereichseinstellungen“ aus.
3. Wählen Sie im Abschnitt „Andere“ die Option „Diesen Arbeitsbereich entfernen“ aus.