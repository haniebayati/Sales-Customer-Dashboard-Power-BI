#  Sales & Customer Dashboard – Power BI

داشبورد تحلیلی فروش و مشتریان برای بررسی عملکرد، روند رشد و تحلیل محصول و مشتری.  
هدف پروژه: استخراج insight تجاری عملی و کمک به تصمیم‌گیری مدیریتی.

---

##  اهداف پروژه

- تحلیل فروش و تعداد سفارش‌ها
- بررسی روند رشد سالانه و تغییرات ماهانه  
- تحلیل سودآوری محصولات و دسته‌بندی‌ها  
- تحلیل رفتار مشتریان و سهم هر سگمنت  
- استخراج insight عملی برای تصمیمات مدیریتی  

---

##  ابزار مورد استفاده

- **Power BI** برای تحلیل و visualization  
- **Power Query** برای پاکسازی و آماده‌سازی داده  
- **DAX** برای محاسبه Measures  
- **Star Schema** برای مدل داده

---

##  ساختار داده

**Fact Table:**
- Orders: `orderid`, `customerid`, `cost`, `order date`, `productid`, `quantity`, `region id`, `sales`

**Dimension Tables:**
- Customers: `customerid`, `name`, `segment`  
- Products: `productid`, `categoryid`, `price`, `product name`  
- Category: `categoryid`, `category name`  
- Region: `regionid`, `region name`  
- Date: `date`, `month`, `year`, `day` (ساخته شده با DAX)

---

## روابط
  - Customers → Orders (یک به چند)  
  - Products → Orders (یک به چند)  
  - Category → Products (یک به چند)  
  - Date → Orders (یک به چند)
  - Region → Orders (یک به چند)

---
## ساختار پروژه

### 1. نمای کلی مدیریتی
 **هدف:** رصد روند فروش و سود در مناطق و سال های مختلف.

  
   **KPIها:** مجموع فروش، سود ناخالص، حاشیه سود، رشد فروش سالانه
 
   **ویژوال‌ها:**  
    نمودار خطی فروش براساس ماه، نمودار میله ای 5 محصول برتر
     
   **فیلترها (Slicer):** منطقه، سال
   
   **تعامل:** بروزرسانی دینامیک تمام ویژوال‌ها و KPIها با انتخاب فیلترها

### 2. تحلیل محصول و دسته بندی برای شناسایی محصولات سودده و کم سود
  
   **ویژوال‌ها:**  
    ماتریس دسته بندی (زیرشاخه با مقادیر فروش، سود ناخالص)، نمودار میله ای سود براساس دسته بندی

     
### 3. تحلیل مشتری و منطقه برای تمرکز منابع فروش
  
   **ویژوال‌ها:**  
   نمودار میله ای فروش براساس منطقه، نموار میله ای سود براساس بخش مشتری، جدول مشتریان برتر

 ---   

 
## Insightهای استخراج شده

در West – 2023 بیشترین فروش در August بوده، در 2022 در September → پیشنهاد: کمپین فروش West زودتر شروع شود.


Beta Laptop پر فروش‌ترین محصول و Delta Desk یک دوم فروش Beta Laptop را دارد → نیاز به بررسی دلایل فروش پایینDelta Desk.


مشتریان حقوقی/شرکتی (B2B) حدود 2 برابر بیشتر از مشتریان آنلاین فروش داشته‌اند → تمرکز بیشتر روی توسعه مشتریان B2B سودآور است.


Gross Profit و Profit Margin تحلیل شدند تا سوددهی محصولات و مناطق مشخص شود، به طوری که مدیر بتواند تصمیمات قیمت‌گذاری و تمرکز مارکتینگ را بهینه کند.

---

## نتیجه‌گیری

این پروژه نمونه‌ای از چرخه کامل تحلیل داده در Power BI است:

Data Cleaning → Modeling → DAX → Visualization → Insight Extraction

---
##  برخی از Measures 
```DAX

-- فروش کل
Total Sales = SUM(Orders[Sales])

-- تعداد سفارش‌ها
Total Orders = COUNTROWS(Orders)

-- تعداد کالاهای فروخته شده
Total Quantity = SUM(Orders[Quantity])

-- Gross Profit
Gross Profit = SUM(Orders[Sales]) - SUM(Orders[Cost])

-- Profit Margin
Profit Margin % = DIVIDE([Gross Profit], [Total Sales], 0)

-- رشد فروش سالانه (YoY)
YoY Sales Growth =
VAR CurrentYearSales = CALCULATE([Total Sales], YEAR(Orders[Order Date]) = SELECTEDVALUE(Date[Year]))
VAR PreviousYearSales = CALCULATE([Total Sales], YEAR(Orders[Order Date]) = SELECTEDVALUE(Date[Year]) - 1)
RETURN DIVIDE(CurrentYearSales - PreviousYearSales, PreviousYearSales, 0)

-- میانگین فروش هر سفارش
Average Sales per Order = DIVIDE([Total Sales], [Total Orders], 0)

-- میانگین تعداد کالا در هر سفارش
Average Quantity per Order = DIVIDE([Total Quantity], [Total Orders], 0)

-- Top 5 Products
Top 5 Products =
TOPN(5, SUMMARIZE(Orders, Orders[ProductID], "ProductSales", SUM(Orders[Sales])), [ProductSales], DESC)

-- Top 5 Customers
Top 5 Customers =
TOPN(5, SUMMARIZE(Orders, Orders[CustomerID], "CustomerSales", SUM(Orders[Sales])), [CustomerSales], DESC)

-- فروش کل هر Region
Sales by Region = SUM(Orders[Sales])

-- سهم هر Region از فروش کل
Region Share % = DIVIDE([Sales by Region], [Total Sales], 0)

