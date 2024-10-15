---
lab:
  title: Sichern von Daten in einem Data Warehouse
  module: Get started with data warehouses in Microsoft Fabric
---

# Sichern von Daten in einem Data Warehouse

Gemeinsam steuern Microsoft Fabric-Berechtigungen und granulare SQL-Berechtigungen den Warehouse-Zugriff und Benutzerberechtigungen. In dieser Übung sichern Sie Daten mithilfe präziser Berechtigungen, Sicherheit auf Spaltenebene, Sicherheit auf Zeilenebene und dynamischer Datenmaske.

Dieses Lab dauert ungefähr **45** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung abzuschließen.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Wählen Sie auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com) die Option **Data Warehouse mit Synapse** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-empty-workspace.png)

## Erstellen eines Data Warehouse

Erstellen Sie als Nächstes ein Data Warehouse im eben von Ihnen erstellten Arbeitsbereich. Die Startseite von „Data Warehouse mit Synapse“ enthält eine Verknüpfung zum Erstellen eines neuen Warehouse:

1. Erstellen Sie auf der Startseite **Synapse Data Warehouse** ein neues **Warehouse** mit einem Namen Ihrer Wahl.

    Nach einer Minute wird ein neues Warehouse erstellt:

    ![Screenshot eines neuen Warehouse](./Images/new-empty-data-warehouse.png)

## Anwenden dynamischer Datenmaskierung auf Spalten in einer Tabelle

Dynamische Datenmaskierungsregeln werden auf einzelne Spalten auf Tabellenebene angewendet, sodass alle Abfragen von der Maskierung betroffen sind. Benutzerinnen und Benutzer, die keine explizite Berechtigung zum Anzeigen vertraulicher Daten haben, sehen maskierte Werte in den Abfrageergebnissen, während Benutzerinnen und Benutzer mit einer expliziten Berechtigung zum Anzeigen der Daten diese ungeschwärzt sehen. Es gibt vier Arten von Masken: Standard, E-Mail, Zufall und benutzerdefinierte Zeichenfolge. In dieser Übung wenden Sie eine Standardmaske, eine E-Mail-Maske und eine Maske für benutzerdefinierte Zeichenfolge an.

1. Wählen Sie in Ihrem neuen Warehouse die Kachel **T-SQL** aus, und ersetzen Sie den SQL-Standardcode durch die folgende T-SQL-Anweisung, um eine Tabelle zu erstellen und Daten einzufügen und anzuzeigen.  Die in der Anweisung `CREATE TABLE` angewendeten Masken führen folgende Aktionen aus:

    ```sql
    CREATE TABLE dbo.Customer
    (   
        CustomerID INT NOT NULL,   
        FirstName varchar(50) MASKED WITH (FUNCTION = 'partial(1,"XXXXXXX",0)') NULL,     
        LastName varchar(50) NOT NULL,     
        Phone varchar(20) MASKED WITH (FUNCTION = 'default()') NULL,     
        Email varchar(50) MASKED WITH (FUNCTION = 'email()') NULL   
    );
    GO
    --Users restricted from seeing masked data will see the following when they query the table
    --The FirstName column shows the first letter of the string with XXXXXXX and none of the last characters.
    --The Phone column shows xxxx
    --The Email column shows the first letter of the email address followed by XXX@XXX.com.
    
    INSERT dbo.Customer (CustomerID, FirstName, LastName, Phone, Email) VALUES
    (29485,'Catherine','Abel','555-555-5555','catherine0@adventure-works.com'),
    (29486,'Kim','Abercrombie','444-444-4444','kim2@adventure-works.com'),
    (29489,'Frances','Adams','333-333-3333','frances0@adventure-works.com');
    GO

    SELECT * FROM dbo.Customer;
    GO
    ```

2. Verwenden Sie die Schaltfläche **&#9655; Ausführen**, um das SQL-Skript auszuführen, das eine neue Tabelle mit dem Namen **Customer** im **dbo**-Schema des Data Warehouse erstellt.

