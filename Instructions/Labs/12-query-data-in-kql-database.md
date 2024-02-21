---
lab:
  title: Abfragen von Daten aus einer KQL-Datenbank
  module: Query data from a Kusto Query database in Microsoft Fabric
---

# Erste Schritte beim Abfragen einer Kusto-Datenbank in Microsoft Fabric

Ein KQL-Abfrageset ist ein Tool, mit dem Sie Abfragen ausführen, ändern und Abfrageergebnisse aus einer KQL-Datenbank anzeigen können. Sie können jede Registerkarte im KQL-Abfrageset mit einer anderen KQL-Datenbank verknüpfen und Ihre Abfragen für die zukünftige Verwendung speichern oder für die Datenanalyse freigeben. Sie können die KQL-Datenbank auch für eine beliebige Registerkarte wechseln, sodass Sie die Abfrageergebnisse aus verschiedenen Datenquellen vergleichen können.

Das KQL-Abfrageset verwendet die Kusto-Abfragesprache, die mit vielen SQL-Funktionen kompatibel ist, um Abfragen zu erstellen. Weitere Informationen zur [Kusto-Abfragesprache (KQL)](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext).

Sie werden ungefähr **25** Minuten für dieses Lab benötigen.

## Szenario

In diesem Szenario sind Sie ein*e Analyst*in, der/die damit beauftragt ist, ein Beispieldataset mit Rohdaten zu Taxifahrten in New York abzufragen, aus dem Sie zusammenfassende Statistiken (Profilerstellung) der Daten aus der Fabric-Umgebung ziehen. Sie verwenden KQL, um diese Daten abzufragen und Informationen zu sammeln, um Informationen zu den Daten zu erhalten.

> **Hinweis:** Für diese Übung benötigen Sie ein *Geschäfts-*, *Schul- oder Unikonto* von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Wählen Sie auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com) die Option **Echtzeitanalyse** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

In diesem Lab verwenden Sie Echtzeitanalysen (RTA) in Fabric, um eine KQL-Datenbank anhand eines Beispiel-Eventstreams zu erstellen. Im Bereich „Echtzeitanalyse“ ist ein praktisches Beispieldataset vorhanden, mit dem Sie die Funktionen der Echtzeitanalyse kennenlernen können. Sie verwenden diese Beispieldaten, um KQL/SQL-Abfragen und Abfragesätze zu erstellen, die Echtzeitdaten analysieren und andere Verwendungen in nachgelagerten Prozessen ermöglichen.

## Erstellen einer KQL-Datenbank

1. Wählen Sie unter **Echtzeitanalyse** das Feld **KQL-Datenbank** aus.

   ![Abbildung der Auswahl der KQL-Datenbank](./Images/select-kqldatabase.png)

1. Sie werden aufgefordert, einen **Namen** für die KQL-Datenbank anzugeben.

   ![Abbildung des Namens der KQL-Datenbank](./Images/name-kqldatabase.png)

1. Geben Sie der KQL-Datenbank einen Namen, den Sie sich gut merken können (z. B. **MyStockData**), und klicken Sie auf **Erstellen**.

1. Wählen Sie im Bereich **Datenbankdetails** das Bleistiftsymbol aus, um die Verfügbarkeit in OneLake zu aktivieren.

   ![Abbildung der Aktivierung von OneLake](./Images/enable-onelake-availability.png)

   Verwenden Sie dann den Schieberegler, um die Verfügbarkeit zu aktivieren.

   ![Abbildung: Auswahl des Schiebereglers in Data Lake](./Images/data-availability-data-lake.png)
   
1. Wählen Sie das Feld mit **Beispieldaten** aus den Optionen von ***Rufen Sie zunächst Daten ab*** aus.

   ![Abbildung der Auswahloptionen mit hervorgehobenen Beispieldaten](./Images/load-sample-data.png)

   Wählen Sie das dann Feld **Automotive operations analytics** aus den Optionen für Beispieldaten aus.

   ![Abbildung der Auswahl von Analysedaten für das Lab](./Images/create-sample-data.png)

