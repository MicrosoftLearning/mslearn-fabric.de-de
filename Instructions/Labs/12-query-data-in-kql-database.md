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

6. Wählen Sie das Feld **Metrikanalysen** aus den Optionen für Beispieldaten aus.

   ![Abbildung der Auswahl von Analysedaten für das Lab](./Images/create-sample-data.png)

7. Sobald die Daten geladen wurden, überprüfen Sie, ob die Daten in die KQL-Datenbank geladen wurden. Wählen Sie dazu die Auslassungspunkte rechts neben der Tabelle aus, navigieren Sie zur **Abfragetabelle**, und wählen Sie **Alle 100 Datensätze anzeigen** aus.

    <div><video controls src="./Images/check-kql-sample-dataset.mp4" muted="false" autoplay loop></video></div>

> **HINWEIS**: Wenn Sie dies zum ersten Mal ausführen, kann die Zuordnung von Computeressourcen mehrere Sekunden dauern.

## Szenario
In diesem Szenario sind Sie ein Analyst, der mit der Abfrage eines Beispieldatasets beauftragt ist, das Sie in der Fabric-Umgebung implementieren werden.



Eine Kusto-Abfrage ist eine Möglichkeit, Daten zu lesen, zu verarbeiten und die Ergebnisse anzuzeigen. Die Abfrage ist in Nur-Text geschrieben, der einfach zu verarbeiten ist. Eine Kusto-Abfrage kann eine oder mehrere Anweisungen enthalten, die Daten als eine Tabelle oder ein Diagramm anzeigen.

Eine Table-Anweisung verfügt über einige Operatoren, die Tabellendaten verwenden. Jeder Operator verwendet eine Tabelle als Eingabe und gibt eine Tabelle als Ausgabe zurück. Operatoren werden durch ein Pipe-Symbol (|) verbunden. Daten wechseln von einem Operator zum anderen. Jeder Operator ändert die Daten in irgendeiner Weise und gibt sie weiter.

Sie können sich den Prozess wie einen Trichter vorstellen, bei dem Sie mit einer vollständigen Tabelle mit Daten beginnen. Jeder Operator filtert, sortiert oder fasst die Daten zusammen. Die Reihenfolge der Operatoren ist wichtig, da sie nacheinander arbeiten. Am Ende des Trichters erhalten Sie eine endgültige Ausgabe.

Die Operatoren sind KQL-spezifisch, können jedoch SQL oder anderen Sprachen ähneln.