3. Erweitern Sie dann im Bereich **Explorer** **Schemas** > **dbo** > **Tabellen**, und überprüfen Sie, ob die Tabelle **Customer** erstellt wurde. Die SELECT-Anweisung gibt unmaskierte Daten zurück, da Sie als Arbeitsbereichsadmin angemeldet sind, der unmaskierte Daten sehen kann.

4. Stellen Sie eine Verbindung mit einem Testbenutzerkonto her, das Mitglied der Arbeitsbereichsrolle **Betrachter** ist, und führen Sie die folgende T-SQL-Anweisung aus.

    ```sql
    SELECT * FROM dbo.Customer;
    GO
    ```

    Dem Testbenutzerkonto wurde keine UNMASK-Berechtigung erteilt, sodass Daten, die für die Spalten „FirstName“, „Phone“ und „Email“ zurückgegeben werden, maskiert sind, da diese Spalten in der `CREATE TABLE`-Anweisung mit einer Maske definiert wurden.

5. Stellen Sie wieder eine Verbindung als Arbeitsbereichsadmin her, und führen Sie die folgenden T-SQL-Dateien aus, um für das Testbenutzerkonto die Maske von den Daten zu entfernen.

    ```sql
    GRANT UNMASK ON dbo.Customer TO [testUser@testdomain.com];
    GO
    ```

6. Stellen Sie erneut eine Verbindung mit dem Testbenutzerkonto her und führen Sie die folgende T-SQL-Anweisung aus.

    ```sql
    SELECT * FROM dbo.Customer;
    GO
    ```

    Die Daten werden unmaskiert zurückgegeben, da dem Testbenutzer die `UNMASK`-Berechtigung erteilt wurde.

## Anwenden von Sicherheitsmaßnahmen auf Zeilenebene

Die Sicherheit auf Zeilenebene (Row-Level Security, RLS) kann verwendet werden, um den Zugriff auf Zeilen basierend auf der Identität oder Rolle der Benutzerin bzw. des Benutzers zu beschränken, die oder der eine Abfrage ausführt.  In dieser Übung beschränken Sie den Zugriff auf Zeilen, indem Sie eine Sicherheitsrichtlinie und ein sicherheitsrelevantes Prädikat erstellen, das als Inlinetabellenwertfunktion definiert ist.

1. Wählen Sie im Warehouse, das Sie in der letzten Übung erstellt haben, die Dropdownliste **Neue SQL-Abfrage** aus.  Wählen Sie in der Dropdownliste unter der Kopfzeile **Leer** die Option **Neue SQL-Abfrage** aus.

2. Erstellen Sie eine Tabelle, und fügen Sie Daten ein. Damit Sie die Sicherheit auf Zeilenebene in einem späteren Schritt testen können, ersetzen Sie „testuser1@mydomain.com“ durch einen Benutzernamen aus Ihrer Umgebung, und ersetzen Sie „testuser2@mydomain.com“ durch Ihren Benutzernamen.
    ```sql
    CREATE TABLE dbo.Sales  
    (  
        OrderID INT,  
        SalesRep VARCHAR(60),  
        Product VARCHAR(10),  
        Quantity INT  
    );
    GO
     
    --Populate the table with 6 rows of data, showing 3 orders for each test user. 
    INSERT dbo.Sales (OrderID, SalesRep, Product, Quantity) VALUES
    (1, 'testuser1@mydomain.com', 'Valve', 5),   
    (2, 'testuser1@mydomain.com', 'Wheel', 2),   
    (3, 'testuser1@mydomain.com', 'Valve', 4),  
    (4, 'testuser2@mydomain.com', 'Bracket', 2),   
    (5, 'testuser2@mydomain.com', 'Wheel', 5),   
    (6, 'testuser2@mydomain.com', 'Seat', 5);  
    GO
   
    SELECT * FROM dbo.Sales;  
    GO
    ```