1. Nachdem die Daten geladen wurden, können wir überprüfen, ob die KQL-Datenbank mit Daten gefüllt ist.

   ![Daten werden in die KQL-Datenbank geladen](./Images/choose-automotive-operations-analytics.png)

1. Sobald die Daten geladen wurden, überprüfen Sie, ob die Daten in die KQL-Datenbank geladen wurden. Wählen Sie dazu die Auslassungspunkte rechts neben der Tabelle aus, navigieren Sie zur **Abfragetabelle** und wählen Sie **100 Datensätze anzeigen** aus.

    ![Abbildung der Auswahl der obersten 100 Dateien aus der Tabelle RawServerMetrics](./Images/rawservermetrics-top-100.png)

   > **HINWEIS:** Wenn Sie dies zum ersten Mal ausführen, kann die Zuordnung von Computeressourcen mehrere Sekunden dauern.

    ![Abbildung der 100 Datensätze aus den Daten](./Images/explore-with-kql-take-100.png)

## Einführung in Kusto-Abfragesprache (Kusto Query Language, KQL) und deren Syntax

Kusto-Abfragesprache (KQL) ist eine Abfragesprache, die zum Analysieren von Daten im Microsoft Azure Data Explorer verwendet wird, der Teil von Azure Fabric ist. KQL ist einfach und intuitiv konzipiert, sodass Anfänger sie leicht lernen und verwenden können. Gleichzeitig ist es sehr flexibel und anpassbar, so dass fortgeschrittene Benutzer*innen komplexe Abfragen und Analysen durchführen können.

KQL basiert auf einer Syntax ähnlich wie SQL, aber mit einigen wichtigen Unterschieden. KQL verwendet beispielsweise einen Pipeoperator (|) anstelle eines Semikolons (;) zum Trennen von Befehlen und verwendet einen anderen Satz von Funktionen und Operatoren zum Filtern und Bearbeiten von Daten.

Eines der wichtigsten Features von KQL ist die Fähigkeit, große Datenmengen schnell und effizient zu verarbeiten. Durch diese Funktion ist es ideal für die Analyse von Protokollen, Telemetriedaten und anderen Arten von Big Data. KQL unterstützt auch eine Vielzahl von Datenquellen, einschließlich strukturierter und unstrukturierter Daten, was sie zu einem vielseitigen Tool für die Datenanalyse macht.

Im Kontext von Microsoft Fabric kann KQL zum Abfragen und Analysieren von Daten aus verschiedenen Quellen wie Anwendungsprotokollen, Leistungsmetriken und Systemereignissen verwendet werden. Dies kann Ihnen helfen, Einblicke in die Integrität und Leistung Ihrer Anwendungen und Infrastruktur zu erhalten und Probleme und Optimierungsmöglichkeiten zu identifizieren.

Insgesamt ist KQL eine leistungsstarke und flexible Abfragesprache, mit der Sie schnell und einfach Einblicke in Ihre Daten gewinnen können, unabhängig davon, ob Sie mit Microsoft Fabric oder anderen Datenquellen arbeiten. Mit seiner intuitiven Syntax und leistungsstarken Funktionen ist KQL auf jeden Fall eine nähere Erkundung wert.

In diesem Modul konzentrieren wir uns auf die Grundlagen von Abfragen gegen eine KQL-Datenbank, wobei wir zuerst KQL und dann T-SQL verwenden. Wir werden uns auf die grundlegenden Elemente der T-SQL-Syntax konzentrieren, die für Abfragen verwendet werden:

**SELECT**-Abfragen, die zum Abrufen von Daten aus einer oder mehreren Tabellen verwendet werden. Beispielsweise können Sie eine SELECT-Abfrage verwenden, um die Namen und Gehälter aller Mitarbeiter in einem Unternehmen abzurufen.

**WHERE**-Abfragen, die verwendet werden, um die Daten basierend auf bestimmten Bedingungen zu filtern. Beispielsweise können Sie eine WHERE-Abfrage verwenden, um die Namen von Mitarbeitern abzurufen, die in einer bestimmten Abteilung arbeiten oder ein Gehalt haben, das über einem bestimmten Betrag liegt.

