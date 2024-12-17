---
lab:
  title: Arbeiten mit Echtzeitdaten in einem Microsoft Fabric Eventhouse
  module: Work with data in a Microsoft Fabric eventhouse
---

# Arbeiten mit Echtzeitdaten in einem Microsoft Fabric Eventhouse

In Microsoft Fabric wird ein *Eventhouse* verwendet, um Echtzeitdaten im Zusammenhang mit Ereignissen zu speichern. Dies wird häufig von einer Streamingdatenquelle durch einen *Eventstream* erfasst.

Innerhalb eines Eventhouses werden die Daten in einer oder mehreren KQL-Datenbanken gespeichert, die jeweils Tabellen und andere Objekte enthalten, die Sie mithilfe von KQL (Kusto-Abfragesprache) oder einer Teilmenge von SQL (Structured Query Language) abfragen können.

In dieser Übung erstellen und füllen Sie ein Eventhouse mit einigen Beispieldaten im Zusammenhang mit Taxifahrten und fragen dann die Daten mithilfe von KQL und SQL ab.

Diese Übung dauert ca. **25** Minuten.

## Erstellen eines Arbeitsbereichs

Bevor Sie mit Daten in Fabric arbeiten, erstellen Sie einen Arbeitsbereich mit aktivierter Fabric-Kapazität.

1. Wählen Sie auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric) unter `https://app.fabric.microsoft.com/home?experience=fabric` die Option **Real-Time Intelligence** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Ein Eventhouse erstellen

Nachdem Sie nun über einen Arbeitsbereich mit Unterstützung für eine Fabric-Kapazität verfügen, können Sie ein Eventhouse darin erstellen.

1. Erstellen Sie auf der Startseite von **Real-Time Intelligence** ein neues **Eventhouse** mit einem Namen Ihrer Wahl. Wenn das Eventhouse erstellt wurde, schließen Sie alle Eingabeaufforderungen oder Tipps, die angezeigt werden, bis die Eventhouse-Seite angezeigt wird:

   ![Screenshot eines neuen Eventhouse.](./Images/create-eventhouse.png)

1. Beachten Sie im linken Bereich, dass Ihr Eventhouse eine KQL-Datenbank mit demselben Namen wie das Eventhouse enthält.
1. Wählen Sie die KQL-Datenbank aus, um sie anzuzeigen.

    Derzeit enthält die Datenbank keine Tabellen. In der Übung verwenden Sie dann noch einen Ereignisstream, um Daten aus einer Echtzeitquelle in eine Tabelle zu laden.
   
1. Wählen Sie auf der Seite der KQL-Datenbank **Get data** > **Sample**. Wählen Sie dann die **Automotive Operations Analytics**-Beispieldaten aus.

1. Stellen Sie nach Abschluss des Ladens der Daten (nimmt evtl. einige Zeit in Anspruch) sicher, dass eine **Automotive**-Tabelle erstellt wurde.

   ![Screenshot der Automotive-Tabelle in einer Eventhouse-Datenbank.](./Images/choose-automotive-operations-analytics.png)

## Abfragen von Daten mithilfe von KQL

Kusto-Abfragesprache (KQL) ist eine intuitive, umfassende Sprache, mit der Sie eine KQL-Datenbank abfragen können.

### Abrufen von Daten mithilfe von KQL

1. Wählen Sie im linken Bereich des Eventhouse-Fensters unter Ihrer KQL-Datenbank die Standarddatei **Abfrageset** aus. Diese Datei enthält einige Beispiel-KQL-Abfragen, um Ihnen den Einstieg zu erleichtern.
1. Ändern Sie die erste Beispielabfrage wie folgt.

    ```kql
    Automotive
    | take 100
    ```

    > **HINWEIS:** Das Pipe-Zeichen ( | ) wird für zwei Zwecke in KQL verwendet, einschließlich der Trennung von Abfrageoperatoren in einer tabellarischen Ausdrucksanweisung. Er wird auch als logischer OR-Operator in eckigen oder runden Klammern verwendet, um anzugeben, dass Sie eines der durch das Pipe-Zeichen getrennten Elemente angeben können.

1. Wählen Sie den Abfragecode aus und führen Sie ihn aus, um 100 Zeilen aus der Tabelle zurückzugeben.

   ![Screenshot des KQL-Abfrageeditors.](./Images/kql-take-100-query.png)

    Sie können präziser sein, indem Sie bestimmte Attribute, die Sie abfragen möchten, mit dem Schlüsselwort `project` hinzufügen und dann mit dem Schlüsselwort `take` angeben, wie viele Datensätze zurückgegeben werden sollen.

1. Geben Sie die folgende Abfrage ein, wählen Sie sie aus und führen Sie sie aus:

    ```kql
    // Use 'project' and 'take' to view a sample number of records in the table and check the data.
    Automotive 
    | project vendor_id, trip_distance
    | take 10
    ```

    > **HINWEIS:** Die Verwendung von // kennzeichnet einen Kommentar.

    Eine weitere gängige Methode in der Analyse ist das Umbenennen von Spalten im Abfragesatz, um sie benutzerfreundlicher zu gestalten.

1. Probieren Sie die folgende Abfrage aus:

    ```kql
    Automotive 
    | project vendor_id, ["Trip Distance"] = trip_distance
    | take 10
    ```

### Zusammenfassen der Daten mithilfe von KQL

Sie können das Schlüsselwort *Zusammenfassen* mit einer Funktion zur Aggregation und anderweitigen Bearbeitung von Daten verwenden.

1. Probieren Sie die folgende Abfrage aus, die die Funktion **sum** verwendet, um die Reisedaten zusammenzufassen und weitere Informationen zur insgesamt zurückgelegten Strecke zu erhalten:

    ```kql

    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance)
    ```

    Sie können die zusammengefassten Daten nach einer angegebenen Spalte oder einem angegebenen Ausdruck gruppieren.

