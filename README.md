# CoinTab Dataset Challenge - Solution 

## Step 1: Looking at the Data

Having downloaded and unzipped all .xlsx files, we take a brief look at them.

Each table is associated with an alias to identify whether it's related to *Courier Company (C)* or *The Company X.*
1. Company X - Order Report &nbsp; &nbsp; **{X1}**
2. Company X - Pincode Zones &nbsp; &nbsp; **{X2}**
3. Company X - SKU Master &nbsp; &nbsp; **{X3}**
4. Courier Company - Rates &nbsp; &nbsp; **{C1}**
5. Courier Company - Invoice &nbsp; &nbsp; **{C2}**
6. Expected_Result
    
## Step 2: Exploratory Data Analysis

Create a rough schema of the database and note down pointers such as:
1. Data Types
2. Column with unique values
3. Any Null Values present?
4. Column Type (Categ./ Cont./ Qualitiative)
    
    
### Observations

* None of the columns in any table has any Null Values.
* C2 table can be modified for better usage of the data present in the table.
* X1.Order No. and X1.SKU has duplicate values which needs to be treated using Group By / Pivot Table

#### C2 Table (Re-Designed)

![Screenshot 2022-08-01 193217](https://user-images.githubusercontent.com/91784043/182167754-21461d80-9ca2-4da1-9be5-96d7b2aabf53.png)

# Step 3: Defining Problem Statement

*Part 1* 

Create a resultant CSV/Excel file with the following columns:
* Order ID
* AWB Number
* Total weight as per X (KG)
* Weight slab as per X (KG)
* Total weight as per Courier Company (KG)
* Weight slab charged by Courier Company (KG)
* Delivery Zone as per X
* Delivery Zone charged by Courier Company
* Expected Charge as per X (Rs.)
* Charges Billed by Courier Company (Rs.)
* Difference Between Expected Charges and Billed Charges (Rs.)

*Part 2*

Create a summary table
* Total orders where X has been correctly charged
* Total Orders where X has been overcharged
* Total Orders where X has been undercharged



### Observations

Part 2 would be a pivot table (summary table) derived out of Part 1.

For Part 1, it is evident from the Schema, that the following rows can be so derived:

1. From Table C1
* Order ID  
* AWB Number 
* Total weight as per Courier Company (KG)
* Delivery Zone charged by Courier Company
* Charges Billed by Courier Company (Rs.)

2. Feature Engineering
* Weight slab as per X (KG)
* Weight slab charged by Courier Company (KG)
* Expected Charge as per X (Rs.)
* Difference Between Expected Charges and Billed Charges (Rs.)

3. Rest Of the columns (to be derived by joining tables)
* Delivery Zone as per X
* Total weight as per X (KG)



### Tools required to Analyse Data

Given that the data does not have too many rows, we can use **Ms-Excel** to do this. For joining tables, we can use **Power Query Editor.** 


# Step 4: Putting Everything Together

1. We copy Order No. and Order Qty data from X1 table to another tab in the same file.
2. Name that tab X1_Dash 
3. Group X1_Dash by Order No. using pivot table
 &nbsp; Alt+N+V --> Enter
4. Put Order No. in Rows and Order Qty in values
5. We get a table with Weight (g) for each SKU value
6. Merge it with X1 

 &nbsp; Go to Data Tab --> Get Data --> Combine Queries --> Merge
 
7. We get a table with Weight (g) and Qty. for Order No. (not unique, yet)

Now, we will group the data in X1 by Order No. to get total Qty and weight for each Order No.

8. Select the data --> Alt+N+V
9. In pivot table, put Order No. and SKU in "Rows" and Order Qty. and Weight in values
10. We get a table X1 with Weight and Qty for each order no.

<br/>

#### The Main Table

1. Open a new Excel File, name it "Calculations_Intermediate.xlsx"
2. Copy the following colums from C1 as it is:
* Order ID  
* AWB Number 
* Total weight as per Courier Company (KG)
* Delivery Zone charged by Courier Company


Next, open an excel file, save it as "All_Merges.xlsx"

1. Go to **Data Tab** --> **Get Data** --> **Launch Power Query Editor...** --> **New Source** --> **File** --> **Excel Workbook**
2. Load the tables: C1, X1, X2, and X3

### Creating "Total Weight as per X" Column
1. Data Tab --> Get Data --> Combine Queries --> Merge
2. Inner Join tables X1 and C1 on *Order ID Column*

We get a table with Total Weight(g) and Qty. for each Order ID. To get the Total_Weight(g), we mutiply the weights(g) by Order Qty. column

5. Order the table in ascending order (by Order ID) copy the Total_Weight(g). 
6. Go to Calculations_Intermediate.xlsx, order the table in ascending order by Order ID
7. Paste the weights and name the column: "Total Weight as per X"
8. Ctrl + S to save the all changes


### Creating "Delivery Zone as per X" Column

1. In All_Merges.xlsx, load the table Calculations_Intermediate.xlsx <br/>
 &nbsp; Go to **Data Tab** --> **Get Data** --> **Launch Power Query Editor...** --> **New Source** --> **File** --> **Excel Workbook**
2. Inner Join tables X2, and C1 on *Customer Pincode Column* save the merge as "Delivery Intermediate"

We get a table with Zone as per X for each Customer Pincode

3. Inner Join Calculations_Intermediate.xlsx and Delivery_Intermediate name the merge Calculations
4. Copy the table Calcualtions, open an excel file, and paste the table as values <br/>
 &nbsp; Ctrl+Alt+V --> Paste as Values
5. Save the file as Calculation.xlsx

### After all this we have, the following columns in Calculations.xlsx

* Order ID  
* AWB Number 
* Total weight as per Courier Company (KG)
* Delivery Zone charged by Courier Company
* Delivery Zone as per X
* Total weight as per X (g)


# Step 5: Feature Engineering

Now, we shall derive columns from the existing columns.

* Total weight as per X (g)

First let's convert this into KGs:
1. Type 1000 on any cell outside the table and copy that cell
2. Select the "Total weight as per X (g)" column Ctrl + Down Arrow
3. Ctrl + Alt + V --> Multiply
4. Rename the column as "Total weight as per X (KG)"


* Weight slab as per X (KG) & Weight slab charged by Courier Company (KG)

1. Create a small table such as one below
2. Name two new columns as Weight slab as per X (KG) & Weight slab charged by Courier Company (KG)
3. Use VLOOKUP Function with Approximate Match to fill the values
 &nbsp; Approximate Match will give the values even if it doesn't exist. If the value is less than what I am looking for it will append the values


* Expected Charge as per X (Rs.)

1. Create a new column "Expected Charge as per X (Rs.)"
2. Use the HLOOKUP function "=" on C2 (Redesigned Table)
3. Select the entire column
4. Do Ctrl+D 
 &nbsp; Makes sure the formula is applied to each selected cell 


* Difference Between Expected Charges and Billed Charges (Rs.)
1. "Charges Billed by Courier Company (Rs.)" - "Expected Charge as per X (Rs.)"
2. Select the entire column 
3. Do Ctrl+D


#### Now, we have a csv file with following columns
* Order ID
* AWB Number
* Total weight as per X (KG)
* Weight slab as per X (KG)
* Total weight as per Courier Company (KG)
* Weight slab charged by Courier Company (KG)
* Delivery Zone as per X
* Delivery Zone charged by Courier Company
* Expected Charge as per X (Rs.)
* Charges Billed by Courier Company (Rs.)
* Difference Between Expected Charges and Billed Charges (Rs.)


Save all the change in the file. <br/><br/><br/>

![Screenshot 2022-08-01 185515](https://user-images.githubusercontent.com/91784043/182167841-913f58a1-d493-494e-8976-4aa0efe9438e.png)

<br/>

### This was part 1 of the problem.


# Step 6: Pivot Table

1. Select the entire Calculations.xlsx table (Ctrl+A)
2. Press Alt+N+V --> Enter

A pivot table is created in another tab.

3. Put Order ID under Rows 
4. Put "Difference Between Expected Charges and Billed Charges (Rs.)" "Charges Billed by Courier Company (Rs.)" under values
5. Use the function =COUNTIF(B4:B127, "=0") to get count of correctly charged orders.
6. Use the function =SUMIF(B4:B127,"=0",C4:C127) to get sum of correctly charged orders.
7. Replace "=0"  as condition with >0 to get undercharged orders
8. Replace "=0"  as condition with <0 to get overcharged orders

In this manner, we have created a table that looks like this: <br/><br/><br/>
![Screenshot 2022-08-01 185248](https://user-images.githubusercontent.com/91784043/182167863-353849d8-3a75-4f60-aec9-2a235a1630de.png)


# Step 7: Clean Up

1. Copy the table derived from pivot table, paste it in a csv file and save it as Summary.xlsx
2. In Calculations.csv, delete the tab where pivot table was created.
3. Save both files in a folder, say, "CoinTab Solutions"
4. Right click, convert it into a zip file and send.
