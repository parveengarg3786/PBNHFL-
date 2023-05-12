# PBNHFL-

http://mkto-k0023.com/dc/5w4dZQ4HH6-8aj1DCgc3u9vYtrgywd1eUxD2vj1MTEGf_y395O4_4sxM29-hnjTajbtq4_19QZkqnYh-UBjqLh3ViiR4d2qDydzriou3MyrMf2vZ4EBW0Uh9nkRp6-qsOOmhebP8CVSxKwHANlODKw==/MzQ3LUlBVC02NzcAAAGLmQ7f4poPi-SzdjZ9Z3zygLwrrBbwlByle2bF96yKoCDXFZM02_BAPAGoAT5LfqAeeg2KqyU=

https://pnbhousingfinance--loscrmdemo.sandbox.my.salesforce.com/?ec=301&startURL=%2Fvisualforce%2Fsession%3Furl%3Dhttps%253A%252F%252Fpnbhousingfinance--loscrmdemo.sandbox.lightning.force.com%252Flightning%252Fr%252FPayment_History__c%252Fa0Q9H000004HV0aUAG%252Fview

Hi,

ODS detail as given below…

Hostname: dbm0vm01-scan1

Username: BI_TECHMATRIX

Password: Will be shared telephonically to concerned user only

Connection Type: Basic

Role: Default

TNS Port No.: 1521

Service Name: KASPROD_NEW
TECHMATRIX@1617



Hyper

SELECT lld.loanno,lld.loanid,lld.cifid,lhd.mmr,lhd.FACILITY_TYPE,lhd.NAME_OF_INST,lhd.EFFECTIVE_TO,lhd.SCHEMEID_INST AS SCHEMEID,lhd.FUND_NAME 
FROM LMT_LOAN_DTL lld 
INNER JOIN LMT_HYPT_DTL lhd ON 
lld.loanid=lhd.loanid 
where lld.status <> 'X' 
and lld.status <> 'X' 
and lld.loanno in ('NHL/MRH/0316/276670' ,'NHL/GUR/1215/256436','HOU/MUM/1015/246052','HOU/DEL/0816/307122')


NPA Detail

select  
LLD.LOANNO, 
LNC.LOANID ,
LLD.CIFID ,
LNC.NPA_CONV_DATE NPA_Conversion_date ,
LNC.NPA_RECONV_DATE NPA_Re_Conversion_date,
LLD.DPD AS GROUP_DPD ,
LNC.LAST_UNPAID_DATE 
from LMT_NPA_CONVERSION  LNC 
INNER JOIN lmt_loan_dtl lld ON lld.LOANID =LNC.LOANID 
--where lld.loanno in ('NHL/MRH/0316/276670' ,'NHL/GUR/1215/256436','HOU/MUM/1015/246052','HOU/DEL/0816/307122') 
order by LNC.loanid


Processing and legal Fee - getting error because of functions(fnccgvalue) inside this query

SELECT    A.loanno,A.loanid,A.cifid,NVL(A.DESCRIPTION, 'Total') "Charge Description", 
             SUM(NVL(A.EFFECTIVE_CHARGE_AMT, 0)) "Charge Amount", 
             SUM(NVL(A.WAIVED_AMT, 0)) "Waived Amount", 
             SUM(NVL(A.PAIDAMT, 0)) "Paid Amount", 
             SUM(NVL(A.stax_paid, 0)) "Tax Paid", 
             SUM(NVL(A.EFFECTIVE_CHARGE_AMT - NVL(A.PAIDAMT, 0) - 
                     NVL(A.WAIVED_AMT, 0), 
                     0)) "Unpaid Amount", 
             nvl(a.mode_of_payment, ' ') "Payment Mode", 
             nvl(a.is_deduct_from_disb, ' ') "Is Deduct from Disb" 
        FROM (SELECT B.CHARGEDESCRIPTION DESCRIPTION,lld.loanno,lld.loanid,lld.cifid, 
                     NVL(A.EFFECTIVE_CHARGE_AMT, 0) EFFECTIVE_CHARGE_AMT, 
                     NVL(A.WAIVED_AMT, 0) WAIVED_AMT, 
                     NVL(A.PAIDAMT, 0) PAIDAMT, 
                     (select sum(x.paidamt) 
                        from lot_rcpt_charge_mx x, mst_dues md 
                       where x.customerchargeid = a.customerchargeid 
                         and x.status <> 'X' 
                         and md.duetypeid = x.subduetypeid) stax_paid, 
                     (SELECT fnccgvalue(lr.cg_paymentmode) 
                        FROM lot_customer_t lct, 
                             lmt_rcpt       lr, 
                             lmt_rcpt_dtl   lrd 
                       where lct.comp_appl_id = a.comp_appl_id 
                         and lct.newtoncifid = lr.cifid 
                         and lr.tranrcptid = lrd.tranrcptid 
                         and fnccgvalue(lrd.cg_event_id, 'V') = 'LOFEES' 
                         and lct.guar_coap_flag = 'P' 
                         and lr.status not in ('X', 'R') 
                         and lrd.status <> 'X' 
                         and rownum = 1) Mode_Of_Payment, 
                     a.customerchargeid customerchargeid, 
                     a.is_deduct_from_disb is_deduct_from_disb 
                FROM LOT_CHARGES_DTL A, CHARGECODE B ,lmt_loan_dtl lld 
               WHERE A.APPLLEVEL = 'A' 
                 AND lld.loanno in ('NHL/MRH/0316/276670' ,'NHL/GUR/1215/256436','HOU/MUM/1015/246052','HOU/DEL/0816/307122') 
                 AND A.CHARGEID = B.CHARGEID 
                 AND A.SUBDUETYPEID IS NULL 
                 AND A.comp_appl_id=lld.srcapplid 
                 AND A.STATUS <> 'X' 
                 AND B.STATUS <> 'X') A 
       GROUP BY A.DESCRIPTION, 
                a.mode_of_payment, 
                a.customerchargeid, 
                a.is_deduct_from_disb, 
                a.loanno,a.loanid,a.cifid