1. Führen Sie die folgende Abfrage aus, um die Fahrtstrecken nach Stadtbezirken innerhalb des NY-Taxisystems zu gruppieren und die Gesamtstrecke zu ermitteln, die von jedem Stadtbezirk aus zurückgelegt wurde.

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = pickup_boroname, ["Total Trip Distance"]
    ```

    Die Ergebnisse enthalten einen Leerwert, der für die Analyse nie gut ist.

1. Ändern Sie die Abfrage wie hier gezeigt, um die Funktion *case* zusammen mit den Funktionen *isempty* und *isnull* zu verwenden, um alle Reisen, für die der Stadtbezirk unbekannt ist, für die Nachverfolgung in eine Kategorie ***Nicht identifiziert*** zu gruppieren.

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    ```

### Sortieren von Daten mithilfe von KQL

Um unsere Daten besser zu verstehen, ordnen wir sie normalerweise nach einer Spalte, und dieser Vorgang wird in KQL entweder mit einem *sort by*- oder einem *order by*-Operator durchgeführt (sie funktionieren auf die gleiche Weise).

1. Probieren Sie die folgende Abfrage aus:

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    | sort by Borough asc
    ```

1. Ändern Sie die Abfrage wie folgt und führen Sie sie erneut aus. Beachten Sie, dass der Operator *order by* genauso funktioniert wie *sort by*:

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    | order by Borough asc 
    ```

### Filtern von Daten mithilfe von KQL

In KQL wird die Klausel *where* zum Filtern von Daten verwendet. Sie können Bedingungen in einer *where*-Klausel kombinieren, indem Sie die logischen Operatoren *and* und *or* verwenden.

1. Führen Sie die folgende Abfrage aus, um die Reisedaten so zu filtern, dass nur Reisen enthalten sind, die aus Manhatten stammen:

    ```kql
    Automotive
    | where pickup_boroname == "Manhattan"
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    | sort by Borough asc
    ```

## Abfragen von Daten mithilfe von Transact-SQL

KQL Database unterstützt Transact-SQL nicht von Haus aus, aber es bietet einen T-SQL-Endpunkt, der Microsoft SQL Server emuliert und es Ihnen ermöglicht, T-SQL-Abfragen über Ihre Daten durchzuführen. Der T-SQL-Endpunkt weist einige Einschränkungen und Unterschiede zum nativen SQL Server auf. Er unterstützt zum Beispiel nicht das Erstellen, Ändern oder Löschen von Tabellen oder das Einfügen, Aktualisieren oder Löschen von Daten. Außerdem werden einige T-SQL-Funktionen und -Syntaxen nicht unterstützt, die nicht mit KQL kompatibel sind. Es wurde erstellt, um Systemen, die KQL nicht unterstützt haben, die Verwendung von T-SQL zum Abfragen der Daten in einer KQL-Datenbank zu ermöglichen. Sie sollten daher KQL als primäre Abfragesprache für die KQL-Datenbank verwenden, da diese mehr Möglichkeiten und Leistung bietet als T-SQL. Sie können auch einige SQL-Funktionen verwenden, die von KQL unterstützt werden, wie z. B. count, sum, avg, min, max usw.

### Abrufen von Daten aus einer Tabelle mithilfe von Transact-SQL

1. Fügen Sie in Ihrem Abfrageset die folgende Transact-SQL-Abfrage hinzu, und führen Sie sie aus: 

    ```sql
    SELECT TOP 100 * from Automotive
    ```

1. Ändern Sie die Abfrage wie folgt, um bestimmte Spalten abzurufen:

    ```sql
    SELECT TOP 10 vendor_id, trip_distance
    FROM Automotive
    ```

1. Ändern Sie die Abfrage, um einen Alias zuzuweisen, der **trip_distance** in einen benutzerfreundlicheren Namen umbenennt.

    ```sql
    SELECT TOP 10 vendor_id, trip_distance as [Trip Distance]
    from Automotive
    ```

### Zusammenfassen von Daten mithilfe von Transact-SQL

1. Führen Sie die folgende Abfrage aus, um die gesamt zurückgelegte Entfernung zu ermitteln:

    ```sql
    SELECT sum(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    ```

1. Ändern Sie die Abfrage so, dass die Gesamtdistanz nach Abholungsbezirk gruppiert wird:

    ```sql
    SELECT pickup_boroname AS Borough, Sum(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY pickup_boroname
    ```

1. Passen Sie die Abfrage weiter an, um eine *CASE*-Anweisung zu verwenden, um Reisen mit unbekanntem Ursprung in eine ***Nicht identifiziert***-Kategorie für die Nachverfolgung zu gruppieren. 

    ```sql
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
               ELSE pickup_boroname
             END;
    ```

### Sortieren von Daten mithilfe von Transact-SQL

1. Führen Sie die folgende Abfrage aus, um die gruppierten Ergebnisse nach Bezirk zu sortieren:
 
    ```sql
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    ORDER BY Borough ASC;
    ```

### Filtern von Daten mithilfe von Transact-SQL
    
1. Führen Sie die folgende Abfrage aus, um die gruppierten Daten so zu filtern, dass nur Zeilen mit dem Bezirk „Manhattan“ in den Ergebnissen enthalten sind:

    ```sql
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    HAVING Borough = 'Manhattan'
    ORDER BY Borough ASC;
    ```

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Eventhouse erstellt und Daten mithilfe von KQL und SQL abgefragt.

Wenn Sie die Untersuchung Ihrer KQL-Datenbank abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus.
2. Wählen Sie in der Symbolleiste **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.
