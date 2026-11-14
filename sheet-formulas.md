# Sheet formulas

All calculations live in the **Monthly** tab as formulas, not in n8n.
The founder (or an investor) can click any cell and check the maths.

Columns A–N hold the raw monthly data (see `sample-data/monthly_metrics.csv`).
Add these headers in row 1 and formulas in row 2, then select **O2:AC(last row)** and press **Ctrl+D**.

## Calculations

| Col | Header | Formula (row 2) |
|---|---|---|
| O | `month_date` | `=IF(ISNUMBER(A2),A2,DATEVALUE(A2&"-01"))` |
| P | `month_label` | `=TEXT(O2,"mmmm yyyy")` |
| Q | `net_new_mrr_eur` | `=C2+D2-E2` |
| R | `mrr_growth_pct` | `=IF(ROW()=2,"",ROUND((B2-B1)/B1*100,1))` |
| S | `burn_eur` | `=J2-I2` |
| T | `burn_change_pct` | `=IF(ROW()=2,"",ROUND((S2-S1)/S1*100,1))` |
| U | `runway_months` | `=IF(S2<=0,"profitable",ROUND(L2/S2,1))` |
| V | `customer_churn_pct` | `=IF(ROW()=2,"",ROUND(H2/F1*100,1))` |

## Data checks (each shows `OK` or the problem)

| Col | Header | Formula (row 2) |
|---|---|---|
| W | `check_month` | `=IF(ROW()=2,"OK",IF(EDATE(O1,1)=O2,"OK","MISSING MONTH"))` |
| X | `check_cash` | `=IF(ROW()=2,"OK",IF(ABS(L1+I2-J2+K2-L2)<=1,"OK","CASH DOES NOT ADD UP"))` |
| Y | `check_customers` | `=IF(ROW()=2,"OK",IF(F1+G2-H2=F2,"OK","CUSTOMERS DO NOT ADD UP"))` |
| Z | `check_mrr` | `=IF(ROW()=2,"OK",IF(B1+C2+D2-E2=B2,"OK","MRR DOES NOT ADD UP"))` |
| AA | `check_empty` | `=IF(COUNTBLANK(A2:N2)=0,"OK","EMPTY CELLS")` |

## Warnings and status

**AB `warnings`**
```
=IFERROR(TEXTJOIN(" · ",TRUE,IF(AND(ISNUMBER(T2),T2>20),"Burn up "&T2&"%",""),IF(AND(ISNUMBER(U2),U2<12),"Runway under 12 months",""),IF(AND(ISNUMBER(R2),R2<0),"MRR fell",""),IF(AND(ISNUMBER(V2),V2>3),"Customer churn "&V2&"%",""),IF(ROW()>2,IF(N2<N1,"Pipeline shrank",""),"")),"Cannot calculate: data error")
```

**AC `status`**
```
=IF(AND(W2="OK",X2="OK",Y2="OK",Z2="OK",AA2="OK"),IF(AB2="","OK","WARNING"),"DATA ERROR")
```

## Warning rules (change the thresholds to fit the company)

| Rule | Threshold |
|---|---|
| Burn up | more than 20 % vs last month |
| Runway | under 12 months |
| MRR fell | growth below 0 % |
| Customer churn | more than 3 % of last month's customers |
| Pipeline shrank | pipeline lower than last month |
