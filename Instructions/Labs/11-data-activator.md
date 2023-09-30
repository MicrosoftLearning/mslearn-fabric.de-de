---
lab:
  title: Verwenden vom Data Activator in Fabric
  module: Get started with Data Activator in Microsoft Fabric
---

# Verwenden vom Data Activator in Fabric

In Microsoft Fabric stellt ein Data Warehouse eine relationale Datenbank für umfangreiche Analysen bereit. Im Gegensatz zum standardmäßigen schreibgeschützten SQL-Endpunkt für Tabellen, die in einem Lakehouse definiert sind, bietet ein Data Warehouse vollständige SQL-Semantik, einschließlich der Möglichkeit zum Einfügen, Aktualisieren und Löschen von Daten in den Tabellen.

Dieses Lab dauert ungefähr **30** Minuten.

> **Hinweis:** Sie benötigen eine Microsoft Fabric-Lizenz, um diese Übung durchführen zu können. Weitere Informationen zum Aktivieren einer kostenlosen Fabric-Testlizenz finden Sie unter [Erste Schritte mit Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial). Dazu benötigen Sie ein *Schul-* , *Geschäfts-* oder Unikonto von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Melden Sie sich bei [Microsoft Fabric](https://app.fabric.microsoft.com) unter `https://app.fabric.microsoft.com` an, und wählen Sie **Power BI** aus.
2. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
3. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
4. Beim Öffnen Ihres neuen Arbeitsbereichs sollte dieser wie im Folgenden gezeigt leer sein:

    ![Screenshot: Leerer Arbeitsbereich in Power BI](./Images/new-workspace.png)
