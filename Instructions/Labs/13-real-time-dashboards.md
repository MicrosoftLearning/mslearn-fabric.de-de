
## ***ARBEITSENTWURF**
---
lab:
  title: Echtzeitdashboards
  module: Query data from a Kusto Query database in Microsoft Fabric
---

# Erste Schritte beim Abfragen einer Kusto-Datenbank in Microsoft Fabric

Echtzeitdashboards ermöglichen es Ihnen, Einblicke aus Microsoft Fabric mithilfe der Kusto-Abfragesprache (KQL) zu gewinnen, um sowohl strukturierte als auch unstrukturierte Daten abzurufen und in Diagrammen, Scatterplots, Tabellen usw. in Panels zu rendern, die eine Verknüpfung ähnlich wie Slicer in Power BI ermöglichen. 

Dieses Lab dauert ungefähr **25** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Wählen Sie auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com) die Option **Echtzeitanalyse** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

In diesem Lab verwenden Sie Echtzeitanalysen (RTA) in Fabric, um eine KQL-Datenbank anhand eines Beispiel-Eventstreams zu erstellen. Real-Time Analytics bietet ein Beispieldataset, mit dem Sie die Funktionen von RTA erkunden können. Sie verwenden diese Beispieldaten, um die KGL-/SQL-Abfragen und -Abfragesets zu erstellen, die Echtzeitdaten analysieren und eine zusätzliche Verwendung in nachgelagerten Prozessen ermöglichen.

## Erstellen einer KQL-Datenbank

1. Wählen Sie unter **Echtzeitanalyse** das Feld **KQL-Datenbank** aus.

   ![Abbildung der Auswahl der KQL-Datenbank](./Images/select-kqldatabase.png)

2. Sie werden aufgefordert, einen **Namen** für die KQL-Datenbank anzugeben.

   ![Abbildung des Namens der KQL-Datenbank](./Images/name-kqldatabase.png)

3. Geben Sie der KQL-Datenbank einen Namen, den Sie sich gut merken können (z. B. **MyStockData**), und klicken Sie auf **Erstellen**.

4. Wählen Sie im Bereich **Datenbankdetails** das Bleistiftsymbol aus, um die Verfügbarkeit in OneLake zu aktivieren.

   ![Abbildung der Aktivierung von OneLake](./Images/enable-onelake-availability.png)

5. Wählen Sie das Feld mit **Beispieldaten** aus den Optionen von ***Rufen Sie zunächst Daten ab*** aus.
 
   ![Abbildung der Auswahloptionen mit hervorgehobenen Beispieldaten](./Images/load-sample-data.png)

6. Wählen Sie das Feld **Metrikanalysen für Automobilbranche** aus den Optionen für Beispieldaten aus.

   ![Abbildung der Auswahl von Analysedaten für das Lab](./Images/create-sample-data.png)

7. Nachdem die Daten geladen wurden, können wir überprüfen, ob die KQL-Datenbank mit Daten gefüllt ist.

   ![Daten werden in die KQL-Datenbank geladen](./Images/choose-automotive-operations-analytics.png)

7. Sobald die Daten geladen wurden, überprüfen Sie, ob die Daten in die KQL-Datenbank geladen wurden. Wählen Sie dazu die Auslassungspunkte rechts neben der Tabelle aus, navigieren Sie zur **Abfragetabelle** und wählen Sie **100 Datensätze anzeigen** aus.

    ![Abbildung der Auswahl der obersten 100 Dateien aus der Tabelle RawServerMetrics](./Images/rawservermetrics-top-100.png)

   > **HINWEIS:** Wenn Sie dies zum ersten Mal ausführen, kann die Zuordnung von Computeressourcen mehrere Sekunden dauern.

    ![Abbildung der 100 Datensätze aus den Daten](./Images/explore-with-kql-take-100.png)


## Szenario
In diesem Szenario erstellen Sie ein Echtzeitdashboard basierend auf von Microsoft Fabric bereitgestellten Beispieldaten, mit dem Sie Daten in einer Vielzahl von Methoden anzeigen, eine Variable erstellen und diese Variable verwenden können, um die Dashboardbereiche miteinander zu verknüpfen, sodass Sie einen tieferen Einblick in die Vorgänge innerhalb des Quellsystems erhalten. In diesem Modul verwenden wir das Dataset „NY Taxi“, um die aktuellen Details von Fahrten nach Bezirk und ähnlichem zu untersuchen.

1. Navigieren Sie zu **Echtzeitanalyse**, und wählen Sie dann auf der Fabric-Hauptseite die Option **Echtzeitdashboard** aus.

    ![Wählen Sie Echtzeitdashboards aus.](./Images/select-real-time-dashboard.png)

1. Drücken Sie auf die Schaltfläche **Neue Kachel hinzufügen**.

```kusto

Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 

```
3. Drücken Sie die Schaltfläche „Ausführen“, und vergewissern Sie sich, dass Ihre Abfrage keine Fehler aufweist.
4. Wählen Sie rechts im Bereich die Registerkarte **Visualformatierung** aus, und füllen Sie den ***Kachelnamen*** und ***Visualtyp*** aus.

   ![Abbildung der Kachel „Visualformatierung“.](./Images/visual-formatting-tile.png)

