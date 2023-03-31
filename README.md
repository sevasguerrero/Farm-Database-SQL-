# Farm-Database-SQL-
Team Name: ns_21482_2

				  
				  
				  
				  
Ryan Maguire: https://github.com/rmaguire2020/SQL_Project

Amy Nguyen: https://github.com/AmyN137/MySQL-Farm 

Destinee Pruden: https://github.com/DPRUDEN25MySQL-Farm-Project

Sevastian Guerrero: https://github.com/sevasguerrero

Saahil Bapat: https://github.com/saahil-bapat/farm


# Farm Database

The problem being modeled by the farm data model is the process of recording the use of employees, livestock, and machine resources, on a farm in order to optimize their utilization to better produce products for clients. Concerning human resources, the model contains both “Employees” and “Client” entities that track data like contact information, and role (for employees). This makes the job of reaching out to employees or clients more efficient, as all information needed for the task is consolidated. Also, related to these two entities, are the “Product” and “OrderDetails” entities. These keep track of product quality, amount, price, and specific details related to orders such as which clients/employees are connected to them. The product measurement units are specific on the type of animal being kept a record of. For meat the measure would be pounds (lbs), for eggs the count, and milk is measured in gallons. Keeping track of specific information related to the product being shipped allows for the farm to pinpoint the origin of any related problems and work with the employees or clients associated with them. 
	Regarding livestock resources, the data model includes “Livestock” and “Feeding” entities. The “Livestock” entity keeps track of information about the animals like their type, breed, and age while the “Feeding” entity tracks information like the date time they are fed, how much they are fed, and water consumption. This entity also takes into account the cost of the feed and the water consumption to ultimately help determine the profit of the farm. Also, the feeding entity tracks which employees are involved in the feeding process and data related to their individual interactions with the animals. 
	Finally, as far as machine resources, the model contains both “Machinery” and “Machinery Log” entities. The “Machine” entity contains data about the make, model, and maintenance for each machine while the “Machinery Log” tracks the date and time the machines are used (specifically related to which employees are operating them). This allows for the farm to stay up to date on machine maintenance because they can schedule based on past maintenance. Also, it makes it easy to know if issues occur under specific employees. 



## Farm Data Model
<p align="center"

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Screen%20Shot%202023-03-30%20at%2010.01.21%20AM.png)


## Data Model Description

The Employees entity has a foreign key to itself as there are multiple supervisors, an example of a recursive relationship. Employees has a one to many relationship with employees, as an employee can only be supervised by one supervisor. Employees have a many to many relationship with livestock as many employees can feed many of the livestock (including the same ones). Due to this many to many relationship an associative entity is formed called Feeding. Feeding's composite primary keys helps establish that only one employee can feed one animal at a specified date (feedDate) and time (feedTime). Livestock has its primary key (livestockID) as foreign key in Product because each livestock corresponds with the products made. Livestock has a 1:m relationship to Product. This occurs because one animal can produce many products. Product, Client, and Employees all have a foreign key in OrderDetails (productID, clientID, employeeID).Product, Client, and employees all share the m:m relationship. OrderDetails would be considered an associative entity, because there can be multiple employees that help multiple of the same clients and there can be multiple clients that buy quantities of the same product and other products in the same order. Machinery has a many to many relationship with employees as the Machinery log is the associative entity. Just as the Ordering Details entity (orderNumber), Machinery Log has a single primary key (logID), establishing that only one employee can use one machine at a specific date an time. 
## Data Dictionary

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Client.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Employees.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Feeding.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Livestock.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Machinery.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/MachineryLog.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/OrderDetails.png)

![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Product.png)



## Ten Queries

Query 1: How much feed did the best meat producing cow eat each day?
```sql
SELECT Livestock.livestockID, feedAmount, productType, MAX(Product.qtyInStock) AS meatProduced
FROM Livestock
JOIN Feeding ON Livestock.livestockID = Feeding.livestockID
JOIN Product ON Livestock.livestockID = Product.livestockID
WHERE Livestock.livestockType = 'Cow'
GROUP BY Livestock.livestockID, feedAmount,productType
ORDER BY meatProduced DESC
LIMIT 1;
```

This query involves selecting the best meat producing cow and what they ate each day. A manager would be interested in this data because it would help them determine if the amount of food eaten buy a cow corrsonds with the amount of output of meat in pounds. If it does then the managers should try to fed the cows the same amount as the best meat producing cow to increase production. and the same query can be done to determine if milk producing cows should be fed a certain amount.

Query 2: Which client orders had the most machine days put into it?
```sql
SELECT clientName, COUNT(logdate) AS totalMachineDays
FROM Client
JOIN OrderDetails ON Client.clientID = OrderDetails.orderNumber
JOIN Employees ON  OrderDetails.employeeID = Employees.employeeID
JOIN MachineryLog ON Employees.employeeID = MachineryLog.employeeID
GROUP BY clientName
ORDER BY totalMachineDays DESC
LIMIT 1;
```
 The objective of this query is to retrieve the client name and the number of days a machine was utilized to complete a order. This information can aid a manager in identifying clients who may be ordering relatively small quantities of products, resulting in a smaller profit margin, but their orders are utilizing machines longer, leading to higher expenses. This insight could prompt the manager to consider implementing an additional charge, on small orders.

