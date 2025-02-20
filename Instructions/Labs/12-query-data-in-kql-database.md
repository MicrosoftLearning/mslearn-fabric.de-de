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

1. Navigieren Sie in einem Browser unter `https://app.fabric.microsoft.com/home?experience=fabric` zur [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric) und melden Sie sich mit Ihren Fabric-Anmeldeinformationen an.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Ein Eventhouse erstellen

Nachdem Sie nun über einen Arbeitsbereich mit Unterstützung für eine Fabric-Kapazität verfügen, können Sie ein Eventhouse darin erstellen.

1. Wählen Sie in der Menüleiste auf der linken Seite **Workloads** aus. Wählen Sie dann die Kachel **Real-Time Intelligence** aus.
1. Wählen Sie auf der Startseite von **Real-Time Intelligence** die Kachel **Real-Time Intelligence-Beispiel erkunden** aus. Es wird automatisch ein Eventhouse namens **RTISample** erstellt:

   ![Screenshot eines neuen Eventhouse mit Beispieldaten.](./Images/create-eventhouse-sample.png)

1. Beachten Sie im linken Bereich, dass Ihr Eventhouse eine KQL-Datenbank mit demselben Namen wie das Eventhouse enthält.
1. Stellen Sie sicher, dass auch eine **Bikestream-Tabelle** erstellt wurde.

## Abfragen von Daten mithilfe von KQL

Kusto-Abfragesprache (KQL) ist eine intuitive, umfassende Sprache, mit der Sie eine KQL-Datenbank abfragen können.

### Abrufen von Daten mithilfe von KQL

1. Wählen Sie im linken Bereich des Eventhouse-Fensters unter Ihrer KQL-Datenbank die Standarddatei **Abfrageset** aus. Diese Datei enthält einige Beispiel-KQL-Abfragen, um Ihnen den Einstieg zu erleichtern.
1. Ändern Sie die erste Beispielabfrage wie folgt.

    ```kql
    Bikestream
    | take 100
    ```

    > **HINWEIS:** Das Pipe-Zeichen ( | ) wird für zwei Zwecke in KQL verwendet, einschließlich der Trennung von Abfrageoperatoren in einer tabellarischen Ausdrucksanweisung. Er wird auch als logischer OR-Operator in eckigen oder runden Klammern verwendet, um anzugeben, dass Sie eines der durch das Pipe-Zeichen getrennten Elemente angeben können.

1. Wählen Sie den Abfragecode aus und führen Sie ihn aus, um 100 Zeilen aus der Tabelle zurückzugeben.

   ![Screenshot des KQL-Abfrageeditors.](./Images/kql-take-100-query.png)

    Sie können präziser sein, indem Sie bestimmte Attribute, die Sie abfragen möchten, mit dem Schlüsselwort `project` hinzufügen und dann mit dem Schlüsselwort `take` angeben, wie viele Datensätze zurückgegeben werden sollen.

1. Geben Sie die folgende Abfrage ein, wählen Sie sie aus und führen Sie sie aus:

    ```kql
    // Use 'project' and 'take' to view a sample number of records in the table and check the data.
    Bikestream
    | project Street, No_Bikes
    | take 10
    ```

    > **HINWEIS:** Die Verwendung von // kennzeichnet einen Kommentar.

    Eine weitere gängige Methode in der Analyse ist das Umbenennen von Spalten im Abfragesatz, um sie benutzerfreundlicher zu gestalten.

1. Probieren Sie die folgende Abfrage aus:

    ```kql
    Bikestream 
    | project Street, ["Number of Empty Docks"] = No_Empty_Docks
    | take 10
    ```

### Zusammenfassen der Daten mithilfe von KQL

Sie können das Schlüsselwort *Zusammenfassen* mit einer Funktion zur Aggregation und anderweitigen Bearbeitung von Daten verwenden.

