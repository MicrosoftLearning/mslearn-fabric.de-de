---
lab:
  title: Arbeiten mit der API für GraphQL in Microsoft Fabric
  module: Get started with GraphQL in Microsoft Fabric
---

# Arbeiten mit der API für GraphQL in Microsoft Fabric

Die Microsoft Fabric-API für GraphQL ist eine Datenzugriffsebene, die eine schnelle und effiziente Abfrage mehrerer Datenquellen mit einer weit verbreiteten und vertrauten API-Technologie ermöglicht. Mit der API können Sie die Besonderheiten der Back-End-Datenquellen abstrahieren, damit Sie sich auf Ihre Anwendungslogik konzentrieren und alle Daten bereitstellen können, die ein Client in einem einzigen Aufruf benötigt. GraphQL verwendet eine einfache Abfragesprache und leicht zu manipulierende Resultsets wodurch die Zeit, die Anwendungen für den Zugriff auf Ihre Daten in Fabric benötigen, minimiert wird.

Dieses Lab dauert ungefähr **30** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric) unter `https://app.fabric.microsoft.com/home?experience=fabric`.
1. Wählen Sie in der Menüleiste auf der linken Seite **Neuer Arbeitsbereich** aus.
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Erstellen einer SQL-Datenbank mit Beispieldaten

Da Sie nun über einen Arbeitsbereich verfügen, ist es an der Zeit, eine SQL-Datenbank zu erstellen.

1. Wählen Sie im Fabric-Portal im linken Bedienfeld **+ Neues Element** aus.
1. Navigieren Sie zum Abschnitt **Daten speichern** und wählen Sie die **SQL-Datenbank** aus.
1. Geben Sie **AdventureWorksLT** als Datenbanknamen ein und wählen Sie **Erstellen**.
1. Sobald Sie Ihre Datenbank erstellt haben, können Sie Beispieldaten von der Karte **Beispieldaten** in Ihre Datenbank laden.

    Nach einer Minute wird die Datenbank mit Beispieldaten für Ihr Szenario aufgefüllt.

    ![Screenshot einer neuen Datenbank, die mit Beispieldaten geladen wurde.](./Images/sql-database-sample.png)

## Abfragen einer SQL-Datenbank

Der SQL-Abfrage-Editor bietet Unterstützung für IntelliSense, Codevervollständigung, Syntaxhervorhebung, clientseitige Analyse und Validierung. Sie können Anweisungen der Datendefinitionssprache (Data Definition Language, DDL), Datenbearbeitungssprache (Data Manipulation Language, DML) und Datenkontrollsprache (Data Control Language, DCL) ausführen.

1. Navigieren Sie auf der **AdventureWorksLT**-Datenbankseite zu **Home** und wählen Sie **Neue Abfrage**.
1. Geben Sie in den neuen leeren Abfragebereich den folgenden T-SQL-Code ein und führen Sie ihn aus.

    ```sql
    SELECT 
        p.Name AS ProductName,
        pc.Name AS CategoryName,
        p.ListPrice
    FROM 
        SalesLT.Product p
    INNER JOIN 
        SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
    ORDER BY 
    p.ListPrice DESC;
    ```
    
    Diese Abfrage verknüpft die Tabellen `Product` und `ProductCategory`, um die Produktnamen, ihre Kategorien und ihre Listenpreise anzuzeigen, sortiert nach Preis in absteigender Reihenfolge.

1. Schließen Sie alle Registerkarten der Abfrage.

## Erstellen einer API für GraphQL

Zunächst richten Sie einen GraphQL-Endpunkt ein, um die Verkaufsauftragsdaten verfügbar zu machen. Mit diesem Endpunkt können Sie Verkaufsaufträge basierend auf verschiedenen Parametern abfragen, z. B. Datum, Kundschaft und Produkt.

1. Navigieren Sie im Fabric-Portal zu Ihrem Arbeitsbereich und wählen Sie **+ Neues Element** aus.
1. Navigieren Sie zum Abschnitt **Daten entwickeln** und wählen Sie **API für GraphQL** aus.
1. Geben Sie einen Namen an, und wählen Sie **Erstellen** aus.
1. Wählen Sie auf der Hauptseite Ihrer API für GraphQL die Option **Datenquelle auswählen** aus.
1. Wenn Sie aufgefordert werden, eine Konnektivitätsoption auszuwählen, wählen Sie **Mit Fabric-Datenquellen mit Single-Sign-On-Authentifizierung (SSO) verbinden**.
1. Wählen Sie auf der Seite **Daten auswählen, die Sie verbinden möchten** die zuvor erstellte `AdventureWorksLT`-Datenbank aus.
1. Wählen Sie **Verbinden**.
1. Wählen Sie auf der Seite **Daten auswählen** die Tabelle `SalesLT.Product`. 
1. Zeigen Sie die Daten in der Vorschau an und wählen Sie **Laden**.
1. Wählen Sie **Endpunkt kopieren** und notieren Sie den öffentlichen URL-Link. Dies ist nicht erforderlich, aber hier können Sie Ihre API-Adresse kopieren.

## Deaktivieren von Mutationen

Nachdem unsere API erstellt wurde, möchten wir nur die Verkaufsdaten für Lesevorgänge in diesem Szenario verfügbar machen.

1. Erweitern Sie im **Schemaexplorer** Ihrer API für GraphQL die Option **Mutationen**.
1. Wählen Sie das Feld **...** (Ellipse) neben jeder Mutation und wählen Sie **Deaktivieren**.

Dadurch werden Änderungen oder Aktualisierungen der Daten über die API verhindert. Dies bedeutet, dass die Daten schreibgeschützt sind, und Benutzende können die Daten nur anzeigen oder abfragen, aber keine Änderungen daran vornehmen.

## Abfragen von Daten mithilfe von GraphQL

Lassen Sie uns nun die Daten mit GraphQL abfragen, um alle Produkte zu finden, deren Name mit *„HL Road Frame.“* beginnt.

1. Geben Sie im GraphQL-Abfrage-Editor die folgende Abfrage ein und führen Sie sie aus.

```json
query {
  products(filter: { Name: { startsWith: "HL Road Frame" } }) {
    items {
      ProductModelID
      Name
      ListPrice
      Color
      Size
      ModifiedDate
    }
  }
}
```

In dieser Abfrage ist „Produkte“ der Haupttyp, und enthält Felder für `ProductModelID`, `Name`, `ListPrice`, `Color`, `Size` und `ModifiedDate`. Diese Abfrage gibt eine Liste von Produkten zurück, deren Name mit *„HL Road Frame.“* beginnt.

> **Weitere Informationen**: Siehe [Was ist Microsoft Fabric API für GraphQL?](https://learn.microsoft.com/fabric/data-engineering/api-graphql-overview) in der Microsoft Fabric-Dokumentation, um weitere Informationen über andere in der Plattform verfügbare Komponenten zu erfahren.

In dieser Übung haben Sie Daten aus einer SQL-Datenbank mithilfe von GraphQL in Microsoft Fabric erstellt, abgefragt und verfügbar gemacht.

## Bereinigen von Ressourcen

Wenn Sie Ihre Datenbank vollständig erkundet haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.