Query 3: Write a query listing the employee ID, employee name and the number of clients that each employee with the role of full-time is responsible for.
```sql
SELECT Employees.employeeID, employeeName, COUNT(Client.clientID) AS 'Number of Clients'
FROM Employees
JOIN OrderDetails ON Employees.employeeID = OrderDetails.employeeID
JOIN Client ON OrderDetails.clientID = Client.clientID
WHERE employeeRole = 'Full-Time'
GROUP BY Employees.employeeID;
```
The objective of this query is to know the ID and names of the full-time employees and the number of clients they are responsible for. The reason it is specifically full-time employees is because a farm can have a lot of clients, so it is easier to disperse the number of clients to multiple employees so that not one person is stuck with a super large amount of clients. Also, full-time employees are more likely to stay with the farm, so it is better for them to be in charge of more clients than part-time employees.

Query 4: List the name of the employee and the count of the employees that they supervise.
```sql
SELECT supervisor.employeeName AS "Supervisor", COUNT(supervised.supervisor) AS "Number Supervised"
FROM Employees AS supervisor
JOIN Employees AS supervised ON supervisor.employeeID = supervised.supervisor
GROUP BY supervisor.employeeID, supervisor.employeeName;
```
This query retrieves information about the name of supervisors and how many people they supervise directly. The roles included under the ‘employeeRole’ column are Owner, Manager, Full-Time, Part-Time, and Volunteer. The supervisors would be the Owner and the Managers supervising the other three roles, which is helpful for understanding how many employees each supervisor is responsible for.

Query 5: List cows' ID and weights that are below average weight for the product of meat.
```sql
SELECT Livestock.livestockID, Product.qtyInStock
FROM Livestock
JOIN Product ON Livestock.livestockID = Product.livestockID
WHERE qtyInStock<(SELECT AVG(qtyInStock) FROM Product WHERE productType = "meat") AND livestockType= "Cow" AND productType = "meat"
GROUP BY livestockID, qtyInStock;
```
This query lists the livestock ID and weight (qtyInStock) to help the farm identify the cows that are below average weight. In turn employees will be able to better feed the cows so they can stabilize their weight and ultimately maximize the farm's revenue.

Query 6: Find the name of the client and the total dollar amount the farm profited from the order.
```sql
SELECT clientName,(qtyOrdered*price)-(qtyOrdered*unitPrice) AS "Profit"
FROM Client
JOIN OrderDetails ON Client.clientID = OrderDetails.clientID
JOIN Product ON OrderDetails.productID = Product.productID
GROUP BY clientName, Profit;
```
This query is used to find the name of the client and the dollar amount the farm profited from their orders. This information is useful because the farm could prioritize selling to clients that result in a greater profit. This would maximize total profit.

Query 7: List the date and time logged for tractors that have had maintenance in 2023.
```sql
SELECT logDate,logTime,machinetype,lastMaintenance
FROM MachineryLog
JOIN Machinery on MachineryLog.machineID = Machinery.machineID
WHERE machineType = "Tractor" AND lastMaintenance REGEXP "2023";
```
This query is used to find the date and time logged for tractors that have had maintenance in 2023. This information would be useful to make sure the tractors that had more recent maintenance, and in theory are more reliable, are being utilized at a level greater than tractors with less recent maintenance.

Query 8: Find the average feed amount for each employee role where an employee.
```sql
SELECT employeeRole, AVG(feedAmount)
FROM Feeding
JOIN Employees ON Feeding.employeeID = Employees.employeeID
GROUP BY employeeRole;
```
This query would help supervisors be able to track  how much food is fed by each employee role.  This could help with strategic decisions with keeping more or less employees based on their roles (keeping more full time or keeping more part time) and average feeding.

Query 9: List out the client's contact information who orders milk or eggs.
```sql
SELECT clientPhone, clientEmail
FROM Client
JOIN OrderDetails ON Client.clientID = OrderDetails.clientID
JOIN Product ON OrderDetails.productID = Product.productID
WHERE productType = "milk";
```
This query would be important to help managers find the contact information for two specific product types: milk or eggs. If there was a problem with the product type, managers could use this query to find the contact information for these specific clients easier. 

Query 10: What is the average feeding cost for each type of product?
```sql
SELECT productType, ROUND(AVG(unitPrice),2) AS "AVG Unit Price", ROUND(AVG(feedCost*feedAmount),2) AS "AVG Feed Cost"
FROM Feeding
JOIN Livestock ON Feeding.livestockID = Livestock.livestockID
JOIN Product ON Livestock.livestockID=Product.livestockID
GROUP BY productType;
```
This query informs the farm manager's of what each product is costing to maintain (feed). With this information they can begin to strategize on what product they need to focus on to reduce feeding cost 




## Matrix
![App Screenshot](https://raw.githubusercontent.com/AmyN137/MySQL-Farm/main/Query%20Matrix.png)