3. Verwenden Sie die Schaltfläche **&#9655; Ausführen**, um das SQL-Skript auszuführen, das eine neue Tabelle mit dem Namen **Sales** im **dbo**-Schema des Data Warehouse erstellt.

4. Erweitern Sie dann im Bereich **Explorer** **Schemas** > **dbo** > **Tabellen**, und überprüfen Sie, ob die Tabelle **Sales** erstellt wurde.
5. Erstellen Sie ein neues Schema, ein sicherheitsrelevantes Prädikat, das als Funktion definiert ist, und eine Sicherheitsrichtlinie.  

    ```sql
    --Create a separate schema to hold the row-level security objects (the predicate function and the security policy)
    CREATE SCHEMA rls;
    GO
    
    --Create the security predicate defined as an inline table-valued function. A predicate evalutes to true (1) or false (0). This security predicate returns 1, meaning a row is accessible, when a row in the SalesRep column is the same as the user executing the query.

    --Create a function to evaluate which SalesRep is querying the table
    CREATE FUNCTION rls.fn_securitypredicate(@SalesRep AS VARCHAR(60)) 
        RETURNS TABLE  
    WITH SCHEMABINDING  
    AS  
        RETURN SELECT 1 AS fn_securitypredicate_result   
    WHERE @SalesRep = USER_NAME();
    GO 
    
    --Create a security policy to invoke and enforce the function each time a query is run on the Sales table. The security policy has a Filter predicate that silently filters the rows available to read operations (SELECT, UPDATE, and DELETE). 
    CREATE SECURITY POLICY SalesFilter  
    ADD FILTER PREDICATE rls.fn_securitypredicate(SalesRep)   
    ON dbo.Sales  
    WITH (STATE = ON);
    GO
6. Use the **&#9655; Run** button to run the SQL script
7. Then, in the **Explorer** pane, expand **Schemas** > **rls** > **Functions**, and verify that the function has been created.
7. Confirm that you're logged as another user by running the following T-SQL.

    ```sql
    SELECT USER_NAME();
    GO
5. Query the sales table to confirm that row-level security works as expected. You should only see data that meets the conditions in the security predicate defined for the user you're logged in as.

    ```sql
    SELECT * FROM dbo.Sales;
    GO

## Implement column-level security

Column-level security allows you to designate which users can access specific columns in a table. It is implemented by issuing a GRANT statement on a table specifying a list of columns and the user or role that can read them. To streamline access management, assign permissions to roles in lieu of individual users. In this exercise, you will create a table, grant access to a subset of columns on the table, and test that restricted columns are not viewable by a user other than yourself.

1. In the warehouse you created in the earlier exercise, select the **New SQL Query** dropdown.  Under the dropdown under the header **Blank**, select **New SQL Query**.  

2. Create a table and insert data into the table.

 ```sql
    CREATE TABLE dbo.Orders
    (   
        OrderID INT,   
        CustomerID INT,  
        CreditCard VARCHAR(20)      
        );
    GO

    INSERT dbo.Orders (OrderID, CustomerID, CreditCard) VALUES
    (1234, 5678, '111111111111111'),
    (2341, 6785, '222222222222222'),
    (3412, 7856, '333333333333333');
    GO

    SELECT * FROM dbo.Orders;
    GO
 ```

3. Verweigern Sie die Berechtigung zum Anzeigen einer Spalte in der Tabelle. Die T-SQL-Anweisung verhindert, dass <testuser@mydomain.com> die Spalte „CreditCard“ in der Tabelle „Orders“ sieht. Ersetzen Sie in der `DENY`-Anweisung unten testuser@mydomain.com durch einen Benutzernamen in Ihrem System, der über Betrachterberechtigungen für den Arbeitsbereich verfügt.

 ```sql