1. Versuchen Sie die folgende Abfrage, die die Funktion **sum** verwendet, um die Mietdaten zusammenzufassen und zu sehen, wie viele Fahrräder insgesamt verfügbar sind:

    ```kql

    Bikestream
    | summarize ["Total Number of Bikes"] = sum(No_Bikes)
    ```

    Sie können die zusammengefassten Daten nach einer angegebenen Spalte oder einem angegebenen Ausdruck gruppieren.

1. Führen Sie die folgende Abfrage aus, um die Anzahl der Fahrräder nach Nachbarschaft zu gruppieren, um die Menge der verfügbaren Fahrräder in den einzelnen Vierteln zu ermitteln:

    ```kql
    Bikestream
    | summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
    | project Neighbourhood, ["Total Number of Bikes"]
    ```

    Wenn einer der Fahrradpunkte einen Null- oder Leereintrag für die Nachbarschaft hat, enthalten die Ergebnisse der Zusammenfassung einen leeren Wert, was für die Analyse nie gut ist.

1. Ändern Sie die Abfrage wie hier gezeigt, um die Funktion *case* zusammen mit den Funktionen *isempty* und *isnull* zu verwenden, um alle Reisen, für die die Nachbarschaft unbekannt ist, zur Nachverfolgung in der Kategorie ***Nicht identifiziert*** zusammenzufassen.

    ```kql
    Bikestream
    | summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
    | project Neighbourhood = case(isempty(Neighbourhood) or isnull(Neighbourhood), "Unidentified", Neighbourhood), ["Total Number of Bikes"]
    ```

    >**Hinweis**: Da dieser Beispieldataset gut gepflegt ist, kann es sein, dass Sie das Feld „Nicht identifiziert“ im Abfrageergebnis nicht finden.

### Sortieren von Daten mithilfe von KQL

Um unsere Daten besser zu verstehen, ordnen wir sie normalerweise nach einer Spalte, und dieser Vorgang wird in KQL entweder mit einem *sort by*- oder einem *order by*-Operator durchgeführt (sie funktionieren auf die gleiche Weise).

1. Probieren Sie die folgende Abfrage aus:

    ```kql
    Bikestream
    | summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
    | project Neighbourhood = case(isempty(Neighbourhood) or isnull(Neighbourhood), "Unidentified", Neighbourhood), ["Total Number of Bikes"]
    | sort by Neighbourhood asc
    ```

1. Ändern Sie die Abfrage wie folgt und führen Sie sie erneut aus. Beachten Sie, dass der Operator *order by* genauso funktioniert wie *sort by*:

    ```kql
    Bikestream
    | summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
    | project Neighbourhood = case(isempty(Neighbourhood) or isnull(Neighbourhood), "Unidentified", Neighbourhood), ["Total Number of Bikes"]
    | order by Neighbourhood asc
    ```

### Filtern von Daten mithilfe von KQL

In KQL wird die Klausel *where* zum Filtern von Daten verwendet. Sie können Bedingungen in einer *where*-Klausel kombinieren, indem Sie die logischen Operatoren *and* und *or* verwenden.

1. Führen Sie die folgende Abfrage aus, um die Fahrraddaten zu filtern, um nur Fahrradpunkte in das Chelsea-Viertel einzuschließen:

    ```kql
    Bikestream
    | where Neighbourhood == "Chelsea"
    | summarize ["Total Number of Bikes"] = sum(No_Bikes) by Neighbourhood
    | project Neighbourhood = case(isempty(Neighbourhood) or isnull(Neighbourhood), "Unidentified", Neighbourhood), ["Total Number of Bikes"]
    | sort by Neighbourhood asc
    ```

## Abfragen von Daten mithilfe von Transact-SQL

