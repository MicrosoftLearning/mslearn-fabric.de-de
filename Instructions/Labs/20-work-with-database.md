---
lab:
  title: Arbeiten mit SQL-Datenbank in Microsoft Fabric
  module: Get started with SQL Database in Microsoft Fabric
---

# Arbeiten mit SQL-Datenbank in Microsoft Fabric

Die SQL-Datenbank in Microsoft Fabric ist eine entwicklerfreundliche Transaktionsdatenbank, die auf der Azure SQL-Datenbank basiert und es Ihnen ermöglicht, Ihre operative Datenbank in Fabric einfach zu erstellen. Eine SQL-Datenbank in Fabric verwendet das SQL-Datenbank-Modul als Azure SQL-Datenbank.

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

1. Geben Sie in einen neuen Abfrage-Editor den folgenden T-SQL-Code ein und führen Sie ihn aus.

    ```sql
   SELECT 
        c.FirstName,
        c.LastName,
        soh.OrderDate,
        soh.SubTotal
    FROM 
        SalesLT.Customer c
    INNER JOIN 
        SalesLT.SalesOrderHeader soh ON c.CustomerID = soh.CustomerID
    ORDER BY 
        soh.OrderDate DESC;
    ```

    Diese Abfrage ruft eine Liste von Kunden und Kundinnen zusammen mit ihren Bestelldaten und Teilergebnissen ab, sortiert nach dem Bestelldatum in absteigender Reihenfolge. 

1. Schließen Sie alle Registerkarten der Abfrage.

## Integrieren von Daten in externe Datenquellen

Sie integrieren externe Daten über Feiertage in den Verkaufsauftrag. Anschließend identifizieren Sie Verkaufsaufträge, die mit öffentlichen Feiertagen übereinstimmen, und geben Einblicke in die Auswirkungen von Feiertagen auf Vertriebsaktivitäten.

1. Navigieren Sie zu **Startseite** und wählen Sie **Neue Abfrage**.

1. Geben Sie in den neuen leeren Abfragebereich den folgenden T-SQL-Code ein und führen Sie ihn aus.

    ```sql
    CREATE TABLE SalesLT.PublicHolidays (
        CountryOrRegion NVARCHAR(50),
        HolidayName NVARCHAR(100),
        Date DATE,
        IsPaidTimeOff BIT
    );
    ```

    Diese Abfrage erstellt die `SalesLT.PublicHolidays`-Tabelle in Vorbereitung auf den nächsten Schritt.

1. Geben Sie in einen neuen Abfrage-Editor den folgenden T-SQL-Code ein und führen Sie ihn aus.

    ```sql
    INSERT INTO SalesLT.PublicHolidays (CountryOrRegion, HolidayName, Date, IsPaidTimeOff)
    VALUES
        ('Canada', 'Victoria Day', '2024-02-19', 1),
        ('United Kingdom', 'Christmas Day', '2024-12-25', 1),
        ('United Kingdom', 'Spring Bank Holiday', '2024-05-27', 1),
        ('United States', 'Thanksgiving Day', '2024-11-28', 1);
    ```
    
    In diesem Beispiel fügt diese Abfrage Feiertage für Kanada, das Vereinigte Königreich und die USA für das Jahr 2024 in die `SalesLT.PublicHolidays`-Tabelle ein.    

1. Geben Sie in einem neuen oder vorhandenen Abfrage-Editor den folgenden T-SQL-Code ein, und führen Sie ihn aus.

    ```sql
    -- Insert new addresses into SalesLT.Address
    INSERT INTO SalesLT.Address (AddressLine1, City, StateProvince, CountryRegion, PostalCode, rowguid, ModifiedDate)
    VALUES
        ('123 Main St', 'Seattle', 'WA', 'United States', '98101', NEWID(), GETDATE()),
        ('456 Maple Ave', 'Toronto', 'ON', 'Canada', 'M5H 2N2', NEWID(), GETDATE()),
        ('789 Oak St', 'London', 'England', 'United Kingdom', 'EC1A 1BB', NEWID(), GETDATE());
    
    -- Insert new orders into SalesOrderHeader
    INSERT INTO SalesLT.SalesOrderHeader (
        SalesOrderID, RevisionNumber, OrderDate, DueDate, ShipDate, Status, OnlineOrderFlag, 
        PurchaseOrderNumber, AccountNumber, CustomerID, ShipToAddressID, BillToAddressID, 
        ShipMethod, CreditCardApprovalCode, SubTotal, TaxAmt, Freight, Comment, rowguid, ModifiedDate
    )
    VALUES
        (1001, 1, '2024-12-25', '2024-12-30', '2024-12-26', 1, 1, 'PO12345', 'AN123', 1, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '123 Main St'), 'Ground', '12345', 100.00, 10.00, 5.00, 'New Order 1', NEWID(), GETDATE()),
        (1002, 1, '2024-11-28', '2024-12-03', '2024-11-29', 1, 1, 'PO67890', 'AN456', 2, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '123 Main St'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '456 Maple Ave'), 'Air', '67890', 200.00, 20.00, 10.00, 'New Order 2', NEWID(), GETDATE()),
        (1003, 1, '2024-02-19', '2024-02-24', '2024-02-20', 1, 1, 'PO54321', 'AN789', 3, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '456 Maple Ave'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), 'Sea', '54321', 300.00, 30.00, 15.00, 'New Order 3', NEWID(), GETDATE()),
        (1004, 1, '2024-05-27', '2024-06-01', '2024-05-28', 1, 1, 'PO98765', 'AN321', 4, (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), 'Ground', '98765', 400.00, 40.00, 20.00, 'New Order 4', NEWID(), GETDATE());
    ```

    Dieser Code fügt der Datenbank neue Adressen und Bestellungen hinzu und simuliert fiktive Aufträge aus verschiedenen Ländern.

