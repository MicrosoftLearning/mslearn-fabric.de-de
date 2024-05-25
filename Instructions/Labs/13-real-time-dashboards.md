---
lab:
  title: Echtzeit-Dashboards in Microsoft Fabric
  module: Get Started with Real-Time Dashboards in Microsoft Fabric
---

# Erste Schritte mit Real-Time-Dashboards in Microsoft Fabric

Echtzeitdashboards ermöglichen es Ihnen, Einblicke aus Microsoft Fabric mithilfe der Kusto-Abfragesprache (KQL) zu gewinnen, um sowohl strukturierte als auch unstrukturierte Daten abzurufen und in Diagrammen, Scatterplots, Tabellen usw. in Panels zu rendern, die eine Verknüpfung ähnlich wie Slicer in Power BI ermöglichen. 

Dieses Lab dauert ungefähr **25** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion.](https://learn.microsoft.com/fabric/get-started/fabric-trial) um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Wählen Sie auf der [Microsoft Fabric-Startseite.](https://app.fabric.microsoft.com) **Real-Time Intelligence** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*). Sie können auch einen vorhandenen Arbeitsbereich verwenden, um ein Echtzeitdashboard zu erstellen.
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

In dieser Übung verwenden Sie die Real-Time Intelligence in Fabric, um ein Real-Time-Dashboard zu erstellen. Real-Time Intelligence bietet bequem ein Beispiel-Dataset, mit dem Sie die Funktionen von Real-Time Intelligence erkunden können. Sie verwenden diese Beispieldaten, um die KGL-/SQL-Abfragen und -Abfragesets zu erstellen, die Echtzeitdaten analysieren und eine zusätzliche Verwendung in nachgelagerten Prozessen ermöglichen.

## Erstellen von Echtzeitdashboards

1. Wählen Sie innerhalb der **Real-Time Intelligence** das Feld **Real-Time-Dashboard** aus.

   ![Abbildung der Auswahl des Real-Time-Dashboards](./Images/create-real-time-dashboard.png)

2. Sie werden aufgefordert, das Real-Time-Dashboard zu **benennen**.

   ![Abbildung der Benennung des Real-Time-Dashboards.](./Images/new-real-time-dashboard.png)

3. Weisen Sie dem Real-Time-Dashboard einen Namen zu, den Sie sich merken, z. B. etwas, das auf Ihrer primären Quelle basiert, und drücken Sie **Erstellen**.

4. Wählen Sie im Bereich **Datenbankdetails** das Bleistiftsymbol aus, um die Verfügbarkeit in OneLake zu aktivieren.

   [ ![Bild vom Aktivieren von Onelake.](./Images/real-time-dashboard-details.png)](./Images/real-time-dashboard-details-large.png#lightbox)

## Hinzufügen einer Datenquelle

Datenquellen dienen als wiederverwendbare Verweise auf bestimmte Datenbanken oder Abfragen innerhalb desselben Arbeitsbereichs wie das Real-Time-Dashboard, sodass verschiedene Kacheln unterschiedliche Datenquellen für ihre Datenanforderungen nutzen können.

1. Wählen Sie die Registerkarte **Verwalten** und dann **Neue Datenquelle** auf der ***Menüleiste*** aus.
1. Wählen Sie im Bereich **Datenquellen** die Schaltfläche **+ Hinzufügen** aus.

    [ ![Neue Datenquelle zum Real-Time-Dashboard hinzufügen.](./Images/add-data-source-to-real-time-dashboard-large.png) ](./Images/add-data-source-to-real-time-dashboard-large.png#lightbox)

1. wählen Sie aus einer der beiden Hauptoptionen, nämlich **OneLake-Datenhubs** oder **Azure Data Explorer** aus.

    ![wählen Sie eine Datenquelle für das Real-Time-Dashboard aus.](./Images/data-source-options-real-time-dashboards.png)

1. Wählen Sie die **Datenquelle** aus, die Ihren Anforderungen entspricht, und wählen Sie dann die Schaltfläche **Verbinden** aus.

    [![Wählen Sie die entsprechende Datenquelle aus.](./Images/select-onelake-data-hub.png)](./Images/select-onelake-data-hub-large.png#lightbox)

    > **Hinweis** Nachdem Sie eine Verbindung mit einer Datenquelle hergestellt haben, können Sie weitere Datenquellen innerhalb des ausgewählten Speicherorts bestätigen und erstellen.

1. Bestätigen Sie ihre **Datenquellen**-Verbindung im Bereich **Neue Datenquelle** erstellen, und wählen Sie **Erstellen** aus.

    [ ![Datenbank unter „Neue Datenquelle erstellen“ bestätigen.](./Images/conected-now-create-datasource.png) ](./Images/conected-now-create-datasource-large.png#lightbox)

1. An diesem Punkt sollten Sie die Auslassungszeichen **...** rechts neben **Seite n** auswählen und die **Seite umbenennen** mit einen Namen, der für die Verwendung der Kachel geeignet ist.
1. Wählen Sie **+ Kachel hinzufügen** aus

    [![Benennen Sie die Seite um und fügen Sie Kacheln hinzu.](./Images/rename-page-add-tile.png)](./Images/rename-page-add-tile-large.png#lightbox)

1. Sie werden an den **Kachelabfragebereich** weitergeleitet, in dem Sie Parameter hinzufügen und Basisabfragen abrufen können, um Ihre Kachel zu unterstützen. 

    [ ![Abfragen des Fensters und Hinzufügen eines neuen Datenquellenbereichs.](./Images/write-query-for-tile.png) ](./Images/write-query-for-tile-large.png#lightbox)

    > **Hinweis**: Sie haben die Möglichkeit, im Dropdownfenster im selben Fenster eine neue Datenquelle hinzuzufügen. Diese Quelle kann sich innerhalb Ihres persönlichen Arbeitsbereichs oder eines beliebigen Arbeitsbereichs befinden, auf dem Sie möglicherweise eine andere KQL-Datenbank in einem Evenhouse gespeichert haben, auf den Sie Zugriff haben.

## Schreiben von Abfragen

Da Kacheln im Real-Time-Dashboard dir Kusto Query Language Codeausschnitte verwenden, um Daten abzurufen und visuelle Elemente zu rendern. Jede Kachel/Abfrage kann ein einzelnes visuelles Element unterstützen.

1. Innerhalb jeder Kachel können Sie aus **Copilot** schreiben oder einfügen, wenn Sie sie an eine neue oder vorhandene Kachel anheften und dann entsprechend Ihren Anforderungen ändern. Aus einer einfachen Abfrage können wir eine Karten-Visualisierung erstellen, die Größen auf der Karte basierend auf der Anzahl der Fahrräder verwendet.

```kusto

['Bike-count']
BikepointID, Latitude, Longitude, No_Bikes

```

## Erstellen von Visualisierungen

Sobald Sie mit der Visualisierung zufrieden sind, wählen Sie einfach **Änderungen übernehmen** aus und fügen Sie dann entweder zusätzliche Visualisierungen hinzu, um Ihr Real-Time-Dashboard zu unterstützen, oder führen Sie zusätzliche Schritte wie **Parameter** oder **Zeitpläne aus**.

   [![Erstellen Sie eine Visualisierung aus einer KQL-Abfrage.](./Images/create-visual-in-tiles.png)](./Images/create-visual-in-tiles-large.png#lightbox)

Sobald die Änderungen übernommen werden, sehen Sie die Daten und können dann Anpassungen für die Lesbarkeit und das Verständnis ihrer Benutzer vornehmen.

   [![Übernommene Änderungen an der Fahrradvisualisierungskarte.](./Images/first-published-visual.png)](./Images/first-published-visual-large.png#lightbox)

Sie können weiterhin **neue Kacheln** erstellen, die Tabelleninformationen und Visualisierungsinformationen enthalten, um das Verständnis Ihrer Benutzercommunity zu erleichtern. Sie haben auch die Möglichkeit, wie weiter oben gezeigt, **Seite[n] hinzuzufügen**, **Neue Datenquelle[n]**. Als Nächstes konzentrieren wir uns auf das Hinzufügen eines Parameters zur Unterstützung beim Navigieren und Verringern der Menge an Informationen, die einem Benutzer angezeigt werden.

## Parameter hinzufügen
Parameter verbessern die Effizienz des Dashboardrenderings und ermöglichen die Verwendung von Filterwerten in der frühesten Phase des Abfrageprozesses. Die Einbeziehung von Parametern in die Abfrage, die mit Ihren Kacheln verknüpft ist, aktiviert Filterfunktionen. Ein Parameter kann über ein Dashboard hinweg verwendet werden, und mehrere Parameter können die in den zugrunde liegenden Visualisierungen dargestellten Daten filtern, einschließlich Tabellen.

Das Erstellen eines Parameters beginnt einfach folgendermaßen: 

1. Wählen Sie oben im Menü die Taste Neuer Parameter aus. Der Bereich Parameter wird geöffnet.
1. Wählen Sie oben im rechten Bereich + Hinzufügen aus.

    [ ![Neuen Parameter hinzufügen.](./Images/add-new-parameter.png) ](./Images/add-new-parameter-large.png#lightbox)

1. Füllen Sie die relevanten Eigenschaften für Ihren Parameter aus.

    [ ![Konfigurieren der Parametereinstellungen.](./Images/configure-parameter.png) ](./Images/configure-parameter-large.png#lightbox)

1. Eines der wichtigsten Features eines Parameters ist die Möglichkeit, **eine Abfrage hinzuzufügen**, um dem Benutzer nur Optionen zu geben, die für die zugrunde liegenden Informationen relevant sind.

    ![Fügen Sie der Parameterauswahl eine Abfrage hinzu.](./Images/add-lookup-query.png)

1. Wählen Sie Fertig aus, um den Parameter zu erstellen.

    [ ![Schließen Sie die Konfiguration ab und wählen Sie die Option in den Parametereinstellungen aus.](./Images/complete-parameter-settings.png) ](./Images/complete-parameter-settings-large.png#lightbox)

### Parametereigenschaften

| Feld            | Beschreibung |
|------------------|-------------|
| **Bezeichnung**        | Der Name des Parameters, der auf dem Dashboard oder auf der Bearbeitungskarte angezeigt wird. |
| **Parametertyp** | Eine der folgenden Typen auswählen: <ul><li>Single selection (Einfachauswahl): Im Filter kann als Eingabe für den Parameter nur ein Wert ausgewählt werden.</li><li>Mehrfachauswahl: Im Filter können als Eingaben für den Parameter mehrere Werte ausgewählt werden.</li><li>Zeitbereich: Ermöglicht die Erstellung zusätzlicher Parameter zum Filtern von Abfragen und Dashboards basierend auf der Zeit. Jedes Dashboard verfügt standardmäßig über eine standardmäßige Zeitbereichsauswahl.</li><li>Freitext: Ermöglicht Benutzern das Eingeben oder Einfügen eines Werts in das Filterfeld ohne vorgefüllte Werte, wobei zuletzt verwendete Werte beibehalten werden.</li></ul> |
| **Beschreibung**  | Eine optionale Beschreibung des Parameters. |
| **Variablenname** | Der Name, der für den Parameter innerhalb der Abfrage verwendet wird. |
| **Datentyp**    | Der Typ der Daten, den die Parameterwerte darstellen. |
| **Auf Seiten anzeigen** | Seiten, auf denen der Parameter angezeigt wird, mit einer Option zum Auswählen aller Seiten. |
| **Quelle**       | Der Ursprung der Parameterwerte, der folgender sein kann: <ul><li>Feste Werte: Statische Filterwerte, die manuell eingegeben wurden.</li><li>Abfrage: Dynamische Werte, die mit einer KQL-Abfrage eingeführt wurden.</li></ul> |
| **Wert „Alle auswählen“ hinzufügen** | Gilt für einzelne und mehrere Auswahlparametertypen - diese Option ruft Daten für alle Parameterwerte ab und muss in die Abfrage für die Funktionalität integriert werden. |
| **Standardwert** | Der Standardwert des Filters, der beim anfänglichen Rendern des Dashboards festgelegt wird. |

6. Fügen Sie den Parameter zu den einzelnen Abfragen in den Kacheln hinzu und wählen Sie dann **Änderungen übernehmen** aus.

**Vor der KQL-Abfrage**
```kusto
//Add the street parameter to each tile's query
['bike-count']
| where No_Bikes > 0
| project BikepointID, Latitude, Longitude, No_Bikes

```

**Nach der KQL-Abfrage**
```kusto
//Add the street parameter to each tile's query
['bike-count']
| where No_Bikes > 0 and Street == street
| project BikepointID, Latitude, Longitude, No_Bikes

```
   [ ![aktualisieren Sie jede Abfrage in den Kacheln, um die Parameter einzuschließen.](./Images/update-each-query.png) ](./Images/update-each-query-large.png#lightbox)

## Aktivieren der automatischen Aktualisierung

Die automatische Aktualisierung ist eine Funktion, welche die automatische Aktualisierung von Dashboarddaten ermöglicht, ohne dass eine manuelle Seite neu geladen oder eine Aktualisierungsschaltfläche gedrückt wird. Die anfängliche Häufigkeit der automatischen Aktualisierung kann von einem Datenbank-Editor konfiguriert werden. Sowohl Editoren als auch Viewer haben die Möglichkeit, die tatsächliche automatische Aktualisierungsrate während der Dashboardanzeige zu ändern. Datenbank-Editoren verfügen über die Berechtigung, eine minimale Aktualisierungsrate einzurichten, um übermäßige Belastung für das Cluster zu verringern. Sobald diese Mindestrate festgelegt ist, können Datenbankbenutzer keine Aktualisierungsrate unter dem angegebenen Minimum mehr auswählen. Dadurch wird sichergestellt, dass die Leistung des Systems beibehalten wird, ohne die Ressourcen zu überlasten.

1. Wählen Sie die Registerkarte „Verwalten“ > „Automatische Aktualisierung“ aus.

    [ ![aktivieren Sie das Feature für die automatische Aktualisierung.](./Images/enable-auto-refresh.png) ](./Images/enable-auto-refresh-large.png#lightbox)

1. Schalten Sie die Option um, sodass die automatische Aktualisierung aktiviert ist.
1. Wählen Sie Werte für Minimum time interval (Minimales Zeitintervall) und Default refresh rate (Standardaktualisierungsrate) aus.
1. Wählen Sie Anwenden aus, und speichern sie dann das Dashboard.

    [ ![Aktivieren Sie die automatische Aktualisierung und legen Sie Intervalle fest.](./Images/enable-and-configure-refresh-rate.png) ](./Images/enable-and-configure-refresh-rate-large.png#lightbox)

## Bereinigen von Ressourcen

In dieser Übung haben Sie eine KQL-Datenbank erstellt und ein Beispieldataset für Abfragen eingerichtet. Danach haben Sie die Daten mit KQL und SQL abgefragt. Wenn Sie die Untersuchung Ihrer KQL-Datenbank abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.
1. Wählen Sie auf der Leiste auf der linken Seite das **Symbol** für Ihren Arbeitsbereich aus.
2. Wählen Sie auf der Symbolleiste im Menü „...“ die Option **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.

