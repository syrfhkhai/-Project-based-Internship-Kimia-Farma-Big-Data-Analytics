# Project based Internship Kimia Farma Big Data Analytics
Contributor : Syarifah Khairunnisa

## Program Description
The Project Based Internship Program in collaboration with Rakamin Academy and Kimia Farma Big Data Analytics is a self-development and career acceleration program intended for those of you who are interested in exploring the Big Data Analytics position at the Kimia Farma company. This program provides access to basic learning in the form of Article Reviews (reading materials) and Company Coaching Videos (video learning) to introduce you to the competencies and skills that Big Data Analytics must have in companies. Apart from the material, there will be testing of your learning outcomes in the form of assignment questions every week and ending with the creation of a final assignment which will become your portfolio in this program. The final task that will be created in this program is that you are asked to create a dashboard from sales data for one of Kimia Farma's products in one year from the raw data that has been provided.

### Objectives
* Design data analysis table from the existing raw dataset with BigQuery
* Design company performance report visualization/dashboard with Looker Studio

### Dataset
The dataset provided consists of the following tables:
* kf_final_transaction
* kf_inventory
* kf_kantor_cabang
* kf_product

[Downloads Data Set](https://drive.google.com/drive/folders/11SGb3FOF9RG1jgzDp1OUG3Nemklngx7w?usp=sharing)

### Tools
Tool : Google Cloud BigQuery

Visualization : Looker Data Studio

## Data Analysis
As a data analyst, your have to create a summary table by combining the data from the provided raw table, with the following mandatory columns:

* transaction_id : kode id transaksi
* date : tanggal transaksi dilakukan
* branch_id : kode id cabang Kimia Farma
* branch_name : nama cabang Kimia Farma
* kota : kota cabang Kimia Farma
* provinsi : provinsi cabang Kimia Farma
* rating_cabang : penilaian konsumen terhadap cabang Kimia Farma
* customer_name : Nama customer yang melakukan transaksi
* product_id : kode product obat
* product_name : nama obat
* actual_price : harga obat
* discount_percentage : Persentase diskon yang diberikan pada obat
* persentase_gross_laba : Persentase laba yang seharusnya diterima dari obat dengan ketentuan berikut:
  * Harga <= Rp 50.000 -> laba 10%
  * Harga > Rp 50.000 - 100.000 -> laba 15%
  * Harga > Rp 100.000 - 300.000 -> laba 20%
  * Harga > Rp 300.000 - 500.000 -> laba 25%
  * Harga > Rp 500.000 -> laba 30%,
* nett_sales : harga setelah diskon
* nett_profit : keuntungan yang diperoleh Kimia Farma
* rating_transaksi : penilaian konsumen terhadap transaksi yang dilakukan

<details>
  <summary> Click to see query </summary>
    <br>
    
```sql
CREATE TABLE kimia_farma.kf_analysis AS
SELECT
  f.transaction_id,
  f.date,
  f.branch_id,
  c.branch_name,
  c.kota,
  c.provinsi,
  c.rating AS rating_cabang,
  f.customer_name,
  f.product_id,
  p.product_name,
  f.price AS actual_price,
  f.discount_percentage,
  CASE
    WHEN f.price <= 50000 THEN 0.10
    WHEN f.price > 50000 AND f.price <= 100000 THEN 0.15
    WHEN f.price > 100000 AND f.price <= 300000 THEN 0.20
    WHEN f.price > 300000 AND f.price <= 500000 THEN 0.25
    ELSE 0.30
  END AS persentase_gross_laba,
  (f.price - (f.price * f.discount_percentage / 100)) AS nett_sales,
  ((f.price - (f.price * f.discount_percentage / 100)) * 
    CASE
      WHEN f.price <= 50000 THEN 0.10
      WHEN f.price > 50000 AND f.price <= 100000 THEN 0.15
      WHEN f.price > 100000 AND f.price <= 300000 THEN 0.20
      WHEN f.price > 300000 AND f.price <= 500000 THEN 0.25
      ELSE 0.30
    END) AS nett_profit,
  f.rating AS rating_transaksi
FROM
  kimia_farma.kf_final_transaction f
JOIN
  kimia_farma.kf_kantor_cabang c
ON
  f.branch_id = c.branch_id
JOIN
  kimia_farma.kf_product p
ON
  f.product_id = p.product_id;
;
```
    
<br>
</details>
<br>

>The query can also be accessed through the file: query.txt

>The tabel can be accessed trough the file: Dataset\kf_analisa

## Data Visualization
[See Looker Studio](https://lookerstudio.google.com/reporting/84c59f6c-1c35-4410-a03f-7b2b45a36012)

To support visualization, a new table was generated to explore the top five branches with the best ratings but the lowest transaction ratings:
 
<details>
  <summary> Click to see query </summary>
    <br>
    
```sql
CREATE TABLE kimia_farma.kf_branch_analysis_lim AS
SELECT c.branch_id, 
  c.branch_name, 
  AVG(tr.rating) AS rating_transaction,
  c.rating AS rating_branch
FROM kimia_farma.kf_kantor_cabang AS c
  LEFT JOIN kimia_farma.kf_final_transaction AS tr
  ON (c.branch_id = tr.branch_id)
GROUP BY c.branch_id, c.branch_name, c.rating
ORDER BY AVG(tr.rating) ASC, c.rating DESC
LIMIT 5
;
```
<br>
</details>
<br>

> The query can also be accessed through the file: query.txt