**GROUP BY**-Abfragen, die verwendet werden, um die Daten anhand einer oder mehrerer Spalten zu gruppieren und Aggregatfunktionen für sie auszuführen. Sie können beispielsweise eine GROUP BY-Abfrage verwenden, um das durchschnittliche Gehalt der Mitarbeiter nach Abteilung oder Land zu erhalten.

**ORDER BY**-Abfragen, die verwendet werden, um die Daten nach einer oder mehreren Spalten in aufsteigender oder absteigender Reihenfolge zu sortieren. Beispielsweise können Sie eine ORDER BY-Abfrage verwenden, um die Namen der Mitarbeiter nach ihren Gehältern oder nach ihren Nachnamen sortiert abzurufen.

   > **ACHTUNG:** Sie können keine Power BI-Berichte aus Abfragesets mit **T-SQL** erstellen, da Power BI T-SQL nicht als Datenquelle unterstützt. **Power BI unterstützt nur KQL als native Abfragesprache für Abfragesets**. Wenn Sie T-SQL zum Abfragen Ihrer Daten in Microsoft Fabric verwenden möchten, müssen Sie den T-SQL-Endpunkt verwenden, der Microsoft SQL Server emuliert und Ihnen das Ausführen von T-SQL-Abfragen an Ihren Daten ermöglicht. Der T-SQL-Endpunkt weist jedoch einige Einschränkungen und Unterschiede zum nativen SQL Server auf und unterstützt das Erstellen oder Veröffentlichen von Berichten in Power BI nicht.

> **HINWEIS**: Neben der oben gezeigten Methode, ein Abfragefenster aufzurufen, können Sie im Hauptfenster der KQL-Datenbank auch auf die Schaltfläche **Daten durchsuchen** klicken.

   ![Abbildung der Schaltfläche „Daten durchsuchen“](./Images/explore-your-data.png)

## ```SELECT``` Daten aus unserem Beispieldataset mithilfe von KQL

1. In dieser Abfrage werden 100 Datensätze aus der Tabelle „Trips“ abgerufen. Wir verwenden das Schlüsselwort ```take```, um das Modul aufzufordern, 100 Datensätze zurückzugeben.

    ```kusto
    
    Trips
    | take 100
    ```

    > **HINWEIS:** Das Pipe-Zeichen (```|```) wird für zwei Zwecke in KQL verwendet, einschließlich der Trennung von Abfrageoperatoren in einer tabellarischen Ausdrucksanweisung. Er wird auch als logischer OR-Operator in eckigen oder runden Klammern verwendet, um anzugeben, dass Sie eines der durch das Pipe-Zeichen getrennten Elemente angeben können.

1. Wir können genauer sein, indem wir mit dem Schlüsselwort ```project``` bestimmte Attribute hinzufügen, die wir abfragen möchten, und dann mit dem Schlüsselwort ```take``` der Engine mitteilen, wie viele Datensätze sie zurückgeben soll.

    > **HINWEIS:** Die Verwendung von ```//``` kennzeichnet Kommentare, die im Microsoft Fabric-Abfragetool ***Untersuchen Ihrer Daten*** verwendet werden.

    ```kusto
    
    // Use 'project' and 'take' to view a sample number of records in the table and check the data.
    Trips 
    | project vendor_id, trip_distance
    | take 10
    ```

1. Eine weitere gängige Methode in der Analyse ist das Umbenennen von Spalten in unserem Abfrageset, um sie benutzerfreundlicher zu gestalten. Dies kann erreicht werden, indem Sie den neuen Spaltennamen gefolgt von dem Gleichheitszeichen und der Spalte verwenden, die wir umbenennen möchten.

    ```kusto
    
    Trips 
    | project vendor_id, ["Trip Distance"] = trip_distance
    | take 10
    ```

1. Eventuell möchten wir auch die Fahrten zusammenfassen, um zu sehen, wie viele Meilen zurückgelegt wurden:

    ```kusto
    
    Trips
    | summarize ["Total Trip Distance"] = sum(trip_distance)
    ```

## ```GROUP BY``` Daten aus unserem Beispieldataset mithilfe von KQL

