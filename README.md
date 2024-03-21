# Adventure-Works
Repo of an adventure works dashboard created in Power BI. This project demonstrates skills both in the frontend and backend side of Power BI. On the backend side an ETL or extract transform load process was done. On the backend side various things were done including data cleaning and transforming in Power Query, creating a developing an effective data model as well as creating new DAX measures and calculated columns. On the frontend side 5 different pages were created showcasing different things.

1. Executive Dashboard: A summary at the executive level highlighting and summarizing the most important metrics like total revenue and total profit, top 10 products by orders and much more.

2. Map Dashboard: A dynamic map view showing by region different metrics including total order and profits indicated by density bubbles.

3. Product Dashboard: A drill through page summarizing at the product level showing useful information including metrics such as monthly orders vs expected target, time series profit over time etc.

4. Customer Dashboard: A page dedicated to customer information including top 100 customers by revenue, customer occupation breakdown and more.

5. A Decomposition tree to help identify which product is a problem by breaking down the return rate by product name.


## Background

Adventure works is a theoretical bike shop and this data describing product sales from January 2020 - June 2022, therefore two and a half years of product data. There is 50 000 + sales transactions and therefore this dataset is realistic for a small - medium sized company. Adventure works sells bikes, accessories and clothing.

## ETL

As stated early I followed an ETL process or extract transform load in the backend in Power BI extracting data from Excel, transforming the data in Power Query and loading it into Power BI's front end.

### Extract

Data was taken and organized from CSV files and was quite easy to import into Power Query, as the data was clearly named correctly and organized in a folder, the files used to import were organized like below:


![Capture](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/a26d0234-39d5-4ddb-b2b4-d05b22adf5fd)


### Transform
Next in the ETL process is the transform step, here we take the data and transform it in Power Query, here we have 8 tables that we have created in the backend, I wont dive into each transformation step and table, but will display one table that had significant changes, in particular the customer table, below is a screenshot showing the customer table before any transformations.

![customer not transformed](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/4053e7cd-7e4f-4165-ace7-a9dc41c75398)

Here we can see the table overview as well as the steps on the right-hand pane that will be applied. Here are the steps in order:

**Promoted Headers:** Sets the headers of the columns we see this already in the picture before, Power BI does this right away.

**Changed Type:**  Annual income, customer key, total children were all changed from text to a number and birthdate was changed to a date.

**Removed Errors:** removed any customer keys that had errors.

**Filtered Rows:** Extension of remove errors: looked for any null or blank values in customer key and removed them.

**Capitalized each Word:** changed prefix, first and last name to be in proper case.

**Insert Merged Column:** Created a full name column using prefix, first name and last name.

**Duplicated Column:** Copied email address column

**Reordered Column:** move the copied email address beside the original email address column.

**Renamed Columns:** Changed the copied email address column name to domain name.

**Extracted Text Between delimiters:** extracted the domain name using the @ symbol as the start and a . as the end delimiter
 
**Trimmed Text:** Trimmed the domain field making sure no leading or trailing spaces.

**Capitalized Each word1:** Changed the domain name to be in proper case.

**Replaced Value:** Removed the hyphens in the domain name with a space to just get the domain name.

These were the steps applied to this table which outputted the result table below:


![customer transformed](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/0f2bc217-4e64-4835-9d4b-6a9de5a29e02)

Similar set of steps were applied to the other tables, I just wanted to showcase at least one table to show how I would transform the data in Power Query. I also want to add that the sales data was combined using different years into one table.

### Load

Data now cleaned and transformed is ready to be loaded into Power BI's Front end.



### Data Model
Below is the data model for our adventure works report, we can clearly see how each table is related to each other. We have two fact tables and 6-dimension tables. Everything thing here uses many to one cardinality and single cross filter direction, making this data model more efficient and easier to understand.

![data model](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/c8f35b4f-ace2-4a42-ab72-4c3e4e6cc40c)

### Measure Table
For our report many different measures were constructed, written using DAX, below is an image of all the measures in the report: 

![measure table](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/f343beb3-0ded-4152-ac53-af6f0c32a333)

it would be unrealistic to show every single measure calculation but i will show below some of the more important ones used for this report.

#### Total Orders

```dax
Total Orders = DISTINCTCOUNT('Sales Data'[OrderNumber])
```

#### Total Returns

```dax
Total Returns = COUNTROWS('Returns Data')
```

#### Total Revenue
 
```dax
Total Revenue = SUMX(
    'Sales Data', RELATED('Product Lookup'[ProductPrice])*'Sales Data'[OrderQuantity])
```