DENY SELECT ON dbo.Orders (CreditCard) TO [testuser@mydomain.com];
 ```

4. Testen Sie die Sicherheit auf Spaltenebene, indem Sie sich bei Fabric mit dem Benutzerkonto anmelden, dem die Auswahlberechtigung verweigert wird.

5. Fragen Sie die Tabelle „Orders“ ab, um zu bestätigen, dass die Sicherheit auf Spaltenebene wie erwartet funktioniert. Die folgende Abfrage gibt nur die Spalten „OrderID“ und „CustomerID“ zurück und nicht die Spalte „CreditCard“.  

    ```sql
    SELECT * FROM dbo.Orders;
    GO

    --You'll receive an error because access to the CreditCard column has been restricted.  Try selecting only the OrderID and CustomerID fields and the query will succeed.

    SELECT OrderID, CustomerID from dbo.Orders
    ```

## Konfigurieren von granularen SQL-Berechtigungen mit T-SQL

Fabric Warehouse verfügt über ein Berechtigungsmodell, mit dem Sie den Zugriff auf Daten auf Arbeitsbereichsebene und auf Elementebene steuern können. Wenn Sie eine genauere Kontrolle darüber benötigen, was Benutzerinnen und Benutzer mit sicherungsfähigen Daten in einem Fabric Warehouse tun können, können Sie die standardmäßigen DCL-Befehle (SQL Data Control Language) `GRANT`, `DENY` und `REVOKE` verwenden. In dieser Übung erstellen Sie Objekte, sichern sie mithilfe von `GRANT` und `DENY`, und führen dann Abfragen aus, um die Auswirkung der Anwendung präziser Berechtigungen anzuzeigen.

1. Wählen Sie im Warehouse, das Sie in der vorherigen Übung erstellt haben, die Dropdownliste **Neue SQL-Abfrage** aus.  Wählen Sie unter der Kopfzeile **Leer** die Option **Neue SQL-Abfrage** aus.  

2. Erstellen Sie eine gespeicherten Prozedur und eine Tabelle.

 ```
    CREATE PROCEDURE dbo.sp_PrintMessage
    AS
    PRINT 'Hello World.';
    GO
  
    CREATE TABLE dbo.Parts
    (
        PartID INT,
        PartName VARCHAR(25)
    );
    GO
    
    INSERT dbo.Parts (PartID, PartName) VALUES
    (1234, 'Wheel'),
    (5678, 'Seat');
    GO  
    
    --Execute the stored procedure and select from the table and note the results you get because you're a member of the Workspace Admin. Look for output from the stored procedure on the 'Messages' tab.
      EXEC dbo.sp_PrintMessage;
    GO
    
    SELECT * FROM dbo.Parts
    GO
  ```

3. Wenden Sie als Nächstes `DENY SELECT`-Berechtigungen auf die Tabelle für ein Benutzerkonto an, das Mitglied der Rolle Arbeitsbereichsbetrachter ist und `GRANT EXECUTE` auf die Prozedur für dasselbe Benutzerkonto.

 ```sql
    DENY SELECT on dbo.Parts to [testuser@mydomain.com];
    GO

    GRANT EXECUTE on dbo.sp_PrintMessage to [testuser@mydomain.com];
    GO

 ```

4. Melden Sie sich bei Fabric mit dem Benutzerkonto an, das Sie in den Anweisungen DENY und GRANT oben anstelle von [testuser@mydomain.com] angegeben haben. Testen Sie dann die granularen Berechtigungen, die Sie eben angewendet haben, indem Sie die gespeicherte Prozedur ausführen und die Tabelle abfragen.  

 ```sql
    EXEC dbo.sp_PrintMessage;
    GO
    
    SELECT * FROM dbo.Parts
 ```

## Bereinigen von Ressourcen

In dieser Übung haben Sie dynamische Datenmaskierung auf Spalten in einer Tabelle angewendet, die Sicherheit auf Zeilenebene angewendet, die Sicherheit auf Spaltenebene implementiert und granulare SQL-Berechtigungen mithilfe von T-SQL konfiguriert.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.
