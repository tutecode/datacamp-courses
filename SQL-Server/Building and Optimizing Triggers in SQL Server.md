# Building and Optimizing Triggers in SQL Server

### Course Description

Auditing your SQL Server database and keeping data integrity can be a challenging task for DBAs and database developers. SQL Server triggers are special types of stored procedures designed to help you achieve consistency and integrity of your database. This course will teach you how to work with triggers and use them in real-life examples. Specifically, you will learn about the use cases and limitations of triggers and get practice designing and implementing them. You will also learn to optimize triggers to fit your specific needs.

# Introduction to Triggers

## Introduction

Hi! My name is Florin Angelescu and I will be your instructor for this course on Building and Optimizing SQL Server Triggers. After completing this course, you will feel confident implementing and using triggers in your day-to-day tasks. Let's get started!

## What is a trigger?

A trigger is a special type of stored procedure that is automatically executed when events (like data modifications) occur on the database server.

![](https://i.imgur.com/cgYAUwe.png)


## Types of trigger (based on T-SQL commands)

In terms of Transact-SQL (T-SQL) commands, there are three types of trigger in SQL Server:
- **Data Manipulation Language** (DML) triggers are executed when a user or process modifies data through an `INSERT`, `UPDATE`, or `DELETE` statement. These triggers are associated with statements related to tables or views.
- **Data Definition Language** (DDL) triggers fire in response to statements executed at the database or server level, like `CREATE`, `ALTER`, or `DROP`.
- **Logon** triggers fire in response to `LOGON` events when a user's session is established. We will cover all these trigger types in more detail in later lessons.

## Types of trigger (based on behavior)

Another way to classify triggers is based on their behavior. A trigger can behave differently in relation to the statement that fires it, resulting in two types of triggers. The first type, an `AFTER` trigger, is used when you want to execute a piece of code after the initial statement that fires the trigger. An example use case of this type of trigger is to rebuild an index after a large insert of data into a table. Another example is using a trigger to send alerts when `UPDATE` statements are run against the database. You'll see more examples as you progress through the course.

## Types of trigger (based on behavior)

The second trigger type is the `INSTEAD OF` trigger. As its name suggests, an `INSTEAD OF` trigger will not perform the initial operation, but will execute custom code instead. Some examples of using this behavior are to prevent inserting data into tables, prevent updates or deletions, or even prevent tables from being dropped. You can notify the database administrator of suspicious behavior while also preventing any changes. As with the `AFTER` triggers, there are more possible use cases than the examples provided here. Now, let's see how we actually create a trigger.

## Trigger definition (with AFTER)

Because a trigger is a SQL Server object, we add a new one by using the `CREATE` statement. In this case, we use the `CREATE TRIGGER` statement to add a new DML trigger. After naming the trigger, we have to link it to an existing object; in this example, it's the "Products" table. The next part focuses on setting the trigger behavior. We use an `AFTER` trigger for this example. We also need to specify the event that fires the trigger. It can be either an `INSERT`, `UPDATE`, or `DELETE` statement. Let's stick with an `INSERT` for now. The AS keyword marks the beginning of our trigger workflow. The actions performed by the trigger are mentioned after this keyword.

![](https://i.imgur.com/aRWOPJG.png)

## Trigger definition (with INSTEAD OF)

Here, another example of a DML trigger uses an INSTEAD OF statement to prevent deletions from the table. Any attempt to remove rows from the table will fail due to the use of INSTEAD OF.

![](https://i.imgur.com/KjRvr1K.png)

## AFTER vs. INSTEAD OF

Understanding the difference between the `AFTER` and `INSTEAD OF` behaviors is very important. When the `AFTER` keyword is used, the trigger will perform the defined actions only after the event that fired it finishes. When `INSTEAD OF` is used, the event that fired the trigger will not run, and only the trigger actions will be performed.

![](https://i.imgur.com/2qeiy5i.png)

### Types of trigger

Data Manipulation Language (DML) triggers are executed when a user or process tries to modify data in a given table. A trigger can be fired in response to these statements.

What are the three DML statements that can be used to fire triggers?

* `DELETE` / `INSERT` / `UPDATE`

### Creating your first trigger

You have been hired by the company Fresh Fruit Delivery to help secure their database and ensure data integrity. The company sells fresh fruit to other online shops, and they use several tables to keep track of stock and placed orders.

One of their tables (Discounts) specifies the discount amount that shops receive when placing large orders. A deletion of several hundred rows happened at some point in the past when one of their employees removed some orders by mistake. They need a new trigger on the Discounts table to prevent DELETE statements related to the table, and this is where you can help.

* Create a new trigger on the Discounts table.
* Use the trigger to prevent DELETE statements.

```
-- Create a new trigger that fires when deleting data
CREATE TRIGGER PreventDiscountsDelete
ON Discounts
-- The trigger should fire instead of DELETE
INSTEAD OF DELETE
AS
	PRINT 'You are not allowed to delete data from the Discounts table.';
```

### Practicing creating triggers

The Fresh Fruit Delivery company needs help creating a new trigger called OrdersUpdatedRows on the Orders table.

This trigger will be responsible for filling in a historical table (OrdersUpdate) where information about the updated rows is kept.

A historical table is often used in practice to store information that has been altered in the original table. In this example, changes to orders will be saved into OrdersUpdate to be used by the company for auditing purposes.

* Create the new trigger for the Orders table.
* Set the trigger to be fired only after UPDATE statements.

```
-- Set up a new trigger
CREATE TRIGGER OrdersUpdatedRows
ON Orders
-- The trigger should fire after UPDATE statements
AFTER UPDATE
-- Add the AS keyword before the trigger body
AS
	-- Insert details about the changes to a dedicated table
	INSERT INTO OrdersUpdate(OrderID, OrderDate, ModifyDate)
	SELECT OrderID, OrderDate, GETDATE()
	FROM inserted;
```

Remember that a trigger is always attached to another SQL object, like a table.

# How DML triggers are used

## Why should we use DML triggers?

Developers and database administrators can create and use triggers for a multitude of purposes. The main reason for using triggers is to initiate actions when manipulating data (inserting, modifying, or deleting information). Sometimes the manipulation of data needs to be prevented, and this can also be done with the use of triggers. Another use case often seen in practice is using triggers for tracking data changes and even database object changes. Database admins also use triggers to track user actions and to secure the database by protecting it from unwanted changes.

## Deciding between AFTER and INSTEAD OF

As mentioned earlier, it's important to understand the difference between the AFTER and INSTEAD OF trigger types so you know when to use each. From a trigger definition perspective, the two types of triggers look almost the same. But in practice, they will have very different outcomes when fired.

![](https://i.imgur.com/oSxDnnC.png)

## Deciding between AFTER and INSTEAD OF

The outcome of the trigger execution is highly dependent on the keyword you choose (AFTER or INSTEAD OF). The AFTER trigger adds new statements to the initial one, while the INSTEAD OF trigger prevents the initial statement from executing. This difference will influence the use cases for each type of trigger.

![](https://i.imgur.com/DW9USof.png)

## AFTER trigger usage example

One example of using an AFTER trigger is for a large insert of data into a sales table. Once that data gets inserted, a cleansing step should run to remove or repair any unwanted information. The cleansing step will be started by the trigger. When the cleansing step finishes, a report with the results will be generated. This report should be analyzed by a database administrator, so the trigger will then send an email to the responsible people. This is all part of the trigger definition.

![](https://i.imgur.com/M7QmyDV.png)

## INSTEAD OF trigger usage example

Now let's look at an example where using an INSTEAD OF trigger is appropriate. Imagine you have a table containing light bulb information and stock details for a sales platform. The "Power" column values have changed for some models, and an update is initiated to change the information in the table. There are still some bulb models in stock that have the old power values, however, and they shouldn't be updated. An INSTEAD OF trigger can help you deal with this more complex situation.

![](https://i.imgur.com/xu6Gjln.png)

## INSTEAD OF trigger usage example

The correct approach is to update the characteristics only for the models that don't have the old version in stock.

![](https://i.imgur.com/j9MFEjE.png)

## INSTEAD OF trigger usage example

The new models need to be in the table too, but as new rows instead of updated ones.

![](https://i.imgur.com/tFpJR2Y.png)

### When to use triggers

Triggers are a good way to execute additional actions when changes occur in your database.

Triggers can also be used to prevent changes and execute different actions instead.

Which of the following could be valid use cases for triggers?

* To send an email to the Sales team when a new order is added to the Orders table
* To copy the modified rows to a history table when an update occurs on the Products table
* To deny the creation of a new database in SQL Server

Triggers can be used for data manipulation, sending notifications, or preventing server/database modifications.

### Creating a trigger to keep track of data changes

The Fresh Fruit Delivery company needs to keep track of any new items added to the Products table. You can do this by using a trigger.

The new trigger will store the name, price, and first introduced date for new items into a ProductsHistory table.

* Create the ProductsNewItems trigger on the Products table.
* Set the trigger to fire when data is inserted into the table.

```
-- Create a new trigger
CREATE TRIGGER ProductsNewItems
ON Products
AFTER INSERT
AS
	-- Add details to the history table
	INSERT INTO ProductsHistory(Product, Price, Currency, FirstAdded)
	SELECT Product, Price, Currency, GETDATE()
	FROM inserted;
```

# Trigger alternatives

So far we've talked about the types of triggers and the scenarios that best fit their use. But as with any feature, triggers are not always the best solution in SQL Server. In this lesson we will compare triggers with other objects and take a look at the appropriate situations for using them.

## Triggers vs. stored procedures

The first comparison of triggers is with regular stored procedures. The trigger is a stored procedure as well, but a special kind. What makes it so special? A trigger is fired when a defined database or server event occurs. Regular stored procedures run when called explicitly. A trigger runs only when the event that fires it is starting. Triggers don't allow input parameters or transaction statements (like BEGIN TRANSACTION or COMMIT). They also cannot return output values. On the other hand, regular stored procedures allow input parameters, transaction statements, and the possibility to return output.

![](https://i.imgur.com/FZznEHU.png)

## Triggers vs. stored procedures

These considerations impact the real-life use cases for triggers and regular stored procedures. We use triggers for audit and to enforce integrity in most cases, and we use regular stored procedures for more general tasks.

## Triggers vs. computed columns

The second comparison of triggers is with computed columns. Computed columns are a good way to automate calculation of the values contained by some columns. Computed column values are determined based on values from other columns, but only from the same table. This limitation can be overcome by using triggers. A trigger can use columns from other tables as well to calculate values. While this calculation will be done with INSERT or UPDATE statements when using a trigger, for a calculated column it will be part of the table definition.

![](https://i.imgur.com/sZEnWIC.png)

## Example of a computed column

In this example, the column "TotalAmount" is a computed column. Its value comes from the multiplication of the "Quantity" and "Price" columns from the same table. But if those two columns were not part of the same table, we wouldn't be able to do the calculation using a computed column.

![](https://i.imgur.com/0MHPPLm.png)

## Using a trigger as a computed column

In that case, we could replace the computed column with this trigger definition. Here, the Price column is not part of the "SalesWithoutPrice" table. The trigger determines the "TotalAmount" by using the "Price" column from another table ("Products" in this example).

![](https://i.imgur.com/rTjsYIK.png)

### Triggers vs. stored procedures

One important task when you take ownership of an existing database is to familiarize yourself with the objects that comprise the database.

This task includes getting to know existing procedures, functions, and triggers.

You find the following objects in the Fresh Fruit Delivery database:

* The company uses a regular stored procedure, `MonthlyOrders`, for reporting purposes. The stored procedure sums up order amounts for each product every month.

* The trigger `CustomerDiscountHistory` is used to keep a history of the changes that occur in the `Discounts` table. The trigger is fired when updates are made to the Discounts table, and it stores the old and new values from the `Discount` column into the table `DiscountsHistory`.

* Run an update on the Discounts table (this will fire the CustomerDiscountHistory trigger).
* Get all the rows from DiscountsHistory to verify the outcome.

```
-- Run an update for some of the discounts
UPDATE Discounts
SET Discount = Discount + 1
WHERE Discount <= 5;

-- Verify the trigger ran successfully
SELECT * FROM DiscountsHistory;
```

![](https://i.imgur.com/UfwKVPj.png)

**Question**

Execute the MonthlyOrders regular stored procedure, using EXECUTE MonthlyOrders.

Then, try to execute the CustomerDiscountHistory trigger using

```
UPDATE Discounts SET Discount = Discount + 1 WHERE Discount <= 5
```

What conclusions can be drawn from the execution results and the step performed earlier?

* Triggers can only be fired by the corresponding event, while regular stored procedures can be executed explicitly when needed.

You're learning fast—you identified the difference between triggers and regular stored procedures that helps you to choose the right one to use in practice.

### Triggers vs. computed columns

While continuing your analysis of the database, you find two other interesting objects:

* The table `SalesWithPrice` has a column that calculates the `TotalAmount` as `Quantity * Price`. This is done using a **computed column** which uses columns from the same table for the calculation.

* The trigger `SalesCalculateTotalAmount` was created on the `SalesWithoutPrice` table. The `Price` column is not part of the `SalesWithoutPrice` table, so a computed column cannot be used for the `TotalAmount`. The trigger overcomes this limitation by using the `Price` column from the `Products` table.

Insert new data into SalesWithPrice and then run a SELECT from the same table to verify the outcome.

```
-- Add the following rows to the table
INSERT INTO SalesWithPrice (Customer, Product, Price, Currency, Quantity)
VALUES ('Fruit Mag', 'Pomelo', 1.12, 'USD', 200),
	   ('VitaFruit', 'Avocado', 2.67, 'USD', 400),
	   ('Tasty Fruits', 'Blackcurrant', 2.32, 'USD', 1100),
	   ('Health Mag', 'Kiwi', 1.42, 'USD', 100),
	   ('eShop', 'Plum', 1.1, 'USD', 500);

-- Verify the results after adding the new rows
SELECT * FROM SalesWithPrice;
```

![](https://i.imgur.com/ZHaTQY8.png)

Insert new data into SalesWithoutPrice and then run a SELECT from the same table to verify the outcome.

```
-- Add the following rows to the table
INSERT INTO SalesWithoutPrice (Customer, Product, Currency, Quantity)
VALUES ('Fruit Mag', 'Pomelo', 'USD', 200),
	   ('VitaFruit', 'Avocado', 'USD', 400),
	   ('Tasty Fruits', 'Blackcurrant', 'USD', 1100),
	   ('Health Mag', 'Kiwi', 'USD', 100),
	   ('eShop', 'Plum', 'USD', 500);

-- Verify the results after the INSERT
SELECT * FROM SalesWithoutPrice;
```

![](https://i.imgur.com/2Mgh3Kd.png)

**Question**

The previous step used both a computed column and a trigger to calculate the TotalAmount value automatically. From a user perspective, there was no difference, but from a technical perspective, there is one.

What is the major limitation of computed columns that can be easily overcome with the use of triggers?

* A computed column cannot use columns from other tables for the calculation.

You figured that out fast. If you bump into this limitation in your real-life work, try using a trigger to save your day!

# AFTER triggers (DML)

## Definition and properties

An `AFTER` trigger is used for DML statements to perform an additional set of actions (one or more). This set of actions is performed after the DML event that fired the trigger is finished. The DML events that can make use of an `AFTER` trigger are `INSERT`, `UPDATE`, and `DELETE` statements run against tables or views. The set of actions is comprised of T-SQL code and is defined when the trigger is created.

## AFTER trigger prerequisites

Let's break down the list of database objects and events involved when working with triggers. To make use of an `INSERT`, `UPDATE`, or `DELETE` statement we need to have a table or view to work with. Remember the fact that a trigger needs to be attached to a database object? This means a table is needed for the trigger creation process. We will use the "Products" table for this example. Here is a preview of its columns and contents.

![](https://i.imgur.com/jxsIFbA.png)

## AFTER trigger prerequisites

Once you have a table defined for the scope of the trigger, you should create a description of what you are trying to achieve with the trigger. In this example, we want to keep some details of products that are not sold anymore. These products will be removed from the "Products" table, but their details will be kept in a "RetiredProducts" table for financial accounting reasons. You can see that we already have the answer to the question, "What kinds of DML statements should fire the trigger?" Since rows will be removed from the Products table, the answer is `DELETE` statements. The description of the trigger will also help us in deciding what actions will be performed by the trigger. In this case, the trigger will save information about the deleted rows (from the "Products" table) to the "RetiredProducts" table. The trigger should have a uniquely identifying name&mdash;for this example, it will be "TrackRetiredProducts".

## AFTER trigger prerequisites summary

To recap, our new trigger will fire whenever rows are removed from the `Products` table. The deleted rows' information will be saved to the "RetiredProducts" table. With all this information in hand, we can now create the AFTER trigger.

![](https://i.imgur.com/rpPliY9.png)

## AFTER trigger definition

To create a trigger, we use the `CREATE TRIGGER` statement followed by the trigger name. We attach the trigger to the "Products" table. We choose the trigger type (an `AFTER` trigger in this case) and specify the DML statement that will fire the trigger (`DELETE`). And then we start the section that defines the actions to be performed by the trigger. Notice that we are not getting the information from the "Products" table, but from a table called "deleted".

![](https://i.imgur.com/ICAIGv7.png)

## "inserted" and "deleted" tables

DML triggers use two special tables: "deleted" and "inserted". These tables are automatically created by SQL Server and you can make use of them in your trigger actions. Depending on the operation you are performing, they will hold different information.

## "inserted" and "deleted" tables

The "inserted" table will store the values of the new rows for INSERT and UPDATE statements. For DELETE statements, this table is empty. The "deleted" table will store the values of the modified rows for UPDATE statements or the values of the removed rows for DELETE statements. The "deleted" table is empty for INSERT statements.

## The complete AFTER trigger

The "deleted" table holds the information we need for the rows that will be removed from "Products". We get the relevant information directly from this table.

![](https://i.imgur.com/du3TP3d.png)

### Tracking retired products

As shown in the example from the video, Fresh Fruit Delivery needs to keep track of any retired products in a dedicated history table (RetiredProducts).

You are asked to create a new trigger that fires when rows are removed from the Products table.

The information about the removed rows will be saved into the RetiredProducts table.

* Create the TrackRetiredProducts trigger on the Products table.
* Set the trigger to fire after rows are deleted from the table.

```
-- Create the trigger
CREATE TRIGGER TrackRetiredProducts
ON Products
AFTER DELETE
AS
	INSERT INTO RetiredProducts (Product, Measure)
	SELECT Product, Measure
	FROM deleted;
```

### The TrackRetiredProducts trigger in action

Once you've created a trigger, it's always a good idea to see if it performs as expected.

The company's request for the trigger created earlier was based on a real need: they want to retire several products from their offering. This means you can check the trigger in action.

* Remove retired items from the Products table and check the output from the RetiredProducts table.

```
-- Remove the products that will be retired
DELETE FROM Products
WHERE Product IN ('Cloudberry', 'Guava', 'Nance', 'Yuzu');

-- Verify the output of the history table
SELECT * FROM RetiredProducts;
```

![](https://i.imgur.com/rVsH1NC.png)

### Practicing with AFTER triggers

Fresh Fruit Delivery company is happy with your services, and they've decided to keep working with you.

You have been given the task to create new triggers on some tables, with the following requirements:

* Keep track of canceled orders (rows deleted from the Orders table). Their details will be kept in the table CanceledOrders upon removal.

* Keep track of discount changes in the table Discounts. Both the old and the new values will be copied to the DiscountsHistory table.

* Send an email to the Sales team via the SendEmailtoSales stored procedure when a new order is placed.

1. Create a new trigger on the Orders table to keep track of any canceled orders.

```
-- Create a new trigger for canceled orders
CREATE TRIGGER KeepCanceledOrders
ON Orders
AFTER DELETE
AS 
	INSERT INTO CanceledOrders
	SELECT * FROM deleted;
```

2. Create a new trigger on the Discounts table to keep track of discount value changes.

```
-- Create a new trigger to keep track of discounts
CREATE TRIGGER CustomerDiscountHistory
ON Discounts
AFTER UPDATE
AS
	-- Store old and new values into the `DiscountsHistory` table
	INSERT INTO DiscountsHistory (Customer, OldDiscount, NewDiscount, ChangeDate)
	SELECT i.Customer, d.Discount, i.Discount, GETDATE()
	FROM inserted AS i
	INNER JOIN deleted AS d ON i.Customer = d.Customer;
```

3. Create the trigger NewOrderAlert to notify the Sales team when new orders are placed.

```
-- Notify the Sales team of new orders
CREATE TRIGGER NewOrderAlert
ON Orders
AFTER INSERT
AS
	EXECUTE SendEmailtoSales;
```

# INSTEAD OF triggers (DML)

In contrast with AFTER triggers, INSTEAD OF triggers can only be used for DML statements (not DDL). This is because they were designed to work with DML statements: INSERT, UPDATE, and DELETE.

## Definition and properties

An INSTEAD OF trigger will perform an additional set of actions when fired, in place of the event that fired the trigger. That event is not run when using an INSTEAD OF trigger. This is the main difference between the two trigger types. In terms of the DML statements that are able to fire an INSTEAD OF trigger, we have the same list: INSERT, UPDATE, and DELETE.

## INSTEAD OF trigger prerequisites

As before, let's explore the steps we would perform to create an `INSTEAD OF` trigger. For this example, we will use the "Orders" table that holds details of the orders placed by the Fresh Fruit Delivery company's customers.

![](https://i.imgur.com/5zNhHWO.png)

## INSTEAD OF trigger prerequisites

Following the same steps for our analysis, we know that our target table is the "Orders" table. The trigger we will create should prevent updates to existing entries in this table. This will ensure that placed orders cannot be modified;this is a rule enforced by the company. This means that the trigger will fire as a response to UPDATE statements. The chosen name for the trigger is "PreventOrdersUpdate". Having an informative name is important when creating triggers. It's good if the name can set some expectations about what the trigger is intended to achieve.

## INSTEAD OF trigger definition

Now that we have gathered all the needed details, we can start creating the trigger. The trigger definition is quite straightforward: do not allow row updates on the "Orders" table. You should already be familiar with the CREATE TRIGGER syntax from past examples. Here we use "PreventOrdersUpdate" for the trigger name. As we know from the preceding analysis, the trigger will be attached to the "Orders" table and it will be executed when UPDATE statements are run. We also want to inform the end user about the rule we set in place through the trigger, so we're going to use the RAISERROR syntax to throw an error message as output. The numbers after the error message represent the "severity" and the "state" of the thrown error. The severity of the error in this example is 16. This is the most common value; it means we are throwing a medium-level error. The state parameter is used to identify the error statement in the SQL code if it is used multiple times. We will be using the value 1 because the error is used only one time and can be easily identified in the SQL code. A further explanation of the `RAISERROR` function is outside the scope of this course.

![](https://i.imgur.com/9MWpUvI.png)

### Preventing changes to orders

Fresh Fruit Delivery needs to prevent changes from being made to the Orders table.

Any attempt to do so should not be permitted and an error should be shown instead.

* Create a new trigger on the Orders table.
* Set the trigger to prevent updates and return an error message instead.

```
-- Create the trigger
CREATE TRIGGER PreventOrdersUpdate
ON Orders
INSTEAD OF UPDATE
AS
	RAISERROR ('Updates on "Orders" table are not permitted.
                Place a new order to add new products.', 16, 1);
```

### PreventOrdersUpdate in action

Let's see what the newly created trigger does when you try to update some rows in the `Orders` table.

Run the following script to change the order quantity to 700 for order number 425:

```
UPDATE Orders SET Quantity = 700
WHERE OrderID = 425;
```

What happens when you run the code?

* The query results pane shows an error thrown by the new trigger.

The trigger is now preventing you from doing any updates to the Orders table.

### Creating the PreventNewDiscounts trigger

The company doesn't want regular users to add discounts. Only the Sales Manager should be able to do that.

To prevent such changes, you need to create a new trigger called PreventNewDiscounts.

The trigger should be attached to the Discounts table and prevent new rows from being added to the table.

```
-- Create a new trigger
CREATE TRIGGER PreventNewDiscounts
ON Discounts
INSTEAD OF INSERT
AS
	RAISERROR ('You are not allowed to add discounts for existing customers.
                Contact the Sales Manager for more details.', 16, 1);
```

# DDL triggers

So far, we've only talked about DML triggers. But as mentioned earlier, triggers also come in Data Definition Language (DDL) flavors. In this lesson, you are going to learn how to create DDL triggers and how they are different from DML triggers.

## Definition and properties

The simplest way to illustrate the specific properties of DDL triggers is to compare them with DML triggers. The biggest difference is indicated by the names of the trigger types. _DML triggers_ respond to events associated with DML statements: INSERT, UPDATE, and DELETE. On the other hand, DDL triggers respond to events associated with DDL statements like CREATE, ALTER, and DROP. While DML triggers can be used with both the AFTER and INSTEAD OF keywords, the DDL triggers accept only the AFTER keyword. DML triggers are attached to tables or views, while DDL triggers are created at the database or server level. Furthermore, we can make use of the "inserted" and "deleted" special tables when working with DML triggers, but there are no special tables for use with DDL triggers. That said, you can use your own defined tables to keep track of changes when using DDL triggers.

![](https://i.imgur.com/vYAGwUk.png)

## AFTER and FOR

When dealing with DDL triggers, you'll often see syntax like this. You might expect the AFTER keyword to come before CREATE_TABLE, but the FOR keyword is used instead.

![](https://i.imgur.com/MSB15Bv.png)

## AFTER and FOR

That's because for SQL Server triggers, the `FOR` and `AFTER` keywords have the same result. To minimize the potential for confusion, people often use the FOR keyword for DDL triggers and the AFTER keyword for DML triggers. We'll stick to that convention here, but be aware that both versions are correct and will behave in the same way.

## DDL trigger prerequisites

Let's do a quick analysis of an example DDL trigger. We'll create a trigger that will log information about table changes in a database. The trigger will be created at the database level. The changes will be written to a dedicated log table and will include details about the creation, alteration, and deletion of tables. The trigger name will be "TrackTableChanges".

## DDL trigger definition

Creating a DDL trigger is not much different from creating a DML trigger. We start with the CREATE TRIGGER syntax, only this time we attach the trigger at the database level. As mentioned, we use the FOR keyword for DDL triggers. After the keyword, we include the DDL statements that will fire the trigger. Note that although the DDL statements included in the trigger definition resemble the actual DDL syntax, an underscore is used between the words instead of a space. The statement that is going to perform the data insert comes next. This statement will log the information from the EVENTDATA function, as well as details about the user performing the change. The EVENTDATA function actually holds information about the event that runs and fires the trigger. Pretty cool, right?

![](https://i.imgur.com/uSddutQ.png)

## Preventing the triggering events for DML triggers

The `INSTEAD OF` statement cannot be used to prevent the triggering action from happening for DDL triggers. So are DDL triggers used only as AFTER triggers? The answer is no. You can define a trigger to roll back the statements that fired it. In this example, we don't want the users to remove tables from the database, so we set DROP_TABLE as the event firing the trigger. When the trigger fires we throw an error but also roll back the initial operation, so the table deletion does not take place. We now have a database trigger that prevents the firing event from happening. It's that simple.

![](https://i.imgur.com/W4zN6g4.png)

### Tracking table changes

You need to create a new trigger at the database level that logs modifications to the table TablesChangeLog.

The trigger should fire when tables are created, modified, or deleted.

* Create the new trigger following the company's requirements.

```
-- Create the trigger to log table info
CREATE TRIGGER TrackTableChanges
ON DATABASE
FOR CREATE_TABLE,
	ALTER_TABLE,
	DROP_TABLE
AS
	INSERT INTO TablesChangeLog (EventData, ChangedBy)
    VALUES (EVENTDATA(), USER);
```

### Using FOR in a trigger

What is the purpose of the FOR clause when used in a trigger definition?

* FOR is a synonym of AFTER and performs the trigger's set of actions after the triggering event finishes.

`AFTER` and `FOR` will have the same outcome when used in a trigger definition.

### Preventing table deletion

Fresh Fruit Delivery wants to prevent its regular employees from deleting tables from the database.

* Create a new trigger, PreventTableDeletion, to prevent table deletion.
* The trigger should roll back the firing statement, so the deletion does not happen.

```
-- Add a trigger to disable the removal of tables
CREATE TRIGGER PreventTableDeletion
ON DATABASE
FOR DROP_TABLE
AS
	RAISERROR ('You are not allowed to remove tables from this database.', 16, 1);
    -- Revert the statement that removes the table
    ROLLBACK;
```

Couldn’t have done it better myself. Remember that ROLLBACK is the way to prevent the firing action when designing database or server triggers.






# Logon triggers

## Definition and properties

Like other triggers, logon triggers perform a set of actions when fired. Their defining characteristic is that they are fired by `LOGON` events. A `LOGON` event occurs when a user logs on and creates a connection to SQL Server. The trigger is fired after the authentication phase (meaning after the username and password are checked), but before the user session is established (when the information from SQL Server becomes available for queries). This is important to know when using these triggers to audit and control server sessions.

## Logon trigger prerequisites

A logon trigger can only be attached at the server level, and the firing event can only be LOGON. When you create a logon trigger, you define the set of actions to be performed by the trigger when it's fired. We will just audit the logon sessions in this example, and we'll use "LogonAudit" as the trigger name. Now let's see how to create the trigger.

## Logon trigger definition

We start with the same syntax we used for the other triggers and pass in the name we chose. Logon triggers are attached at the server level, so we use the ALL SERVER syntax. When the event that fires the trigger starts, the trigger will be executed under the same credentials (username and password) as the firing event. Regular users don't usually have access to sensitive information like logon details, so we instruct the trigger to run under the "sa" account. This is a built-in administrator account that has full permissions on the server; running it under this account ensures that the trigger will be able to extract information about the logon details. This information will be stored into the "ServerLogonLog" table.

![](https://i.imgur.com/hyHLfpf.png)

## Logon trigger definition summary

To summarize, we are creating a trigger called "LogonAudit" at the server level. To avoid permission issues, the trigger will be executed as the "sa" account with administrator privileges. The trigger will be fired for LOGON events and it will store details about the user connecting to SQL Server into the "ServerLogonLog" table.

![](https://i.imgur.com/YsqT50L.png)

### Enhancing database security

Recently, several inconsistencies have been discovered on the Fresh Fruit Delivery company's database server.

The IT Security team does not have an auditing process to find out when users are connecting to the database and track breaking changes back to the responsible user.

You are asked to help the Security team by implementing a new trigger based on their requirements.

Due to the complexity of this request, you should build the `INSERT` statement in the first step and use it to create the trigger in the second step.

* Create the `INSERT` statement that is going to fill in user details in the ServerLogonLog table.
* Select only the details for the situation when the session_id is the same as the @@SPID (ID of the current user).

```
-- Save user details in the audit table
INSERT INTO ServerLogonLog (LoginName, LoginDate, SessionID, SourceIPAddress)
SELECT ORIGINAL_LOGIN(), GETDATE(), @@SPID, client_net_address
-- The user details can be found in SYS.DM_EXEC_CONNECTIONS
FROM SYS.DM_EXEC_CONNECTIONS WHERE session_id = @@SPID;
```

* Create a new trigger at the server level that fires for logon events and saves user details into ServerLogonLog table.

```
-- Create a trigger firing when users log on to the server
CREATE TRIGGER LogonAudit
-- Use ALL SERVER to create a server-level trigger
ON ALL SERVER WITH EXECUTE AS 'sa'
-- The trigger should fire after a logon
FOR LOGON
AS
	-- Save user details in the audit table
	INSERT INTO ServerLogonLog (LoginName, LoginDate, SessionID, SourceIPAddress)
	SELECT ORIGINAL_LOGIN(), GETDATE(), @@SPID, client_net_address
	FROM SYS.DM_EXEC_CONNECTIONS WHERE session_id = @@SPID;
```

The Security team will be pleased they can now track user connections to the server. In the next chapter you'll learn how to help them even more.

### Defining a logon trigger

Which characteristics can be set when creating a logon trigger?

* The trigger name / The set of actions to be performed

For logon triggers, you can only choose the trigger name and the actions to be performed by it. The trigger is always created at the server level, and it's an `AFTER/FOR` trigger.

# Known limitations of triggers

## Advantages of triggers

Triggers provide numerous advantages. As we've seen, they can be used successfully for database integrity purposes. Business rules can be enforced and stored directly in the database when using triggers. This makes it simpler to change or update the applications that are using the database, because the business logic is kept in the database itself. Triggers give you control over which statements are allowed in a database (a good feature when permission schemes don't offer you enough flexibility). Triggers can help you implement complex business logic fired by a single event. And they are also used to audit the database for changes or user activity.

## Disadvantages of triggers

Triggers also have some disadvantages. First, they are difficult to view and detect. It can be hard for an administrator to have a clear overview of the existing triggers in a database and their behavior. This means triggers are not easy to manage in a centralized manner. Triggers are also invisible to client applications. When debugging code, triggers are difficult to trace in most situations. And for some triggers, their complex code can make it hard to follow their logic when troubleshooting. Triggers can also affect server performance when they are overused or poorly designed. Before learning some best practices to overcome their limitations, let's see how to get a good overview of all your triggers.

## Finding server-level triggers

Triggers can be difficult to manage when they are undocumented, when they have complex logic in their design, and because they can be created on many levels (server, database, table). Luckily, SQL Server offers system views that gather all the information about triggers in one place. For example, this is the statement you need to run to get all the information about server-level triggers.

![](https://i.imgur.com/rA1c7D4.png)

## Finding database and table triggers

A similar view will get you details on both database-level triggers and table triggers. The type of the trigger (database or table) can be determined from the "parent_class_desc" column.

![](https://i.imgur.com/gsgieiF.png)

## Viewing a trigger definition (option 1)

If you only want to look at a couple of triggers, using the graphical interface of SQL Server Management Studio is a good solution: just right-click on the trigger name and script the trigger definition. A smarter approach is needed, however, when you want to view the definitions for lots of triggers in the database.

![](https://i.imgur.com/AvSkdRb.png)

## Viewing a trigger definition (option 2)

If you turn your attention to SQL system views again, you'll find there are several ways to extract trigger definitions. SQL system views are like virtual tables in the database, helping you to reach information that cannot be reached otherwise. This example is based on the "sql_modules" system view. It extracts the definition of a trigger based on its ID, but you don't actually need to know the ID; you can use the function OBJECT_ID to get a trigger's ID using its name. This example returns the definition for only one trigger, but the code can be adjusted to output more than one result.

![](https://i.imgur.com/aQsXGUg.png)

## Viewing a trigger definition (option 3)

You can also get the definition of a trigger using the OBJECT_DEFINITION function. You need to pass in the ID of the trigger as the function parameter. To get the ID, use the OBJECT_ID function first, passing it the trigger name.

![](https://i.imgur.com/7cYp0JL.png)

## Viewing a trigger definition (option 4)

The last option you can use is the "sp_helptext" procedure, which uses a parameter called "objname". You can pass the name of the trigger directly to this parameter to get the trigger definition. In practice, the use of the "sp_helptext" procedure is not that common, mostly because procedures cannot be used inside of SELECT syntax. Use of the OBJECT_DEFINITION function or the "sys.sql_modules" view is more widespread. Which one you choose is a matter of personal preference.

![](https://i.imgur.com/FJWMH80.png)

## Triggers best practice

We've seen a couple of helpful hints on how to discover and analyze existing triggers in a database, but the following tips should also always be taken into account: - Make sure your database design is well-documented, so that it's clear for anybody working with it. - Keep your trigger design simple&mdash;avoid complex logic where possible. - Avoid overusing triggers when they are not needed.

### Trigger limitations

What are some known trigger limitations?

* Triggers are invisible to client applications and difficult to debug.
* Triggers are difficult to test and troubleshoot when they are too complex.
* Triggers can degrade the server's performance when overused.

### Creating a report on existing triggers

Taking on the responsibility of administrating the database for the Fresh Fruit Delivery company also means keeping an eye on existing triggers.

The best approach is to have a report that can be run regularly and outputs details of the existing triggers.

This will ensure you have a good overview of the triggers and give you access to some interesting information.

* Start creating the triggers report by gathering information about existing database triggers from the `sys.triggers` table.

```
-- Get the column that contains the trigger name
SELECT name AS TriggerName,
	   parent_class_desc AS TriggerType,
	   create_date AS CreateDate,
	   modify_date AS LastModifiedDate,
	   is_disabled AS Disabled,
       -- Get the column that tells if it's an INSTEAD OF trigger
	   is_instead_of_trigger AS InsteadOfTrigger
FROM sys.triggers;
```

* Include information about existing server-level triggers from the sys.server_triggers table and order by trigger name.


```
-- Gather information about database triggers
SELECT name AS TriggerName,
	   parent_class_desc AS TriggerType,
	   create_date AS CreateDate,
	   modify_date AS LastModifiedDate,
	   is_disabled AS Disabled,
	   is_instead_of_trigger AS InsteadOfTrigger
FROM sys.triggers
UNION ALL
SELECT name AS TriggerName,
	   -- Get the column that contains the trigger type
	   parent_class_desc AS TriggerType,
	   create_date AS CreateDate,
	   modify_date AS LastModifiedDate,
	   is_disabled AS Disabled,
	   0 AS InsteadOfTrigger
-- Gather information about server triggers
FROM sys.server_triggers
-- Order the results by the trigger name
ORDER BY TriggerName;
```

![](https://i.imgur.com/Hqerz2s.png)

* Enhance the report by including the trigger definitions. You can get a trigger's definition using the `OBJECT_DEFINITION` function.

```
-- Gather information about database triggers
SELECT name AS TriggerName,
	   parent_class_desc AS TriggerType,
	   create_date AS CreateDate,
	   modify_date AS LastModifiedDate,
	   is_disabled AS Disabled,
	   is_instead_of_trigger AS InsteadOfTrigger,
       -- Get the trigger definition by using a function
	   OBJECT_DEFINITION(object_id) AS object
FROM sys.triggers
UNION ALL
-- Gather information about server triggers
SELECT name AS TriggerName,
	   parent_class_desc AS TriggerType,
	   create_date AS CreateDate,
	   modify_date AS LastModifiedDate,
	   is_disabled AS Disabled,
	   0 AS InsteadOfTrigger,
       -- Get the trigger definition by using a function
	   OBJECT_DEFINITION(object_id)
FROM sys.server_triggers
ORDER BY TriggerName;
```

![](https://i.imgur.com/m2K37m3.png)

Using information from `sys.triggers` and `sys.server_triggers` will give you a good overview of all your triggers.

# Use cases for AFTER triggers (DML)

## Keeping a history of row changes

A common use for `AFTER` triggers is to store historical data in other tables. In practice, this usually means having a history of changes performed on a table. For example, here we have the "Customers" table containing information about existing customers. The customers' details may change over time, and the information in the table will need to be updated. It is considered a good practice to keep an overview of the changes for the most important tables in your database.

![](https://i.imgur.com/7N2RtBf.png)

## Keeping a history of row changes

To start with the "CustomersHistory" table holds exactly the same details as "Customer", but it keeps a record of any changes that are made.

![](https://i.imgur.com/X6S2znA.png)

## Keeping a history of row changes

Suppose the phone number for the customer eFruits changes. After the change, the tables will hold the following details for this customer. The "Customers" table always shows the current information. The "CustomersHistory" table shows all the changes that have occurred for the customer, along with the change date.

![](https://i.imgur.com/LdGj3jw.png)

## Keeping a history of row changes

The result you saw previously is obtained using a trigger. The trigger will be fired for both INSERT and UPDATE statements. You can include both statements, comma-separated, as the firing actions. This will ensure that when new rows are added to the "Customers" table they are copied to the "CustomersHistory" table, and any changes to the details of existing customers will also end up in the history table. The INSERT statements will be taken care of using the "inserted" special table.

![](https://i.imgur.com/qB2K7oK.png)

## Table auditing using triggers

Another major use of AFTER triggers is to audit changes occurring in the database. Auditing means tracking any changes that occur within the defined scope. In this example, the scope of the audit is comprised of very important tables from the database. A trigger will be created on the "Orders" table. It will fire for any DML statements. Inside the trigger, we will declare two Boolean variables that will check the special tables "inserted" and "deleted". When one of the special tables contains data, the associated variable will be set to "true". The combination of values will tell us if the operation is an INSERT, UPDATE, or DELETE. A table called "TablesAudit" will be used to track the changes. The trigger will insert into that table information about the rows being modified, the user making the change, and the date and time of the change.

![](https://i.imgur.com/owbdJ9h.png)

## Notifying users

A simple and effective use case of triggers is to have them send notifications. Most of the notifications will be about events happening in the database and will be sent to interested users. For example, the Sales department must be notified when new orders are placed. A trigger attached to the `Orders` table will execute a procedure that sends an email when INSERT statements are executed.

![](https://i.imgur.com/hoaLZ2j.png)

### Keeping a history of row changes

The Fresh Fruit Delivery company needs to track changes made to the Customers table.

You are asked to create a new trigger that covers any statements modifying rows in the table.

* Create a new trigger called `CopyCustomersToHistory`.
* Attach the trigger to the `Customers` table.
* Fire the trigger when rows are added or modified.
* Extract the information from the `inserted` special table.

```
-- Create a trigger to keep row history
CREATE TRIGGER CopyCustomersToHistory
ON Customers
-- Fire the trigger for new and updated rows
AFTER INSERT, UPDATE
AS
	INSERT INTO CustomersHistory (CustomerID, Customer, ContractID, ContractDate, Address, PhoneNo, Email, ChangeDate)
	SELECT CustomerID, Customer, ContractID, ContractDate, Address, PhoneNo, Email, GETDATE()
    -- Get info from the special table that keeps new rows
    FROM inserted;
```

### Table auditing using triggers

The company has decided to keep track of changes made to all the most important tables. One of those tables is Orders.

Any modification made to the content of Orders should be inserted into TablesAudit.

* Create a new AFTER trigger on the Orders table.
* Set the trigger to fire for INSERT, UPDATE, and DELETE statements.

```
-- Add a trigger that tracks table changes
CREATE TRIGGER OrdersAudit
ON Orders
AFTER INSERT, UPDATE, DELETE
AS
	DECLARE @Insert BIT = 0;
	DECLARE @Delete BIT = 0;
	IF EXISTS (SELECT * FROM inserted) SET @Insert = 1;
	IF EXISTS (SELECT * FROM deleted) SET @Delete = 1;
	INSERT INTO TablesAudit (TableName, EventType, UserAccount, EventDate)
	SELECT 'Orders' AS TableName
	       ,CASE WHEN @Insert = 1 AND @Delete = 0 THEN 'INSERT'
				 WHEN @Insert = 1 AND @Delete = 1 THEN 'UPDATE'
				 WHEN @Insert = 0 AND @Delete = 1 THEN 'DELETE'
				 END AS Event
		   ,ORIGINAL_LOGIN() AS UserAccount
		   ,GETDATE() AS EventDate;
```

This script is creating a trigger in a database management system (DBMS) that will track changes made to the "Orders" table.

A trigger is a set of SQL statements that are automatically executed by the DBMS in response to a specific event, such as the insertion of a row into a table. In this case, the trigger is named "OrdersAudit" and it will be activated after an INSERT, UPDATE, or DELETE operation is performed on the "Orders" table.

The trigger first declares two local variables, @Insert and @Delete, which will be used to store a bit value indicating whether an INSERT or DELETE operation was performed, respectively. The trigger then uses the EXISTS function to check if the "inserted" and "deleted" virtual tables contain any rows. These virtual tables are automatically created by the DBMS and contain a copy of the rows affected by the INSERT, UPDATE, or DELETE operation that activated the trigger. If the "inserted" table contains any rows, the @Insert variable is set to 1, and if the "deleted" table contains any rows, the @Delete variable is set to 1.

The trigger then uses the INSERT INTO statement to insert a row into the "TablesAudit" table, which is used to store a record of the changes made to the "Orders" table. The row inserted into the "TablesAudit" table contains the name of the table that was modified ("Orders"), the type of event that occurred (INSERT, UPDATE, or DELETE), the name of the user account that performed the operation (retrieved using the ORIGINAL_LOGIN function), and the date and time the event occurred (retrieved using the GETDATE function). The type of event is determined by the values of the @Insert and @Delete variables using a CASE expression.

This trigger can be useful for auditing purposes, as it allows you to track any changes made to the "Orders" table and record who made the change and when it was made.




# Use cases for INSTEAD OF triggers (DML)

## General use of INSTEAD OF triggers

The use cases for these triggers are suggested by their name: `INSTEAD OF`. These kinds of triggers focus on preventing certain operations from happening in your database. If you can control the permitted statements inside your database and set some rules, you can enforce data integrity through triggers as well.

## Triggers that prevent changes

In this example, updates to the "Products" table are not permitted for regular database users. When a non-administrator runs an `UPDATE` statement, the trigger will raise an error using the `RAISERROR` function. An error message will be included to inform the user that they aren't allowed to make any changes to this table. The restriction is applied because the table contains information about stock. An incorrect `UPDATE` statement could wreak havoc by concealing the real stock numbers.

![](https://i.imgur.com/tnN6i00.png)

## Triggers that prevent and notify

In this second example, the trigger doesn't just raise an error message to prevent the deletion of a customer. An alert destined for the database administrator is also sent. When a user tries to remove a customer from the "Customers" table, an email will be sent to the database administrator. The removal action is, of course, denied with an error message. The body of the message is stored in the "EmailBodyText" variable and will include the name of the user who attempted to make the change. After raising the error, the trigger executes the "SendNotification" procedure to send the email alert to the database administrator.


```
CREATE TRIGGER PreventCustomersRemoval
ON Customers
INSTEAD OF DELETE
AS
    DECLARE @EmailBodyText NVARCHAR(50) =
        (SELECT 'User "' + ORIGINAL_LOGIN() +
        '" tried to remove a customer from the database.');
    RAISERROR ('Customer entries are not subject to removal.' , 16, 1);

EXECUTE SendNotification @RecipientEmail = 'admin@freshfruit.com',
                         @EmailSubject = 'Suspicious database behavior', 
                         @EmailBody = @EmailBodyText;
```



![](https://i.imgur.com/zpiWJF5.png)

## Triggers with conditional logic

`INSTEAD OF` triggers should not always be considered as objects that deny operations on the database. They can be used to decide whether or not some operations should succeed. In this example, we create a new trigger on the "Orders" table. It makes no sense for an order to be placed if there is insufficient stock of the product. An `INSTEAD OF` trigger can check whether there is sufficient stock for an order, through an `IF` statement. The process used to decide what the trigger will do is called "conditional logic". It gets this name because operations will be performed or not based on logical conditions, like the `IF` statement. In this example, the `IF` statement checks for the condition where the order quantity is higher than the existing stock level. If the condition is true, an error will be raised and the order will be denied. If the condition is false, indicating that there is sufficient stock, the initial `INSERT` operation is executed. The initial `INSERT` operation can only be executed if stated explicitly in the trigger code.

![](https://i.imgur.com/vHb5Ndw.png)

## Triggers with conditional logic

Here's what the conditional logic used in the trigger looks like. First, the `INSERT` operation fires the trigger. The trigger then verifies whether there is sufficient stock of the product included in the `INSERT` operation. If the condition is true, the order is placed by adding a new row in the "Orders" table. If it's false, the trigger throws an error and the `INSERT` operation is dropped.

![](https://i.imgur.com/zlapkxj.png)

### Preventing changes to Products

The Fresh Fruit Delivery company doesn't want regular users to be able to change product information or the actual stock.

* Create a new trigger, PreventProductChanges, that prevents any updates to the Products table.

```
-- Prevent any product changes
CREATE TRIGGER PreventProductChanges
ON Products
INSTEAD OF UPDATE
AS
	RAISERROR ('Updates of products are not permitted. Contact the database administrator if a change is needed.', 16, 1);
```

An `INSTEAD OF` trigger can prevent DML statements from being executed. What if you don't always want that? Let's see how it's done in the next exercise

### Checking stock before placing orders

On multiple occasions, customers have placed orders for products when the company didn't have enough stock to fulfill the orders.

This issue can be easily fixed by adding a new trigger with conditional logic for its actions.

The trigger should fire when new rows are added to the Orders table and check if the company has sufficient stock of the specified products to fulfill those orders.

If there is sufficient stock, the trigger will then perform the same `INSERT` operation like the one that fired it—only this time, the values will be taken from the inserted special table.

* Add a new trigger that fires for `INSERT` statements and checks if the order quantity can be fulfilled by the current stock.
* Raise an error if there's insufficient stock. Otherwise, perform an `INSERT` by making use of the inserted special table.

```
-- Create a new trigger to confirm stock before ordering
CREATE TRIGGER ConfirmStock
ON Orders
INSTEAD OF INSERT
AS
	IF EXISTS (SELECT *
			   FROM Products AS p
			   INNER JOIN inserted AS i ON i.Product = p.Product
			   WHERE p.Quantity < i.Quantity)
	BEGIN
		RAISERROR ('You cannot place orders when there is no stock for the order''s product.', 16, 1);
	END
	ELSE
	BEGIN
		INSERT INTO Orders (OrderID, Customer, Product, Price, Currency, Quantity, WithDiscount, Discount, OrderDate, TotalAmount, Dispatched)
		SELECT OrderID, Customer, Product, Price, Currency, Quantity, WithDiscount, Discount, OrderDate, TotalAmount, Dispatched FROM Orders;
	END;
```

This is a good example of a trigger designed to use conditional logic. When the condition is true, an error is raised. When it's false, the trigger goes further and inserts new rows.

# Use cases for DDL triggers

## Use cases for DDL triggers

Now it's time to look at some use cases for DDL triggers. This lesson will only give you an overview of DDL triggers at the database and server level. The topic is more complex than what you will be learning here, but this lesson will provide you with the fundamentals to get you started.

## DDL trigger capabilities

DDL triggers can be created at the database level or at the server level. Where the trigger is created will influence the types of statements able to fire it. For example, a trigger created at the database level can respond to statements related to tables, view interactions, and index management, as well as more specific statements to do with permissions management or statistics. At the server level, we can use triggers for database management and to control server permissions and the use of credentials. Don't worry about remembering exactly which DDL statements can be used to fire database- or server-level triggers; you can always check the online documentation from Microsoft for a full list of DDL events.

![](https://i.imgur.com/eu1glre.png)

## Database auditing

So what exactly can we do with database-level triggers? Well, we can keep a trace of any activity happening at the database level. This is called a _database audit_. We are going to use a group event to fire our "DatabaseAudit" trigger: `DDL_TABLE_VIEW_EVENTS`. This DML event includes any operations dealing with tables, views, indexes, or statistics. The advantage of using a group event is that you can specify a single event to cover all the cases that should fire the trigger (in this case, more than a dozen statements). We are going to insert details about the operations performed in the database into the "DatabaseAudit" table. The details of the operations will be extracted using the EVENTDATA function. This function returns information about an operation in XML format. In order to extract specific information in clear text, we call the `value` function. For example, the first call of the EVENTDATA and "value" functions will get the "EventType" from the XML and convert it to the SQL NVARCHAR data type. The same logic is applied for the other columns.

## Database auditing

Here's what the "DatabaseAudit" table looks like after using the trigger. All the user actions are kept in the table. Any breaking changes can then be traced back to the responsible person.

![](https://i.imgur.com/uJWJJdo.png)

![](https://i.imgur.com/9k5tHbp.png)

## Preventing server changes

Remember, it is not possible to use `INSTEAD OF` for DDL triggers. But that doesn't mean you cannot prevent operations from happening when using DDL triggers. DDL triggers are used in the real world to prevent database or server changes that could lead to data loss. The trigger in this example prevents database deletion. As you can see, it uses the `ROLLBACK` statement to prevent `DROP` operations on databases. The statement that fired the trigger was prevented without the use of `INSTEAD OF`.

![](https://i.imgur.com/izRAKoK.png)

### Database auditing

Your next task is to develop a new trigger to audit database object changes.

You need to create the trigger at the database level. You can use the `DDL_TABLE_VIEW_EVENTS` group event to fire the trigger. This group event includes any database operation involving tables, views, indexes, or statistics. By using the group event, you do not need to specify all the events individually.

The trigger will insert details about the firing statement (event, user, query, etc.) into the DatabaseAudit table.

* Create a DatabaseAudit trigger on the database that fires for DDL_TABLE_VIEW_EVENTS.

```
-- Create a new trigger
CREATE TRIGGER DatabaseAudit
-- Attach the trigger at the database level
ON DATABASE
-- Fire the trigger for all tables/ views events
FOR DDL_TABLE_VIEW_EVENTS
AS
	INSERT INTO DatabaseAudit (EventType, DatabaseName, SchemaName, Object, ObjectType, UserAccount, Query, EventTime)
	SELECT EVENTDATA().value('(/EVENT_INSTANCE/EventType)[1]', 'NVARCHAR(50)') AS EventType
		  ,EVENTDATA().value('(/EVENT_INSTANCE/DatabaseName)[1]', 'NVARCHAR(50)') AS DatabaseName
		  ,EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'NVARCHAR(50)') AS SchemaName
		  ,EVENTDATA().value('(/EVENT_INSTANCE/ObjectName)[1]', 'NVARCHAR(100)') AS Object
		  ,EVENTDATA().value('(/EVENT_INSTANCE/ObjectType)[1]', 'NVARCHAR(50)') AS ObjectType
		  ,EVENTDATA().value('(/EVENT_INSTANCE/LoginName)[1]', 'NVARCHAR(100)') AS UserAccount
		  ,EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'NVARCHAR(MAX)') AS Query
		  ,EVENTDATA().value('(/EVENT_INSTANCE/PostTime)[1]', 'DATETIME') AS EventTime;
```

### Preventing server changes

The company is also asking you to find a method to prevent databases from being mistakenly deleted by employees.

After a detailed analysis, you decide to use a trigger to fulfill the request.

The trigger will roll back any attempts to delete databases.

* Create a new trigger called PreventDatabaseDelete.
* Attach the trigger at the server level.

```
-- Create a trigger to prevent database deletion
CREATE TRIGGER PreventDatabaseDelete
-- Attach the trigger at the server level
ON ALL SERVER
FOR DROP_DATABASE
AS
   PRINT 'You are not allowed to remove existing databases.';
   ROLLBACK;
```

Your server is now protected from any database deletion attempts.

# Deleting and altering triggers

You've learned a lot about creating various types of triggers, and had some practice. But what happens when you don't want a trigger anymore? Or when you decide to change the trigger actions? You'll learn how it's done in this lesson.

## Deleting table and view triggers

It's easy to delete a trigger in SQL Server. The syntax is straightforward: DROP TRIGGER followed by the trigger name. This syntax is applicable when you are removing triggers attached to tables or views. In this example, the trigger "PreventNewDiscounts" will be deleted.

![](https://i.imgur.com/cXtIeNp.png)

## Deleting database triggers

If you want to remove a database-level trigger, you have to make that explicit in the syntax. You will use DROP TRIGGER with the trigger name as you did before, but this time the syntax will be expanded with the ON DATABASE statement. This will inform SQL Server to look at the database level for the trigger to be deleted. In the second example, the trigger "PreventViewsModifications" will be deleted at the database level.

![](https://i.imgur.com/jpIsXDB.png)

## Deleting server triggers

A slightly different syntax should be used if you want to delete triggers at the server level. As before, you'll use DROP TRIGGER and the trigger name, but then you'll instruct SQL Server to search for the trigger ON ALL SERVER. In this case, the trigger "DisallowLinkedServers" will be deleted from the server.

![](https://i.imgur.com/npJtVFG.png)

## Disabling triggers

A deleted trigger can never be used again, unless you recreate the trigger. Deleting triggers is okay when you no longer need them, but what if you just want to avoid them for a brief period? Luckily, SQL Server offers the possibility to disable triggers. A disabled trigger still exists as a SQL Server object, but as long as it's disabled it will not be fired. The syntax to disable a trigger is also straightforward, but note that when disabling you need to specify the object the trigger is attached to, even if it is a table. To switch off a table-level trigger, use DISABLE TRIGGER and the trigger name, and specify the name of the table (for example, "ON Discounts"). Replace that with the ON DATABASE statement for database-level triggers, or the ON ALL SERVER statement for triggers created at the server level.

![](https://i.imgur.com/v5DNEok.png)

## Enabling triggers

When you want a disabled trigger to start working again, you simply re-enable it. This is accomplished by using the ENABLE TRIGGER syntax, followed by the trigger name and the scope of the trigger: specify the name of the table or view the trigger is attached to, or use ON DATABASE or ON ALL SERVER. Now all the triggers we disabled previously will be working again.

![](https://i.imgur.com/Q7WoewU.png)

## Altering triggers

There will be situations where you need to modify a trigger. It might be malfunctioning, or you might want to add new functionality. Whatever the reason, at some point you're likely to end up having to change triggers, either during development or later. In this example, a simple trigger is created on the "Discounts" table to prevent any deletions. You run the code and create the trigger, but you notice you forgot to include the word "remove" between "to" and "data" in the print statement. To fix the error, simply drop the existing trigger, then fix your code and run it again.

![](https://i.imgur.com/nbecDTj.png)

## Altering triggers

This can become a hassle in the development phase, when you want to test the trigger and make changes immediately. To avoid this create-and-drop flow, you can use the `ALTER` statement to modify the definition of an existing trigger. Simply replace the CREATE keyword with ALTER, while keeping everything else the same (except for the changes you wish to make, of course!). This example shows how you can add the word "remove" to the message the trigger prints using ALTER.

![](https://i.imgur.com/VMJa0bV.png)

### Removing unwanted triggers

After some time, the Fresh Fruit Delivery company notices that some of the triggers they requested are no longer needed. Their workflow has changed and not all of the triggers are used now.

It's a good practice to have your database clean and up-to-date. Unused objects should always be removed after proper confirmation from the involved parties.

The company calls to ask you to help them remove the unused triggers.

* Remove the `PreventNewDiscounts` trigger attached to the `Discounts` table.

```
-- Remove the trigger
DROP TRIGGER PreventNewDiscounts;
```

* Remove the `PreventTableDeletion` trigger attached to the database.

```
-- Remove the database trigger
DROP TRIGGER PreventTableDeletion
ON DATABASE;
```

* Remove the DisallowLinkedServers trigger attached at the server level.

```
-- Remove the server trigger
DROP TRIGGER DisallowLinkedServers
ON ALL SERVER;
```

### Modifying a trigger's definition

A member of the Sales team has noticed that one of the triggers attached to the Discounts table is showing a message with the word "allowed" missing.

* Modify the trigger definition and fix the typo without dropping and recreating the trigger.
* Add the missing word to the PRINT statement.

```
-- Fix the typo in the trigger message
ALTER TRIGGER PreventDiscountsDelete
ON Discounts
INSTEAD OF DELETE
AS
	PRINT 'You are not allowed to remove data from the Discounts table.';
```

### Disabling a trigger

Fresh Fruit Delivery needs to make some changes to a couple of rows in the Orders table.

Earlier they asked for a trigger to prevent unwanted changes to the Orders table, but now that trigger is stopping them from making the necessary modifications.

You are asked to help them with the situation by temporarily stopping that trigger from firing.

* Pause the trigger execution to allow the company to make the changes.

```
-- Pause the trigger execution
DISABLE TRIGGER PreventOrdersUpdate
ON Orders;
```

The company can go ahead and make their changes while the trigger is disabled.

### Re-enabling a disabled trigger

You helped the company update the Orders table by disabling the PreventOrdersUpdate trigger. Now they want the trigger to be active again to ensure no unwanted modifications are made to the table.

* Re-enable the disabled PreventOrdersUpdate trigger attached to the Orders table.

```
-- Resume the trigger execution
ENABLE TRIGGER PreventOrdersUpdate
ON Orders;
```

With the trigger enabled, the company is safe again from unwanted changes to the Orders table.

# Trigger management

As you saw in previous lessons, you can end up having lots of triggers in your database. One of the main challenges is the ability to manage all these triggers and have a good overview. You will learn how it's done in this lesson.

## Getting info from sys.triggers

All the necessary information about existing triggers can be extracted from SQL Server system tables or views. These objects are used to store information about everything that is happening in SQL Server. We will first turn our attention to the `"sys.triggers"` view and explore the information it can provide us.

![](https://i.imgur.com/JBiLMAk.png)

## Getting info from sys.triggers

"`sys.triggers`" contains 13 columns, but we will only look at the most important ones. The "name" column contains the trigger name (given when the trigger is created). The "`object_id`" column contains a unique identifier for the trigger object. There are a few double columns, where one column contains an integer code and the other column holds the text explanation. For example, the "parent_class" column contains an integer representing the trigger type. If it's a table trigger, the value will be 1, and for a database trigger the value will be 0. The column "parent_class_desc" gives a textual description of the trigger type. The "parent_id" column will tell you the ID of the object the trigger is attached to.

![](https://i.imgur.com/OnJVL9o.png)

## Getting info from sys.triggers

The "create_date" column stores the trigger creation date, while the "modify_date" column tells you when the trigger was last modified. If the trigger is disabled, the "is_disabled" column will have the value 1. Otherwise, it will be 0. Similarly, the value in the "is_instead_of_trigger" column will be 1 for `INSTEAD OF` and 0 for `AFTER` triggers.

![](https://i.imgur.com/qE9c3XY.png)

## Getting info from sys.server_triggers

The "`sys.server_triggers`" view holds information about server-level triggers. It has exactly the same structure as "`sys.triggers`", and the columns hold similar information.

![](https://i.imgur.com/pL4Xybg.png)

## Getting info from sys.trigger_events

What about identifying the events that will fire a trigger? This information is stored in "`sys.trigger_events`".

![](https://i.imgur.com/N9JWjnM.png)

## Getting info from sys.trigger_events

Among the most useful columns in this view is "object_id", which identifies the trigger. The columns "type" and "type_desc" will tell you which events will fire a trigger. The columns "event_group_type" and "event_group_type_desc" will point you to any group events that will fire the trigger.

![](https://i.imgur.com/UOzM87K.png)

## Getting info from sys.trigger_events

Trigger group events are special events that are used to fire a trigger. They can contain multiple regular events. The advantage is that you do not need to specify the events individually. For example, the group `DDL_TABLE_VIEW_EVENTS` contains more than a dozen events related to table and view interactions like `CREATE`, `DROP`, and `ALTER`.

![](https://i.imgur.com/wlpYX8g.png)

## Getting info from sys.server_trigger_events

A similar view can be used to extract information about the server-level triggers. The view is called "`sys.server_trigger_events`".

![](https://i.imgur.com/or0OLzi.png)

## Getting info from sys.trigger_event_types

As mentioned before, you do not need to memorize all the existing events that can be used to fire triggers. The full list is accessible in the "`sys.trigger_event_types`" view, which shows the event types as numbers and as text. It will also show the parent type when the event is part of a larger group. In this example, `CREATE_TABLE`, `ALTER_TABLE`, and `DROP_TABLE` have the value "10018" in the "parent_type" column. In the last row shown here you can see that this type is actually the `DDL_TABLE_EVENTS` group.

![](https://i.imgur.com/KvvDp7x.png)

## Trigger management in practice

In practice, all of this information has to be packed into a useful form. For example, if you want to see a list of triggers along with their firing events and the objects they're attached to, you need to combine information from multiple views. You can get the trigger name and type from "`sys.triggers`". If you join the output with "`sys.trigger_events`" based on the "object_id", you can determine the firing events for each trigger. If you further join the "parent_id" of the trigger with the "object_id" from "sys.objects", you can extract the name and type of the object the trigger is attached to. The second join is chosen to be a `LEFT JOIN` because database-level triggers do not appear as attached to an object. A `LEFT JOIN` will still keep the results in the case of non-matching join parameters. If we'd used an `INNER JOIN`, the results would've been filtered only for matching rows and the database triggers would have been filtered out from the list.

![](https://i.imgur.com/E2zNXCT.png)

## Trigger management in practice

This is the query result. There are no values for "AttachedTo" and "ObjectType" for database-level triggers, because they are not attached to any kind of object. They simply exist on the database. This script is just one of many you can use in the real world. An essential thing to remember is that the views seen in this lesson are usually combined to get more detailed results, not used in isolation.

![](https://i.imgur.com/whp85sB.png)

### Managing existing triggers

Fresh Fruit Delivery has asked you to act as the main administrator of their database.

A best practice when taking over an existing database is to get familiar with all the existing objects.

You'd like to start by looking at the existing triggers.

* Get the name, object_id, and parent_class_desc for all the disabled triggers.

```
-- Get the disabled triggers
SELECT name,
	   object_id,
	   parent_class_desc
FROM sys.triggers
WHERE is_disabled = 1;
```

![](https://i.imgur.com/7ZkPyie.png)

* Get the unmodified server-level triggers.
* An unmodified trigger's create date is the same as the modify date.

```
-- Check for unchanged server triggers
SELECT *
FROM sys.server_triggers
WHERE create_date = modify_date;
```

![](https://i.imgur.com/OHcVXNb.png)

* Use sys.triggers to extract information only about database-level triggers.

```
-- Get the database triggers
SELECT *
FROM sys.triggers
WHERE parent_class_desc = 'DATABASE';
```

SQL Server system views can be used to get information on existing triggers.

### Counting the AFTER triggers

During your analysis of the database, you decide you'd like to have an overview of how many `AFTER` triggers exist.

You use the sys.triggers view to count the `AFTER` triggers.

How many triggers are there?

* 11 `AFTER` triggers

# Troubleshooting triggers

In this lesson, you will learn how to troubleshoot and optimize triggers. You will learn how to keep a history of trigger runs, and how to search for triggers causing issues.

![](https://i.imgur.com/yUOU1tw.png)

## Tracking trigger executions (system view)

One important factor when troubleshooting triggers is to have a history of their execution. This allows you to associate the timing of trigger runs with issues caused by triggers. SQL Server provides information on the execution of triggers that are currently stored in memory. The information can be seen in "`sys.dm_exec_trigger_stats`". Unfortunately, when a trigger is removed from the memory, the information about that trigger is removed from the view as well. This leaves you with a major problem regarding tracking trigger execution history. There is no other option to see the history of trigger runs, unless you create a custom solution.

![](https://i.imgur.com/o9Fb8Wx.png)

## Tracking trigger executions (custom solution)

For example, suppose we want to create a permanent record of executions of the trigger "PreventOrdersUpdate", designed to prevent any updates to the "Orders" table.

![](https://i.imgur.com/nuVPgkK.png)

## Tracking trigger executions (custom solution)

We can use the "TriggerAudit" table to store information on trigger execution. To enable this, we will update the trigger definition to specify that when it executes, the trigger name and the current date and time will be inserted into the "TriggerAudit" table. We'll enhance the trigger definition with the new code using the `ALTER` statement, as shown here.

![](https://i.imgur.com/aONl0qt.png)

## Tracking trigger executions (custom solution)

An attempt to update the quantity for the order with number 784 will result in the "PreventOrdersUpdate" trigger being fired. The trigger will throw an error message saying that updates are not permitted on the "Orders" table.

![](https://i.imgur.com/rqUco63.png)

## Tracking trigger executions (custom solution)

However, with our change to the trigger definition in place, it will also insert information about the trigger execution into the "TriggerAudit" table. If we check the contents of that table, we will see the information about this trigger execution. We now have a permanent record that we can use to track the history of trigger runs. A solution like this will allow you to associate the timings of trigger executions with issues potentially caused by the trigger in future investigations. If the date and time of the issue are similar to the date and time when the trigger was executed, you can assume the trigger might be causing the problem and investigate further.

![](https://i.imgur.com/8beKIq5.png)

## Identifying triggers attached to a table

Sometimes you may have issues with certain tables and suspect triggers to be the root cause. But how can you investigate them if no documentation is available? In this example, we want to find out what triggers are attached to the `Products` table. Starting from the table name, we can query the "sys.objects" view to find the table ID of "Products". We can then use this ID to determine what triggers are attached to the table, and get some information about them.

![](https://i.imgur.com/NTy2hSw.png)

## Identifying triggers attached to a table

We enhance the script further by joining the first query with the "sys.triggers" view. This will help us to find the triggers attached to the "Products" table. The join is made by matching the "parent_id" column of the trigger with the "object_id" column of the "Products" table. We add the following information to our script: the trigger name (from the "name" column), whether it's disabled (the "is_disabled" column), and whether it's an INSTEAD OF trigger (the "is_instead_of_trigger" column). Columns with the "o" prefix are coming from "sys.objects" and columns with the "t" prefix are coming from "sys.triggers".

![](https://i.imgur.com/xMc4RUH.png)

## Identifying triggers attached to a table

The end result is starting to look better, as we now have some insights on the existing triggers attached to the "Products" table.

![](https://i.imgur.com/6X4fRc3.png)

## Identifying events capable of firing a trigger

Now we'll add one more important column with another INNER JOIN on "sys.trigger_events".

![](https://i.imgur.com/zZuwUhU.png)

## Identifying events capable of firing a trigger

The addition of the "type_desc" column brings us details on the events capable of firing the triggers.

![](https://i.imgur.com/QO2e28N.png)

## Viewing the trigger definitions

The last important information to add is the trigger definitions. To troubleshoot a trigger's results or behavior, you need to know what it's intended to do. To view the trigger definition code, we will make use of the `OBJECT_DEFINITION` function. This function will return the definition for an object ID passed as an argument. In this particular example, it will be the ID of the trigger.

![](https://i.imgur.com/Pa4Ytux.png)

## Viewing the trigger definitions

The trigger definitions can now be inspected and modified if needed.

![](https://i.imgur.com/kYpTDcS.png)

### Keeping track of trigger executions

One important factor when monitoring triggers is to have a history of their execution. This allows you to associate the timings between trigger runs and any issues that occur in the database.

If the times match, it's possible that the problems were caused by the trigger.

SQL Server provides information about the execution of any triggers that are currently stored in memory in the sys.dm_exec_trigger_stats view. But once a trigger is removed from the memory, any information about it is removed from the view as well, so you lose the trigger execution history.

To overcome this limitation, you decide to make use of the TriggerAudit table to store information about any attempts to modify rows in the Orders table, because people have reported the table is often unresponsive.

* Modify the PreventOrdersUpdate trigger.
* Set the trigger to fire when rows are updated in the Orders table.
* Add additional details about the trigger execution into the TriggerAudit table.

```
-- Modify the trigger to add new functionality
ALTER TRIGGER PreventOrdersUpdate
ON Orders
-- Prevent any row changes
INSTEAD OF UPDATE
AS
	-- Keep history of trigger executions
	INSERT INTO TriggerAudit (TriggerName, ExecutionDate)
	SELECT 'PreventOrdersUpdate', 
           GETDATE();

	RAISERROR ('Updates on "Orders" table are not permitted.
                Place a new order to add new products.', 16, 1);
```

You remembered how to modify triggers using the `ALTER` statement. From now on, any execution of the trigger will be mentioned in the table.

### Identifying problematic triggers

You've identified an issue when placing new orders in the company's sales system.

The issue is related to a trigger run, but you don't have many details on the triggers themselves. Unfortunately, the database objects (including triggers) are not documented.

You need to identify the trigger that's causing the problem to proceed with the investigation. To be sure, you need to gather some important details about the triggers.

The only information you have when starting the investigation is that the table related to the issues is Orders.

* Find the ID of the Orders table by using the sys.objects system view.

```
-- Get the table ID
SELECT object_id AS TableID
FROM sys.objects
WHERE name = 'Orders';
```

* Find all the triggers attached to the Orders table by joining the first query with sys.triggers.
* Select the trigger name column.

```
-- Get the trigger name
SELECT t.name AS TriggerName
FROM sys.objects AS o
-- Join with the triggers table
INNER JOIN sys.triggers AS t ON t.parent_id = o.object_id
WHERE o.name = 'Orders';
```

* Filter the triggers fired for UPDATE statements, joining the previous query with sys.trigger_events.
* Select the triggers and their firing statements by matching the object_id columns from sys.triggers and sys.trigger_events.

```
SELECT t.name AS TriggerName
FROM sys.objects AS o
INNER JOIN sys.triggers AS t ON t.parent_id = o.object_id
-- Get the trigger events
INNER JOIN sys.trigger_events AS te ON te.object_id = t.object_id
WHERE o.name = 'Orders'
-- Filter for triggers reacting to new rows
AND te.type_desc = 'UPDATE';
```

* Include the trigger definitions in your selection with the use of a standard SQL Server function.

```
SELECT t.name AS TriggerName,
	   OBJECT_DEFINITION(t.object_id) AS TriggerDefinition
FROM sys.objects AS o
INNER JOIN sys.triggers AS t ON t.parent_id = o.object_id
INNER JOIN sys.trigger_events AS te ON te.object_id = t.object_id
WHERE o.name = 'Orders'
AND te.type_desc = 'UPDATE';
```

These are the steps you need to perform to identify the triggers attached to a table and see their definitions.