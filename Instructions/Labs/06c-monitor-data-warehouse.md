---
lab:
  title: Überwachen eines Data Warehouse in Microsoft Fabric
  module: Monitor a data warehouse in Microsoft Fabric
---

# Überwachen eines Data Warehouse in Microsoft Fabric

In Microsoft Fabric stellt ein Data Warehouse eine relationale Datenbank für umfangreiche Analysen bereit. Data Warehouses in Microsoft Fabric umfassen dynamische Verwaltungssichten, die Sie zum Überwachen von Aktivitäten und Abfragen verwenden können.

Dieses Lab dauert ungefähr **30** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Navigieren Sie in einem Browser unter `https://app.fabric.microsoft.com/home?experience=fabric` zur [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric) und melden Sie sich mit Ihren Fabric-Anmeldeinformationen an.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Erstellen eines beispielhaften Data Warehouse

Da Sie nun einen Arbeitsbereich besitzen, ist es an der Zeit, ein Data Warehouse zu erstellen.

1. Wählen Sie in der Menüleiste auf der linken Seite **Erstellen** aus. Wählen Sie auf der Seite *Neu* unter dem Abschnitt *Data Warehouse* die Option **Beispielwarehouse** und erstellen Sie ein neues Data Warehouse mit dem Namen **sample-dw**.

    >**Hinweis**: Wenn die Option **Erstellen** nicht an die Seitenleiste angeheftet ist, müssen Sie zuerst die Ellipses-Option (**…**) auswählen.

    Nach einer Minute wird ein neues Warehouse erstellt und mit Beispieldaten für eine Analyse von Taxifahrten gefüllt.

    ![Screenshot eines neuen Warehouse](./Images/sample-data-warehouse.png)

## Erkunden von dynamischen Verwaltungssichten

Data Warehouses in Microsoft Fabric umfassen dynamische Verwaltungssichten (DMVs), mit denen Sie die aktuelle Aktivität in der Data Warehouse-Instanz identifizieren können.

1. Wählen Sie auf der Data Warehouse-Seite **sample-bw** in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage**aus.
1. Geben Sie im neuen leeren Abfragebereich den folgenden Transact-SQL-Code ein, um die DMV **sys.dm_exec_connections** abzufragen:

    ```sql
   SELECT * FROM sys.dm_exec_connections;
    ```

1. Verwenden Sie die Schaltfläche **&#9655; Ausführen** aus, um das SQL-Skript auszuführen und die Ergebnisse anzuzeigen, einschließlich Details aller Verbindungen zum Data Warehouse.
1. Ändern Sie den SQL-Code, um die DMV **sys.dm_exec_sessions** wie folgt abzufragen:

    ```sql
   SELECT * FROM sys.dm_exec_sessions;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die Details zu allen nicht authentifizierten Sitzungen anzeigen.
1. Ändern Sie den SQL-Code, um die DMV **sys.dm_exec_requests** wie folgt abzufragen:

    ```sql
   SELECT * FROM sys.dm_exec_requests;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die Details zu allen im Data Warehouse ausgeführten Abfragen anzeigen.
