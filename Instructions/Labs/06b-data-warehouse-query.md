---
lab:
  title: Abfragen eines Data Warehouses in Microsoft Fabric
  module: Query a data warehouse in Microsoft Fabric
---

# Abfragen eines Data Warehouses in Microsoft Fabric

In Microsoft Fabric stellt ein Data Warehouse eine relationale Datenbank für umfangreiche Analysen bereit. Dank der umfangreichen Funktionen im Microsoft Fabric-Arbeitsbereich müssen die Benutzer*innen weniger Zeit aufwenden, um Erkenntnisse zu erhalten. Dies wird durch ein einfach zu verwendendes, immer verbundenes semantisches Modell ermöglicht, das im DirectLake-Modus mit Power BI integriert ist. 

Dieses Lab dauert ungefähr **30** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Wählen Sie auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric) unter `https://app.fabric.microsoft.com/home?experience=fabric` die Option **Data Warehouse**.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Erstellen eines beispielhaften Data Warehouse

Da Sie nun einen Arbeitsbereich besitzen, ist es an der Zeit, ein Data Warehouse zu erstellen.

1. Stellen Sie unten links sicher, dass die Erfahrung **Data Warehouse** ausgewählt ist.
1. Wählen Sie auf der **Willkommensseite** **Sample Warehouse** aus und erstellen Sie ein neues Data Warehouse mit dem Namen **sample-dw**.

    Nach einer Minute wird ein neues Warehouse erstellt und mit Beispieldaten für eine Analyse von Taxifahrten gefüllt.

    ![Screenshot eines neuen Warehouse](./Images/sample-data-warehouse.png)

## Abfragen des Data Warehouse

Der SQL-Abfrage-Editor bietet Unterstützung für IntelliSense, Codevervollständigung, Syntaxhervorhebung, clientseitige Analyse und Validierung. Sie können Anweisungen der Datendefinitionssprache (Data Definition Language, DDL), Datenbearbeitungssprache (Data Manipulation Language, DML) und Datenkontrollsprache (Data Control Language, DCL) ausführen.

1. Wählen Sie auf der Data Warehouse-Seite **sample-bw** in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage**aus.

1. Geben Sie im neuen leeren Abfragebereich den folgenden Transact-SQL-Code ein:

    ```sql
    SELECT 
    D.MonthName, 
    COUNT(*) AS TotalTrips, 
    SUM(T.TotalAmount) AS TotalRevenue 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.MonthName;
    ```

1. Verwenden Sie die Schaltfläche **&#9655; Ausführen**, um das SQL-Skript auszuführen und die Ergebnisse anzusehen, die die Gesamtanzahl an Fahrten und den Gesamtumsatz pro Monat anzeigen.

1. Geben Sie den folgenden Transact-SQL-Code ein:

    ```sql
   SELECT 
    D.DayName, 
    AVG(T.TripDurationSeconds) AS AvgDuration, 
    AVG(T.TripDistanceMiles) AS AvgDistance 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.DayName;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die die durchschnittliche Fahrtdauer und Entfernung nach Wochentag anzeigen.

1. Geben Sie den folgenden Transact-SQL-Code ein:

    ```sql
    SELECT TOP 10 
    G.City, 
    COUNT(*) AS TotalTrips 
    FROM dbo.Trip AS T
    JOIN dbo.Geography AS G
        ON T.PickupGeographyID=G.GeographyID
    GROUP BY G.City
    ORDER BY TotalTrips DESC;
    
    SELECT TOP 10 
        G.City, 
        COUNT(*) AS TotalTrips 
    FROM dbo.Trip AS T
    JOIN dbo.Geography AS G
        ON T.DropoffGeographyID=G.GeographyID
    GROUP BY G.City
    ORDER BY TotalTrips DESC;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die die 10 beliebtesten Abhol- und Zielorte anzeigen.

1. Schließen Sie alle Registerkarten der Abfrage.

## Überprüfen der Datenkonsistenz

Die Überprüfung der Datenkonsistenz ist wichtig, um sicherzustellen, dass die Daten für die Analyse und Entscheidungsfindung korrekt und zuverlässig sind. Inkonsistente Daten können zu falschen Analysen und irreführenden Ergebnissen führen. 