KQL Database unterstützt Transact-SQL nicht von Haus aus, aber es bietet einen T-SQL-Endpunkt, der Microsoft SQL Server emuliert und es Ihnen ermöglicht, T-SQL-Abfragen über Ihre Daten durchzuführen. Der T-SQL-Endpunkt weist einige Einschränkungen und Unterschiede zum nativen SQL Server auf. Er unterstützt zum Beispiel nicht das Erstellen, Ändern oder Löschen von Tabellen oder das Einfügen, Aktualisieren oder Löschen von Daten. Außerdem werden einige T-SQL-Funktionen und -Syntaxen nicht unterstützt, die nicht mit KQL kompatibel sind. Es wurde erstellt, um Systemen, die KQL nicht unterstützt haben, die Verwendung von T-SQL zum Abfragen der Daten in einer KQL-Datenbank zu ermöglichen. Sie sollten daher KQL als primäre Abfragesprache für die KQL-Datenbank verwenden, da diese mehr Möglichkeiten und Leistung bietet als T-SQL. Sie können auch einige SQL-Funktionen verwenden, die von KQL unterstützt werden, wie z. B. count, sum, avg, min, max usw.

### Abrufen von Daten aus einer Tabelle mithilfe von Transact-SQL

1. Fügen Sie in Ihrem Abfrageset die folgende Transact-SQL-Abfrage hinzu, und führen Sie sie aus: 

    ```sql
    SELECT TOP 100 * from Bikestream
    ```

1. Ändern Sie die Abfrage wie folgt, um bestimmte Spalten abzurufen:

    ```sql
    SELECT TOP 10 Street, No_Bikes
    FROM Bikestream
    ```

1. Ändern Sie die Abfrage, um einen Alias zu vergeben, der **No_Empty_Docks** in einen benutzerfreundlicheren Namen umbenennt.

    ```sql
    SELECT TOP 10 Street, No_Empty_Docks as [Number of Empty Docks]
    from Bikestream
    ```

### Zusammenfassen von Daten mithilfe von Transact-SQL

1. Führen Sie die folgende Abfrage aus, um die Gesamtzahl der verfügbaren Fahrräder zu ermitteln:

    ```sql
    SELECT sum(No_Bikes) AS [Total Number of Bikes]
    FROM Bikestream
    ```

1. Ändern Sie die Abfrage so, dass die Gesamtzahl der Fahrräder nach Nachbarschaft gruppiert wird:

    ```sql
    SELECT Neighbourhood, Sum(No_Bikes) AS [Total Number of Bikes]
    FROM Bikestream
    GROUP BY Neighbourhood
    ```

1. Ändern Sie die Abfrage weiter, um eine *CASE*-Anweisung zu verwenden, um Fahrradpunkte mit unbekanntem Ursprung in einer ***Nicht identifiziert***-Kategorie für die Nachverfolgung zusammenzufassen. 

    ```sql
    SELECT CASE
             WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
             ELSE Neighbourhood
           END AS Neighbourhood,
           SUM(No_Bikes) AS [Total Number of Bikes]
    FROM Bikestream
    GROUP BY CASE
               WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
               ELSE Neighbourhood
             END;
    ```

### Sortieren von Daten mithilfe von Transact-SQL

1. Führen Sie die folgende Abfrage aus, um die gruppierten Ergebnisse nach Nachbarschaften zu ordnen:
 
    ```sql
    SELECT CASE
             WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
             ELSE Neighbourhood
           END AS Neighbourhood,
           SUM(No_Bikes) AS [Total Number of Bikes]
    FROM Bikestream
    GROUP BY CASE
               WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
               ELSE Neighbourhood
             END
    ORDER BY Neighbourhood ASC;
    ```

### Filtern von Daten mithilfe von Transact-SQL
    
1. Führen Sie die folgende Abfrage aus, um die gruppierten Daten so zu filtern, dass nur Zeilen mit dem Nachbarschaftsnamen „Chelsea“ in den Ergebnissen enthalten sind

    ```sql
    SELECT CASE
             WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
             ELSE Neighbourhood
           END AS Neighbourhood,
           SUM(No_Bikes) AS [Total Number of Bikes]
    FROM Bikestream
    GROUP BY CASE
               WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
               ELSE Neighbourhood
             END
    HAVING Neighbourhood = 'Chelsea'
    ORDER BY Neibourhood ASC;
    ```

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Eventhouse erstellt und Daten mithilfe von KQL und SQL abgefragt.

Wenn Sie die Untersuchung Ihrer KQL-Datenbank abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus.
2. Wählen Sie in der Symbolleiste **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.