#### Total Cost

```dax
Total Cost = SUMX('Sales Data', 'Sales Data'[OrderQuantity]* related('Product Lookup'[ProductCost]))
```


#### Total Profit

```dax
Total profit = [Total Revenue] - [Total Cost]
```


#### Return Rate

```dax
Return Rate = IFERROR([Quantity Returned]/[Quantity Sold],"No Sales")
```


These are just some examples of the different DAX measures made to use in this report. I find it useful to make explicit DAX measures compared to just dragging raw fields into a visual because it allows us to use it everywhere in the report.


### Report View

#### Exec Dashboard

![image](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/6a737938-80d6-4032-852b-50b058401449)

Here is the Exec Dashboard for our Adventure Works Report.

At the top of the report, we have our 4 KPI cards of total revenue, total profit, total orders and return rate.

Next, we have our order by category showing the total amount of orders based on the product category

To the left of that we have Revenue time series graph, we also have a slicer that controls the zoom level of the graph getting more detail if wanted.

To the right of that we have our top 10 products by total orders, underneath that we have the most ordered product and most returned product.

To the left of that we have 3 KPI cards showing how we are doing compared to the previous month, red indicates we are performing worse than the previous month and black means we are performing better than the previous month.

Finally, to the very far left we have a pane that has buttons that can take us to different pages in this report as well as a clear all filters’ buttons.

This page is entirely interactive meaning by filtering one graph/chart, the other visuals in this report filter as well. For example, if we click on the Accessories of the orders graph, it will filter everything else in the report to only consider accessory products. This is an excellent way to quickly see how different things are performing if we desire that.


#### Map Dashboard

![image](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/af351e33-49d4-4273-838e-15506dd1be29)
![Uploading Exec dashboard.PNG…]()

Here is our Dynamic Map visual for our Adventure works report. Here we can see a map and various different parameters that we can change, the bubbles on the map represent how much each country has compared to others, the larger the bubble the more of that parameter it has. Here we can dynamically change the map value to be Total Orders, Quantity Sold, Total Revenue, Total Returns, Return Rate and Total Profit. 

Here we can also filter by region if we are only interested in a particular regions performance

#### Product Dashboard

![image](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/dc077cb5-4835-40f7-ba54-ec70c3ff4393)

Here is our Product Dashboard, we created a drill through page meaning we can select any product name and it will update all other visuals on this page, this is particularly useful because we can see how individual products are performing super easily. 

At the top left we can see the currently selected product; this updates dynamically based on which product you selected. 

The gauge charts on the top look at how each product is performing this month compared to its expected value. The middle represents the current month value and the value on the far right represents the target goal for the month. Red indicates performing worse than expected and black is better than expected.

The graph in the middle shows the total profit over time, there is also an adjustment factor which can be applied to see how profit would change if the product price were to change as well. 

Finally, the last graph on the below is a dynamic graph where the Y value can be changed on the left side under the product metric selection slicer.


#### Customer Dashboard

![image](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/4505262e-99f3-41ab-8fc6-9b3dd8339baa)

Here is our Customer Dashboard showing data at the customer level on the top left we can see two cards; this shows the total unique customers and the average revenue per customer.

To the right we can see a graph that can show two different things, time series of revenue per customer and the total customers.

To the bottom left we have two donut charts breaking down the customers by income level and by occupation.

To the right we can we see the top 100 customers by total orders.

Finally, to the right of that we have a year slicer being able to filter this page by year as well as cards underneath showing the top customer by revenue and that customers actual dollar revenue and total orders.

#### Bonus: Decomposition Tree

![image](https://github.com/Tyler-Wilson-326/Adventure-Works/assets/141818698/e3b32509-997e-49ba-bf42-e384a11ee490)

Here is a decomposition tree with the purpose of trying to find adventure works worst performing product, from here we use return rate as an indicator of what is considered a bad product. We can see here the Road-650 Red, 52 road bike to be contributing the most to the return rate. This visual is interesting because it can really help narrow down where your worst performing products are in a clean way.

## Conclusion

There we have it, this was my adventure works report, i really enjoyed creating this as it taught me how to work in both the backend and frontend of Power BI. I learned a lot about and ETL workflow, data models, dax measures and general good report construction.

## Credit

Full Credit to the Chris Dutton, Aaron Parry and the Maven Analytics Team for providing the data, found [here:] (https://www.udemy.com/course/microsoft-power-bi-up-running-with-power-bi-desktop/)