OTS Details - working fine

SELECT lld.loanno,  
lld.loanid,  
lld.cifid,  
(  
  SELECT MIN(OPD.PYMT_DATE)  
  FROM LMT_OTS_PYMT_DTL OPD  
  WHERE OPD.OTSDTLID=LOD.OTSDTLID  
  AND OPD.STATUS<>'X'  
) AS OTS_DATE, 
NVL(LOD.OTS_AMT,0) AS TOTAL_OTS_AMOUNT, 
(  
  SELECT SUM(NVL(OPD.PYMT_AMT,0))  
  FROM LMT_OTS_PYMT_DTL OPD  
  WHERE OPD.OTSDTLID = LOD.OTSDTLID  
  AND OPD.STATUS<>'X'  
) AS TOTAL_PAYMENT_AMOUNT, 
LLS.EXCESS_AMOUNT as OTS_SUSPENSE_BALANCE, 
NULL AS OTS_VALIDITY, 
MD.DUE_DESCRIPTION AS DUE_DESCRIPTION,  
WD.DUE_AMT AS DUE_AMOUNT,  
WD.MAKER_WAIVED_AMT AS WAIVED_AMOUNT,  
WD.BAL_DUE_AMT AS Balance_Amount  
 
FROM LMT_LOAN_DTL LLD  
INNER JOIN LMT_TRAN_DTL LTD ON  
  LTD.LOANID = LLD.LOANID  
INNER JOIN LMT_OTS_DTL LOD ON  
  LTD.TRANDTLID = LOD.TRANDTLID  
INNER JOIN LMT_OTS_DUE_WAIVER_DTL WD ON  
  WD.OTSDTLID = LOD.OTSDTLID  
INNER JOIN MST_DUES MD ON  
  MD.DUETYPEID = WD.DUETYPEID 
INNER JOIN LMT_LOAN_STATUS LLS ON  
  LLS.loanid = LLD.LOANID  
 
WHERE LLD.STATUS<> 'X'  
AND LTD.STATUS <> 'X'  
AND LOD.STATUS <> 'X'  
AND MD.STATUS <> 'X'  
AND LLS.STATUS <> 'X'  
and lld.loanno in ('NHL/GUR/0315/213174' ,'NHL/KOL/0817/420468','HOU/DEL/1215/256377','HOU/MUM/1116/334863')  
order by lld.loanno



Restructuring Details - works fine
select  
LLD.LOANNO, 
LRD.loanid AS LOANID 
,LLD.CIFID AS CIFID 
,LRD.RESTRUCTURING_DATE AS RESTRUCTURING_DATE 
,(SELECT DESCRIPTION 
           FROM company_generic 
            WHERE genericid = LRD.RESTRUCTURING_TYPE) AS RESTRUCTURING_TYPE 
,LRD.APPLICATION_DATE AS APPLICATION_DATE 
,LRD.APPROVAL_DATE AS APPROVAL_DATE 
,(SELECT DESCRIPTION FROM COMPANY_GENERIC CG WHERE CG.GENERICID=LRD.APPROVED_BY) AS APPROVED_BY 
,(select EMPLOYEENAME from employee e where e.employeecode=LRD.MAKERID) AS MAKERID 
,LRD.MAKEDATE AS MAKEDATE 
,(select EMPLOYEENAME from employee e where e.employeecode=LRD.CHECKERID) AS CHECKERID 
,LRD.CHECKERDATE AS CHECKERDATE 
,LRD.TOTAL_OVERDUE AS TOTAL_OVERDUE 
,LRD.OVERDUE_ADDED_TO_POS AS Overdue_Capitalized 
,LRD.POS_FOR_RESTRUCTURING AS POS_FOR_RESTRUCTURING 
,LRD.EMI_MORAT_PERIOD AS EMI_Moratorium_Prd 
,LRD.PRIN_MORAT_PERIOD AS PRIN_MORAT_PERIOD 
,LRD.TENURE_EXT_PERIOD AS EXTENSION_PERIOD 
,LRD.LN_NEW_END_DT AS NEW_END_DT 
,LRD.STEP_UP AS Step_Up_Flag 
,LRD.PROPOSED_EMI AS PROPOSED_EMI 
,LRD.DISCOUNT AS Fixed_Prd_Discount 
,LRD.FIXED_RATE_DISCOUNT AS Floating_Prd_Discount 
,LRD.OLD_TENURE AS OLD_TENURE 
,LRD.NEW_TENURE AS NEW_TENURE 
,LRD.OLD_INTEREST_RATE AS OLD_ROI 
,LRD.NEW_INTEREST_RATE AS NEW_ROI 
from  
LMT_RESTRUCTURE_DTL LRD, 
LMT_LOAN_DTL     LLD 
WHERE LRD.loanid = LLD.loanid 
and lld.loanno in ('NHL/BAN/0115/210663' ,'NHL/NAG/0215/213040','HOU/JAN/0215/212085','HOU/NAG/0215/212762') 
AND LRD.STATUS <> 'X'