1. Geben Sie in einem neuen oder vorhandenen Abfrage-Editor den folgenden T-SQL-Code ein, und führen Sie ihn aus.

    ```sql
    SELECT DISTINCT soh.SalesOrderID, soh.OrderDate, ph.HolidayName, ph.CountryOrRegion
    FROM SalesLT.SalesOrderHeader AS soh
    INNER JOIN SalesLT.Address a
        ON a.AddressID = soh.ShipToAddressID
    INNER JOIN SalesLT.PublicHolidays AS ph
        ON soh.OrderDate = ph.Date AND a.CountryRegion = ph.CountryOrRegion
    ```

    Nehmen Sie sich einen Moment Zeit, um die Ergebnisse zu beobachten, und notieren Sie, wie die Abfrage Verkaufsaufträge identifiziert, die mit Feiertagen in den jeweiligen Ländern übereinstimmen. Dies kann wertvolle Einblicke in Bestellmuster und potenzielle Auswirkungen von Feiertagen auf Vertriebsaktivitäten bieten.

1. Schließen Sie alle Registerkarten der Abfrage.

## Schützen von Daten

Angenommen, eine bestimmte Benutzergruppe sollte nur Zugriff auf Daten aus den USA haben, um Berichte zu generieren.

Erstellen wir eine Ansicht basierend auf der zuvor verwendeten Abfrage und fügen einen Filter hinzu.

1. Geben Sie in den neuen leeren Abfragebereich den folgenden T-SQL-Code ein und führen Sie ihn aus.

    ```sql
    CREATE VIEW SalesLT.vw_SalesOrderHoliday AS
    SELECT DISTINCT soh.SalesOrderID, soh.OrderDate, ph.HolidayName, ph.CountryOrRegion
    FROM SalesLT.SalesOrderHeader AS soh
    INNER JOIN SalesLT.Address a
        ON a.AddressID = soh.ShipToAddressID
    INNER JOIN SalesLT.PublicHolidays AS ph
        ON soh.OrderDate = ph.Date AND a.CountryRegion = ph.CountryOrRegion
    WHERE a.CountryRegion = 'United Kingdom';
    ```

1. Geben Sie in einem neuen oder vorhandenen Abfrage-Editor den folgenden T-SQL-Code ein, und führen Sie ihn aus.

    ```sql
    -- Create the role
    CREATE ROLE SalesOrderRole;
    
    -- Grant select permission on the view to the role
    GRANT SELECT ON SalesLT.vw_SalesOrderHoliday TO SalesOrderRole;
    ```

    Alle Benutzenden, die als Mitglied zur Rolle `SalesOrderRole` hinzugefügt werden, haben nur Zugriff auf die gefilterte Ansicht. Wenn ein Benutzer bzw. eine Benutzerin in dieser Rolle versucht, auf andere Benutzende zuzugreifen, erhält er bzw. sie eine Fehlermeldung, die in etwa so aussieht:

    ```
    Msg 229, Level 14, State 5, Line 1
    The SELECT permission was denied on the object 'ObjectName', database 'DatabaseName', schema 'SchemaName'.
    ```

> **Weitere Informationen**: Weitere Informationen zu anderen auf der Plattform verfügbaren Komponenten finden Sie unter [Was ist Microsoft Fabric?](https://learn.microsoft.com/fabric/get-started/microsoft-fabric-overview) in der Microsoft Fabric-Dokumentation.

In dieser Übung haben Sie Daten in einer SQL-Datenbank in Microsoft Fabric erstellt, abgefragt und gesichert.

## Bereinigen von Ressourcen

Wenn Sie Ihre Datenbank vollständig erkundet haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.