1. Dann können wir nach dem Abholort ***gruppieren***, was wir mit dem Operator ```summarize``` durchführen. Wir können auch den ```project```-Operator verwenden, mit dem wir die Spalten, die Sie in Ihre Ausgabe aufnehmen möchten, auswählen und umbenennen können. In diesem Fall gruppieren wir innerhalb des Taxisystems von New York nach Stadtbezirken, um unseren Benutzer*innen die zurückgelegte Gesamtstrecke für jeden Stadtbezirk anzuzeigen.

```kusto

Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = pickup_boroname, ["Total Trip Distance"]
```

1. In diesem Fall haben wir einen leeren Wert, was nie gut für die Analyse ist. Wir können die Funktion ```case``` zusammen mit den Funktionen ```isempty``` und ```isnull``` verwenden, um diese leeren Werte zur Nachverfolgung in einer eigenen Kategorie mit dem Titel ***Nicht identifiziert*** zu erfassen.

```kusto

Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
```

## ```ORDER BY``` Daten aus unserem Beispieldataset mithilfe von KQL

Um unsere Daten besser zu verstehen, sortieren wir sie in der Regel nach einer Spalte. Dieser Vorgang wird in KQL entweder mit einem ```sort by```- oder einem ```order by```-Operator durchgeführt, die beide auf dieselbe Weise funktionieren.
 
```kusto

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

Im Gegensatz zu SQL wird die WHERE-Klausel sofort in unserer KQL-Abfrage aufgerufen. Wir können weiterhin die logischen Operatoren ```and``` und ```or``` in der Where-Klausel verwenden. Der Vorgang wird in der Tabelle als wahr oder falsch ausgewertet und kann einfach oder ein komplexer Ausdruck sein, der mehrere Spalten, Operatoren und Funktionen umfassen kann.

```kusto

// let's filter our dataset immediately from the source by applying a filter directly after the table.
Trips
| where pickup_boroname == "Manhattan"
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc

```

## Verwenden von T-SQL zum Abfragen von Zusammenfassungsinformationen

KQL Database unterstützt T-SQL nicht von Haus aus, aber es bietet einen T-SQL-Endpunkt, der Microsoft SQL Server emuliert und es Ihnen ermöglicht, T-SQL-Abfragen über Ihre Daten durchzuführen. Der T-SQL-Endpunkt weist jedoch einige Einschränkungen und Unterschiede zum nativen SQL Server auf. Er unterstützt zum Beispiel nicht das Erstellen, Ändern oder Löschen von Tabellen oder das Einfügen, Aktualisieren oder Löschen von Daten. Außerdem werden einige T-SQL-Funktionen und -Syntaxen nicht unterstützt, die nicht mit KQL kompatibel sind. Es wurde erstellt, um Systemen, die KQL nicht unterstützt haben, die Verwendung von T-SQL zum Abfragen der Daten in einer KQL-Datenbank zu ermöglichen. Sie sollten daher KQL als primäre Abfragesprache für die KQL-Datenbank verwenden, da diese mehr Möglichkeiten und Leistung bietet als T-SQL. Sie können auch einige SQL-Funktionen verwenden, die von KQL unterstützt werden, z. B. count, sum, avg, min, max usw. 

## ```SELECT```-Daten aus unserem Beispieldataset mithilfe von T-SQL

1. In dieser Abfrage rufen wir die ersten 100 Datensätze aus der Tabelle **Trips** mit der ```TOP```-Klausel ab. 

    ```sql
    // We can use the TOP clause to limit the number of records returned
    
    SELECT TOP 100 * from Trips
    ```

1. Wenn Sie ```//``` verwenden (ein Kommentar im Tool ***Daten durchsuchen** in der KQL-Datenbank), können Sie es bei der Ausführung von T-SQL-Abfragen nicht hervorheben, sondern sollten die Standard-Notation ```--``` für SQL-Kommentare verwenden. Dieser doppelte Bindestrich sagt der KQL Engine auch, dass sie im Azure Data Explorer T-SQL erwarten soll.

    ```sql
    -- instead of using the 'project' and 'take' keywords we simply use a standard SQL Query
    SELECT TOP 10 vendor_id, trip_distance
    FROM Trips
    ```

