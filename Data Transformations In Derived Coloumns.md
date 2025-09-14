Standarizing Data Types Among Different Data Sources Before Executing ```UNION All``` Transformation

---

Calculating Discount rate ratio by deviding Discount amount by sales

```sql
(ISNULL([Order Item Discount Rate]) || TRIM([Order Item Discount Rate]) == "")?
(DT_NUMERIC,15,4)((Sales == 0) ? 0 : [Order Item Discount] / Sales)
: (DT_NUMERIC,15,4)[Order Item Discount Rate]
```

---
Replacing Nulls in ```payment``` column With "Unknown"

```ISNULL(PAYMENT) || PAYMENT == "" ? "Unknown" : PAYMENT```

<img width="1920" height="1080" alt="Desktop Screenshot 2025 09 14 - 12 56 20 64" src="https://github.com/user-attachments/assets/de42cb32-080e-447c-be43-76b3944d1327" />