Führen wir eine Abfrage für Ihr Data Warehouse aus, um die Konsistenz zu überprüfen.

1. Wählen Sie in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage** aus.

1. Geben Sie im neuen leeren Abfragebereich den folgenden Transact-SQL-Code ein:

    ```sql
    -- Check for trips with unusually long duration
    SELECT COUNT(*) FROM dbo.Trip WHERE TripDurationSeconds > 86400; -- 24 hours
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die Details zu allen Fahrten mit einer ungewöhnlich langen Dauer anzeigen.

1. Wählen Sie in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage aus**, um eine zweite Registerkarte für die Abfrage hinzuzufügen. Führen Sie dann in der neuen leeren Registerkarte den folgenden Code aus:

    ```sql
    -- Check for trips with negative trip duration
    SELECT COUNT(*) FROM dbo.Trip WHERE TripDurationSeconds < 0;
    ```

1. Geben Sie im neuen leeren Abfragebereich den folgenden Transact-SQL-Code ein und führen Sie ihn aus:

    ```sql
    -- Remove trips with negative trip duration
    DELETE FROM dbo.Trip WHERE TripDurationSeconds < 0;
    ```

    > **Hinweis:** Es gibt verschiedene Möglichkeiten, inkonsistente Daten zu behandeln. Anstatt sie zu entfernen, können sie sie durch einen anderen Wert wie das Mittel oder den Median ersetzen.

1. Schließen Sie alle Registerkarten der Abfrage.

## Als Ansicht speichern

Angenommen, Sie müssen bestimmte Fahrten nach einer Gruppe von Benutzern filtern, die die Daten zum Generieren von Berichten verwenden.

Erstellen wir eine Ansicht basierend auf der zuvor verwendeten Abfrage und fügen einen Filter hinzu.

1. Wählen Sie in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage** aus.

1. Geben Sie im neuen leeren Abfragebereich erneut den folgenden Transact-SQL-Code ein und führen Sie ihn aus:

    ```sql
    SELECT 
        D.DayName, 
        AVG(T.TripDurationSeconds) AS AvgDuration, 
        AVG(T.TripDistanceMiles) AS AvgDistance 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.DayName;
    ```

1. Ändern Sie die Abfrage und fügen Sie `WHERE D.Month = 1` hinzu. Dadurch werden die Daten so gefiltert, dass nur Datensätze aus dem Monat Januar enthalten sind. Die endgültige Abfrage sollte wie folgt aussehen:

    ```sql
    SELECT 
        D.DayName, 
        AVG(T.TripDurationSeconds) AS AvgDuration, 
        AVG(T.TripDistanceMiles) AS AvgDistance 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    WHERE D.Month = 1
    GROUP BY D.DayName
    ```

1. Markieren Sie den Text der SELECT-Anweisung in Ihrer Abfrage. Wählen Sie neben der Schaltfläche **&#9655; Führen Sie** den Wert **Als Ansicht speichern** aus.

1. Erstellen Sie eine neue Ansicht mit dem Namen **vw_JanTrip**.

1. Navigieren Sie im **Explorer** zu **Schemas >> dbo >> Ansichten**. Beachten Sie die Ansicht *vw_JanTrip*, die Sie soeben erstellt haben.

1. Schließen Sie alle Registerkarten der Abfrage.

> **Weitere Informationen**: Weitere Informationen zum Abfragen eines Data Warehouse finden Sie unter [Abfrage mithilfe des SQL-Abfrage-Editors](https://learn.microsoft.com/fabric/data-warehouse/sql-query-editor) in der Microsoft Fabric-Dokumentation.

## Bereinigen von Ressourcen

In dieser Übung haben Sie Abfragen verwendet, um Einblicke in die Daten in einem Microsoft Fabric-Data Warehouse zu erhalten.

Wenn Sie Ihr Data Warehouse erkundet haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
1. Wählen Sie **Arbeitsbereichseinstellungen** und scrollen Sie im Abschnitt **Allgemein** nach unten und wählen Sie **Diesen Arbeitsbereich entfernen**.
1. Wählen Sie **Löschen**, um den Arbeitsbereich zu löschen.