1. Auch hier sehen Sie, dass T-SQL-Standardfeatures mit der Abfrage einwandfrei funktionieren, bei der wir trip_distance in einen benutzerfreundlicheren Namen umbenennen.

    ```sql
    
    -- No need to use the 'project' or 'take' operators as standard T-SQL Works
    SELECT TOP 10 vendor_id, trip_distance as [Trip Distance]
    from Trips
    ```

1. Eventuell möchten wir auch die Fahrten zusammenfassen, um zu sehen, wie viele Meilen zurückgelegt wurden:

    ```sql
    Select sum(trip_distance) as [Total Trip Distance]
    from Trips
    ```
     >**HINWEIS:** Die Verwendung von Anführungszeichen ist in T-SQL im Vergleich zu KQL-Abfragen nicht erforderlich. Beachten Sie außerdem, dass die Befehle `summarize` und `sort by` in T-SQL nicht verfügbar sind.

## ```GROUP BY```-Daten aus unserem Beispieldataset mithilfe von T-SQL

1. Dann könnten wir nach dem Abholort ***gruppieren***. Dies tun wir mit dem ```GROUP BY```-Operator. Wir können auch den ```AS```-Operator verwenden, mit dem wir die Spalten, die Sie in Ihre Ausgabe aufnehmen möchten, auswählen und umbenennen können. In diesem Fall gruppieren wir innerhalb des Taxisystems von New York nach Stadtbezirken, um unseren Benutzer*innen die zurückgelegte Gesamtstrecke für jeden Stadtbezirk anzuzeigen.

    ```sql
    SELECT pickup_boroname AS Borough, Sum(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY pickup_boroname
    ```

1. In diesem Fall haben wir einen leeren Wert, was nie gut für die Analyse ist. Wir können die Funktion ```CASE``` zusammen mit den Funktionen ```IS NULL``` und ```''```, um diese leeren Werte zur Nachverfolgung in einer eigenen Kategorie mit dem Titel ***Nicht identifiziert*** zu erfassen. 

    ```sql
    
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
               ELSE pickup_boroname
             END;
    ```

## ```ORDER BY```-Daten aus unserem Beispieldataset mithilfe von T-SQL

1. Um unsere Daten besser zu verstehen, sortieren wir sie in der Regel nach einer Spalte. Dieser Vorgang wird in T-SQL mit einem ```ORDER BY``` Operator durchgeführt. In T-SQL gibt es keinen ***SORT BY***-Operator.
 
    ```sql
    -- Group by pickup_boroname and calculate the summary statistics of trip_distance
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    -- Add an ORDER BY clause to sort by Borough in ascending order
    ORDER BY Borough ASC;
    ```
    ## Die ```WHERE```-Klausel zum Filtern von Daten in unserer T-SQL-Beispielabfrage
    
1. Im Gegensatz zu KQL würde unsere ```WHERE```-Klausel am Ende der T-SQL-Anweisung stehen. In diesem Fall haben wir jedoch eine ```GROUP BY```-Klausel, für die wir die ```HAVING```-Anweisung verwenden müssen, und wir verwenden den neuen Namen der Spalte, in diesem Fall **Borough**, als Spaltennamen, nach dem gefiltert wird.

    ```sql
    -- Group by pickup_boroname and calculate the summary statistics of trip_distance
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    -- Add a having clause due to the GROUP BY statement
    HAVING Borough = 'Manhattan'
    -- Add an ORDER BY clause to sort by Borough in ascending order
    ORDER BY Borough ASC;
    
    ```

## Bereinigen von Ressourcen

In dieser Übung haben Sie eine KQL-Datenbank erstellt und ein Beispieldataset für Abfragen eingerichtet. Danach haben Sie die Daten mit KQL und SQL abgefragt. Wenn Sie die Untersuchung Ihrer KQL-Datenbank abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.
1. Wählen Sie auf der Leiste auf der linken Seite das **Symbol** für Ihren Arbeitsbereich aus.
2. Wählen Sie auf der Symbolleiste im Menü „...“ die Option **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Andere** die Option **Diesen Arbeitsbereich entfernen** aus.
