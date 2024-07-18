# Coffee House Sales-Dashboard

![Screenshot 2024-07-18 115428](https://github.com/user-attachments/assets/e92c323b-89f3-4f9d-a8b8-51f26b3df2d3)


### Dashboard Link : https://app.powerbi.com/groups/me/reports/36b7608a-9146-4310-b7e2-7cab5095d295/c8208dfe062c2851b235?experience=power-bi
## Problem Statement

This interactive  dashboard helps the Coffee House Owner to get an insight on the performance of his/her shop in terms of different KPMs over several different periods in time.
A simple look into the dashboard and the person knows how the sales are going this week/month compared to the previous.

** Key point metrics used are Total Sales, Total Orders and Total Quantity Sold.

Key insights--

    1.Total sales, total orders and total quantity sold in a month.

    2.Comparison of the KPMs of current month with the previous month.

    3.Comparative look in the sales by Weekdays and Weekends using a dount chart.

    4.Total sales of each day in a month and seeing whether it is above or below the average daily sale mark

    5.Sale by Product category

    6.Sale by Location of stores

    7.Top ten selling products

Tool Tip feature used so that all the KPMs for a particular day can be looked into.

### Documentation of SQL Queries used for conformation of calculations

** Lag(),count(),sum(),over(),order by,round(),concat(),avg()


    1. Total sales for month and comparison with previous month

        select
        month(transaction_date)as curr_month,
        round(sum(transaction_qty*unit_price)) as total_sales,
        (sum(transaction_qty*unit_price)-lag(sum(transaction_qty*unit_price),1) over(order by month(transaction_date)))
        /lag(sum(transaction_qty*unit_price),1) over(order by month(transaction_date))*100 as sales_growth_percentage
        from
        beverage_sales
        where
        month(transaction_date) in (4,5)
        group by
        month(transaction_date)
        order by
        month(transaction_date)

![Screenshot 2024-07-12 104004](https://github.com/user-attachments/assets/c3ed4fc7-1026-432a-8eef-d5aca9abdf09)




    2. Total orders for month and comparison with previous month


        select
        month(transaction_date) as curr_month,
        count(transaction_id) as total_orders,
        (count(transaction_date)-lag(count(transaction_date),1) over(order by month(transaction_date)))
        /(lag(count(transaction_id),1) over(order by month(transaction_date)))* 100 as order_growth
        from
        beverage_sales
        where
        month(transaction_date) in (4,5)
        group by
        month(transaction_date)
        order by
        month(transaction_date)

![Screenshot 2024-07-12 103945](https://github.com/user-attachments/assets/c7362b66-e889-4233-ba5e-4c00a35facc4)



    3. Total Quantity sold for month and comparison with previous month

        select
        month(transaction_date) as curr_month,
        sum(transaction_qty) as total_qty_sold,
        (sum(transaction_qty) - lag(sum(transaction_qty),1) over(order by month(transaction_date)))
        /lag(sum(transaction_qty),1) over(order by month(transaction_date))*100 as growth_sold_quantity
        from
        beverage_sales
        where
        month(transaction_date) in (4,5)
        group by
        month(transaction_date)
        order by
        month(transaction_date)

![Screenshot 2024-07-12 103922](https://github.com/user-attachments/assets/272baf61-4574-4088-a947-d1647b80f003)


    4. Partitioning data of month by weekdays and weekends

        select
        case when dayofweek(transaction_date) in (1,7) then "Weekends" 
        else "Weekdays"
        end as day_type,
        concat(round(sum(transaction_qty * unit_price)/1000,1),'K')as total_sales
        from 
        beverage_sales
        where
        month(transaction_date)=5
        group by
        case when dayofweek(transaction_date) in (1,7) then "Weekends"
        else "Weekdays"
        end

![Screenshot 2024-07-12 111825](https://github.com/user-attachments/assets/62466389-9ac6-43ce-95e2-999f6aeae20d)



    5. Finding the average daily sales of a month

        select
        avg(totalsales_daily) as avg_daily_sale
        from
            (select
                sum(transaction_qty * unit_price) as totalsales_daily
            from beverage_sales
            where month(transaction_date)=5
            group by transaction_date
            )as InternalQuery

![Screenshot 2024-07-18 113107](https://github.com/user-attachments/assets/9eb90bb2-a6bc-4494-a8be-37edd09532e6)



    6. Retriving sales by product type

        select
        product_type,
        concat(round(sum(transaction_qty * unit_price)/1000,2),'K') as total_sales
        from
        beverage_sales
        where
        month(transaction_date)=5
        group by
        product_type
        order by sum(transaction_qty * unit_price) DESC
![Screenshot 2024-07-18 113446](https://github.com/user-attachments/assets/4f69c2ba-47f9-4c4f-82d9-1b744ded2614)


    7. Having the sales by different hours in a day over the month

        select
        hour(transaction_time) as current_hour,
        sum(transaction_qty*unit_price) as total_sales
        from
        beverage_sales
        where
        month(transaction_date)=5
        group by
        hour(transaction_time)
        order by
        sum(transaction_qty*unit_price) desc

![Screenshot 2024-07-18 113637](https://github.com/user-attachments/assets/944073b2-9bd0-4af9-a474-f238f992db7a)