1. Ändern Sie den SQL-Code, um die DMVs zu verknüpfen und Informationen zu derzeit ausgeführten Anforderungen wie folgt in derselben Datenbank zurückzugeben:

    ```sql
   SELECT connections.connection_id,
    sessions.session_id, sessions.login_name, sessions.login_time,
    requests.command, requests.start_time, requests.total_elapsed_time
   FROM sys.dm_exec_connections AS connections
   INNER JOIN sys.dm_exec_sessions AS sessions
       ON connections.session_id=sessions.session_id
   INNER JOIN sys.dm_exec_requests AS requests
       ON requests.session_id = sessions.session_id
   WHERE requests.status = 'running'
       AND requests.database_id = DB_ID()
   ORDER BY requests.total_elapsed_time DESC;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die Details zu allen laufenden Abfragen in der Datenbank anzeigen (einschließlich dieser Abfrage).
1. Wählen Sie in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage aus**, um eine zweite Registerkarte für die Abfrage hinzuzufügen. Führen Sie dann in der neuen leeren Registerkarte den folgenden Code aus:

    ```sql
   WHILE 1 = 1
       SELECT * FROM Trip;
    ```

1. Lassen Sie die Abfrage laufen und kehren Sie zur Registerkarte zurück, die den Code enthält, um die DMVs abzufragen und erneut auszuführen. Dieses Mal sollten die Ergebnisse die zweite Abfrage enthalten, die in der anderen Registerkarte ausgeführt wird. Beachten Sie die verstrichene Zeit für diese Abfrage.
1. Warten Sie einige Sekunden und führen Sie den Code erneut aus, um die DMVs erneut abzufragen. Die verstrichene Zeit für die Abfrage in der anderen Registerkarte sollte gestiegen sein.
1. Kehren Sie zur zweiten Abfrageregisterkarte zurück, auf der die Abfrage noch läuft, und wählen Sie **Abbrechen** aus, um sie abzubrechen.
1. Führen Sie die Abfrage erneut in der Registerkarte mit dem Code zum Abfragen der DMVs aus, um zu bestätigen, dass die zweite Abfrage nicht mehr ausgeführt wird.
1. Schließen Sie alle Registerkarten der Abfrage.

> **Weitere Informationen**: Weitere Informationen zur Verwendung von DMVs finden Sie in der Microsoft Fabric-Dokumentation unter [Überwachen von Verbindungen, Sitzungen und Anforderungen mithilfe von DMVs](https://learn.microsoft.com/fabric/data-warehouse/monitor-using-dmv).

## Erkunden von Abfrageerkenntnissen

Data Warehouses in Microsoft Fabric bieten *Abfrageeinblicke* – einen speziellen Satz von Ansichten, die Details zu den Abfragen bereitstellen, die in Ihrem Data Warehouse ausgeführt werden.

1. Wählen Sie auf der Data Warehouse-Seite **sample-bw** in der Dropdownliste **Neue SQL-Abfrage** **Neue SQL-Abfrage**aus.
1. Geben Sie im neuen leeren Abfragebereich den folgenden Transact-SQL-Code ein, um die Ansicht **exec_requests_history** abzufragen:

    ```sql
   SELECT * FROM queryinsights.exec_requests_history;
    ```

1. Verwenden Sie die Schaltfläche **&#9655; Ausführen** aus, um das SQL-Skript auszuführen und die Ergebnisse anzuzeigen, einschließlich Details der zuvor ausgeführten Abfragen.
1. Ändern Sie den SQL-Code, um die Ansicht **frequently_run_queries** wie folgt abzufragen:

    ```sql
   SELECT * FROM queryinsights.frequently_run_queries;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die Details zu häufig ausgeführten Abfragen anzeigen.
1. Ändern Sie den SQL-Code, um die Ansicht **long_running_queries** wie folgt abzufragen:

    ```sql
   SELECT * FROM queryinsights.long_running_queries;
    ```

1. Führen Sie die geänderte Abfrage aus und zeigen Sie die Ergebnisse an, die Details zu allen Abfragen und deren Laufzeit anzeigen.

> **Weitere Informationen**: Weitere Informationen zur Verwendung von Abfrageerkenntnissen finden Sie in der Microsoft Fabric-Dokumentation unter [Abfrageerkenntnisse in Fabric Data Warehousing](https://learn.microsoft.com/fabric/data-warehouse/query-insights).


## Bereinigen von Ressourcen

In dieser Übung haben Sie dynamische Verwaltungssichten und Abfrageerkenntnisse verwendet, um Aktivitäten in einem Data Warehouse in Microsoft Fabric zu überwachen.

Wenn Sie Ihr Data Warehouse erkundet haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
1. Wählen Sie **Arbeitsbereichseinstellungen** und scrollen Sie im Abschnitt **Allgemein** nach unten und wählen Sie **Diesen Arbeitsbereich entfernen**.
1. Wählen Sie **Löschen**, um den Arbeitsbereich zu löschen.
