

```R
require('RPostgreSQL')
require('DBI')
```


```R
df<-read.csv('loan_5k_data_new.csv')
```


```R
head(df)
```


<table>
<thead><tr><th scope=col>member_id</th><th scope=col>loan_amnt</th><th scope=col>funded_amnt</th><th scope=col>installment</th><th scope=col>issue_d</th><th scope=col>dti</th><th scope=col>total_rec_prncp</th><th scope=col>total_rec_int</th><th scope=col>total_rec_late_fee</th><th scope=col>initial_list_status</th><th scope=col>⋯</th><th scope=col>bc_open_to_buy</th><th scope=col>bc_util</th><th scope=col>sec_app_earliest_cr_line</th><th scope=col>sec_app_inq_last_6mths</th><th scope=col>sec_app_mort_acc</th><th scope=col>sec_app_open_acc</th><th scope=col>sec_app_revol_util</th><th scope=col>sec_app_open_act_il</th><th scope=col>sec_app_chargeoff_within_12_mths</th><th scope=col>sec_app_collections_12_mths_ex_med</th></tr></thead>
<tbody>
	<tr><td>NA      </td><td>10000   </td><td>10000   </td><td>346.32  </td><td>Nov-2008</td><td>20.58   </td><td>4436.30 </td><td>1788.80 </td><td>34.60038</td><td>f       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>        </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td></tr>
	<tr><td>NA      </td><td>15000   </td><td>15000   </td><td>356.15  </td><td>Apr-2011</td><td> 9.45   </td><td> 867.37 </td><td> 907.53 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>        </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td></tr>
	<tr><td>NA      </td><td>25000   </td><td>25000   </td><td>658.74  </td><td>Mar-2011</td><td> 1.83   </td><td>2092.68 </td><td>3164.52 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>        </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td></tr>
	<tr><td>NA      </td><td> 6500   </td><td> 6500   </td><td>218.02  </td><td>Apr-2011</td><td>20.39   </td><td>1235.05 </td><td> 502.39 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>        </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td></tr>
	<tr><td>NA      </td><td> 2200   </td><td> 2200   </td><td> 72.54  </td><td>Sep-2011</td><td> 3.20   </td><td> 207.83 </td><td>  81.07 </td><td>14.93383</td><td>f       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>        </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td></tr>
	<tr><td>NA      </td><td>16000   </td><td>16000   </td><td>423.82  </td><td>Feb-2012</td><td>18.90   </td><td> 478.96 </td><td> 790.37 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>        </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td></tr>
</tbody>
</table>



check for repeating and null values in the dataset.


```R
summary(df)
```


     member_id        loan_amnt      funded_amnt     installment     
     Mode:logical   Min.   : 1000   Min.   : 1000   Min.   :  27.68  
     NA's:5000      1st Qu.: 8000   1st Qu.: 8000   1st Qu.: 247.29  
                    Median :13000   Median :13000   Median : 376.62  
                    Mean   :15207   Mean   :15201   Mean   : 449.85  
                    3rd Qu.:20000   3rd Qu.:20000   3rd Qu.: 611.82  
                    Max.   :40000   Max.   :40000   Max.   :1415.96  
                                                                     
         issue_d          dti         total_rec_prncp total_rec_int    
     Mar-2016: 127   Min.   :  0.00   Min.   :    0   Min.   :    0.0  
     Oct-2018: 121   1st Qu.: 11.82   1st Qu.: 2796   1st Qu.:  679.3  
     Dec-2015: 104   Median : 17.79   Median : 6679   Median : 1467.4  
     Aug-2018:  99   Mean   : 18.46   Mean   : 9333   Mean   : 2424.1  
     Jun-2018:  99   3rd Qu.: 24.12   3rd Qu.:13430   3rd Qu.: 3127.7  
     Dec-2016:  97   Max.   :362.00   Max.   :40000   Max.   :27393.6  
     (Other) :4353   NA's   :6                                         
     total_rec_late_fee initial_list_status   out_prncp     last_credit_pull_d
     Min.   :  0.000    f:1602              Min.   :    0   Feb-2019:3069     
     1st Qu.:  0.000    w:3398              1st Qu.:    0   Jan-2019: 183     
     Median :  0.000                        Median :    0   Oct-2016: 124     
     Mean   :  1.399                        Mean   : 4489   Nov-2018: 119     
     3rd Qu.:  0.000                        3rd Qu.: 6587   Jul-2018: 118     
     Max.   :474.500                        Max.   :39508   Oct-2018: 107     
                                                            (Other) :1280     
     chargeoff_within_12_mths disbursement_method debt_settlement_flag
     Min.   :0.0000           Cash     :4820      N:4917              
     1st Qu.:0.0000           DirectPay: 180      Y:  83              
     Median :0.0000                                                   
     Mean   :0.0122                                                   
     3rd Qu.:0.0000                                                   
     Max.   :6.0000                                                   
                                                                      
             term                  loan_status                 purpose    
      36 months:3555   Fully Paid        :2282   debt_consolidation:2879  
      60 months:1445   Current           :2020   credit_card       :1078  
                       Charged Off       : 626   other             : 346  
                       Late (31-120 days):  34   home_improvement  : 327  
                       In Grace Period   :  20   major_purchase    : 118  
                       Late (16-30 days) :  11   medical           :  53  
                       (Other)           :   7   (Other)           : 199  
                         title                 emp_title        emp_length  
     Debt consolidation     :2611                   : 379   10+ years:1685  
     Credit card refinancing: 976   Manager         :  89   2 years  : 412  
     Other                  : 310   Teacher         :  82   3 years  : 406  
     Home improvement       : 299   Owner           :  49   < 1 year : 405  
     Major purchase         : 103   Sales           :  39   1 year   : 339  
     Medical expenses       :  49   Registered Nurse:  37   n/a      : 338  
     (Other)                : 652   (Other)         :4325   (Other)  :1415  
      home_ownership   annual_inc           verification_status grade   
     ANY     :   2   Min.   :      0   Not Verified   :1573     A: 999  
     MORTGAGE:2462   1st Qu.:  46000   Source Verified:1992     B:1430  
     OWN     : 574   Median :  65424   Verified       :1435     C:1418  
     RENT    :1962   Mean   :  78160                            D: 744  
                     3rd Qu.:  95000                            E: 297  
                     Max.   :1500000                            F:  81  
                                                                G:  31  
       sub_grade    earliest_cr_line    open_acc      open_acc_6m    
     B5     : 324   Nov-2002:  38    Min.   : 1.00   Min.   :0.0000  
     B4     : 321   Mar-2002:  37    1st Qu.: 8.00   1st Qu.:0.0000  
     C1     : 319   May-2004:  37    Median :11.00   Median :1.0000  
     C2     : 302   Aug-2005:  36    Mean   :11.62   Mean   :0.9213  
     C3     : 300   Jul-2001:  35    3rd Qu.:15.00   3rd Qu.:1.0000  
     B3     : 276   Sep-2005:  35    Max.   :52.00   Max.   :9.0000  
     (Other):3158   (Other) :4782                    NA's   :1924    
       total_acc      delinq_2yrs        pub_rec          revol_bal     
     Min.   : 2.00   Min.   : 0.000   Min.   : 0.0000   Min.   :     0  
     1st Qu.:15.00   1st Qu.: 0.000   1st Qu.: 0.0000   1st Qu.:  6102  
     Median :22.00   Median : 0.000   Median : 0.0000   Median : 11400  
     Mean   :24.03   Mean   : 0.321   Mean   : 0.1864   Mean   : 16455  
     3rd Qu.:31.00   3rd Qu.: 0.000   3rd Qu.: 0.0000   3rd Qu.: 20323  
     Max.   :85.00   Max.   :14.000   Max.   :11.0000   Max.   :513183  
                                                                        
       revol_util     out_prncp_inv   total_rev_hi_lim  open_act_il   
     Min.   :  0.00   Min.   :    0   Min.   :     0   Min.   : 0.00  
     1st Qu.: 30.82   1st Qu.:    0   1st Qu.: 15000   1st Qu.: 1.00  
     Median : 50.00   Median :    0   Median : 26100   Median : 2.00  
     Mean   : 50.24   Mean   : 4489   Mean   : 35133   Mean   : 2.87  
     3rd Qu.: 69.80   3rd Qu.: 6587   3rd Qu.: 44900   3rd Qu.: 3.00  
     Max.   :152.70   Max.   :39508   Max.   :675000   Max.   :32.00  
     NA's   :2                        NA's   :159      NA's   :1924   
      open_il_12m      open_il_24m     tot_hi_cred_lim    total_bal_il   
     Min.   :0.0000   Min.   : 0.000   Min.   :   1000   Min.   :     0  
     1st Qu.:0.0000   1st Qu.: 0.000   1st Qu.:  50699   1st Qu.:  9011  
     Median :0.0000   Median : 1.000   Median : 115977   Median : 23301  
     Mean   :0.6629   Mean   : 1.557   Mean   : 180173   Mean   : 35358  
     3rd Qu.:1.0000   3rd Qu.: 2.000   3rd Qu.: 255967   3rd Qu.: 46090  
     Max.   :6.0000   Max.   :10.000   Max.   :4475891   Max.   :520362  
     NA's   :1924     NA's   :1924     NA's   :159       NA's   :1924    
     total_bal_ex_mort    il_util          all_util     pct_tl_nvr_dlq  
     Min.   :     0    Min.   :  0.00   Min.   :  0.0   Min.   : 23.10  
     1st Qu.: 20780    1st Qu.: 55.00   1st Qu.: 42.0   1st Qu.: 92.00  
     Median : 37590    Median : 71.00   Median : 57.0   Median :100.00  
     Mean   : 50979    Mean   : 69.06   Mean   : 56.4   Mean   : 94.31  
     3rd Qu.: 65455    3rd Qu.: 86.00   3rd Qu.: 71.0   3rd Qu.:100.00  
     Max.   :609526    Max.   :178.00   Max.   :175.0   Max.   :100.00  
     NA's   :123       NA's   :2357     NA's   :1924    NA's   :160     
     percent_bc_gt_75 pub_rec_bankruptcies    int_rate        zip_code   
     Min.   :  0.00   Min.   :0.0000       Min.   : 5.31   945xx  :  57  
     1st Qu.:  0.00   1st Qu.:0.0000       1st Qu.: 9.49   750xx  :  56  
     Median : 33.30   Median :0.0000       Median :12.62   606xx  :  50  
     Mean   : 42.12   Mean   :0.1226       Mean   :13.05   112xx  :  49  
     3rd Qu.: 75.00   3rd Qu.:0.0000       3rd Qu.:15.99   300xx  :  46  
     Max.   :100.00   Max.   :4.0000       Max.   :30.94   900xx  :  43  
     NA's   :182                                           (Other):4699  
       addr_state   acc_now_delinq  acc_open_past_24mths  tot_cur_bal     
     CA     : 695   Min.   :0.000   Min.   : 0.00        Min.   :      0  
     TX     : 427   1st Qu.:0.000   1st Qu.: 2.00        1st Qu.:  28847  
     NY     : 406   Median :0.000   Median : 4.00        Median :  81033  
     FL     : 349   Mean   :0.003   Mean   : 4.46        Mean   : 143636  
     OH     : 195   3rd Qu.:0.000   3rd Qu.: 6.00        3rd Qu.: 210626  
     IL     : 191   Max.   :2.000   Max.   :22.00        Max.   :3709587  
     (Other):2737                   NA's   :123          NA's   :159      
     num_rev_accts   num_accts_ever_120_pd    mort_acc       total_cu_tl    
     Min.   : 2.00   Min.   : 0.0000       Min.   : 0.000   Min.   : 0.000  
     1st Qu.: 8.00   1st Qu.: 0.0000       1st Qu.: 0.000   1st Qu.: 0.000  
     Median :12.00   Median : 0.0000       Median : 1.000   Median : 0.000  
     Mean   :13.91   Mean   : 0.4863       Mean   : 1.543   Mean   : 1.466  
     3rd Qu.:18.00   3rd Qu.: 0.0000       3rd Qu.: 3.000   3rd Qu.: 2.000  
     Max.   :64.00   Max.   :18.0000       Max.   :14.000   Max.   :41.000  
     NA's   :159     NA's   :159           NA's   :123      NA's   :1924    
      open_rv_12m      open_rv_24m       max_bal_bc     num_actv_bc_tl  
     Min.   : 0.000   Min.   : 0.000   Min.   :     0   Min.   : 0.000  
     1st Qu.: 0.000   1st Qu.: 1.000   1st Qu.:  2358   1st Qu.: 2.000  
     Median : 1.000   Median : 2.000   Median :  4574   Median : 3.000  
     Mean   : 1.264   Mean   : 2.721   Mean   :  6060   Mean   : 3.678  
     3rd Qu.: 2.000   3rd Qu.: 4.000   3rd Qu.:  7825   3rd Qu.: 5.000  
     Max.   :13.000   Max.   :18.000   Max.   :301210   Max.   :21.000  
     NA's   :1924     NA's   :1924     NA's   :1924     NA's   :159     
     num_actv_rev_tl   num_bc_sats       num_bc_tl        num_il_tl    
     Min.   : 0.000   Min.   : 0.000   Min.   : 0.000   Min.   : 0.00  
     1st Qu.: 3.000   1st Qu.: 3.000   1st Qu.: 4.000   1st Qu.: 4.00  
     Median : 5.000   Median : 4.000   Median : 7.000   Median : 7.00  
     Mean   : 5.598   Mean   : 4.827   Mean   : 7.733   Mean   : 8.38  
     3rd Qu.: 7.000   3rd Qu.: 6.000   3rd Qu.:10.000   3rd Qu.:11.00  
     Max.   :31.000   Max.   :30.000   Max.   :40.000   Max.   :62.00  
     NA's   :159      NA's   :140      NA's   :159      NA's   :159    
     num_op_rev_tl    num_rev_tl_bal_gt_0    num_sats     num_tl_120dpd_2m
     Min.   : 0.000   Min.   : 0.000      Min.   : 1.00   Min.   :0e+00   
     1st Qu.: 5.000   1st Qu.: 3.000      1st Qu.: 8.00   1st Qu.:0e+00   
     Median : 7.000   Median : 5.000      Median :11.00   Median :0e+00   
     Mean   : 8.213   Mean   : 5.535      Mean   :11.64   Mean   :2e-04   
     3rd Qu.:10.000   3rd Qu.: 7.000      3rd Qu.:15.00   3rd Qu.:0e+00   
     Max.   :44.000   Max.   :30.000      Max.   :52.00   Max.   :1e+00   
     NA's   :159      NA's   :159         NA's   :140     NA's   :360     
      num_tl_30dpd     num_tl_90g_dpd_24m num_tl_op_past_12m   tax_liens      
     Min.   :0.00000   Min.   : 0.00000   Min.   : 0.000     Min.   : 0.0000  
     1st Qu.:0.00000   1st Qu.: 0.00000   1st Qu.: 1.000     1st Qu.: 0.0000  
     Median :0.00000   Median : 0.00000   Median : 2.000     Median : 0.0000  
     Mean   :0.00186   Mean   : 0.09296   Mean   : 2.053     Mean   : 0.0424  
     3rd Qu.:0.00000   3rd Qu.: 0.00000   3rd Qu.: 3.000     3rd Qu.: 0.0000  
     Max.   :1.00000   Max.   :13.00000   Max.   :16.000     Max.   :10.0000  
     NA's   :159       NA's   :159        NA's   :159                         
     total_bc_limit   total_il_high_credit_limit   last_pymnt_d    next_pymnt_d 
     Min.   :     0   Min.   :     0             Feb-2019:2057           :2898  
     1st Qu.:  8500   1st Qu.: 15140             Jan-2019: 105   Mar-2019:2084  
     Median : 17000   Median : 32740             Aug-2018:  95   Sep-2010:   6  
     Mean   : 24030   Mean   : 43907             Mar-2018:  84   Oct-2012:   4  
     3rd Qu.: 31900   3rd Qu.: 59442             Sep-2018:  84   Feb-2013:   3  
     Max.   :354000   Max.   :640726             Nov-2017:  83   Apr-2019:   1  
     NA's   :123      NA's   :159                (Other) :2492   (Other) :   4  
     last_pymnt_amnt   pymnt_plan  total_pymnt    total_pymnt_inv inq_last_6mths  
     Min.   :    0.0   n:4999     Min.   :    0   Min.   :    0   Min.   : 0.000  
     1st Qu.:  305.2   y:   1     1st Qu.: 4141   1st Qu.: 4124   1st Qu.: 0.000  
     Median :  598.0              Median : 8935   Median : 8931   Median : 0.000  
     Mean   : 3319.6              Mean   :11905   Mean   :11894   Mean   : 0.573  
     3rd Qu.: 3252.4              3rd Qu.:16920   3rd Qu.:16883   3rd Qu.: 1.000  
     Max.   :40752.6              Max.   :59809   Max.   :59724   Max.   :10.000  
                                                                                  
      inq_last_12m        inq_fi          application_type
     Min.   : 0.000   Min.   : 0.0000   Individual:4748   
     1st Qu.: 0.000   1st Qu.: 0.0000   Joint App : 252   
     Median : 1.000   Median : 0.0000                     
     Mean   : 2.003   Mean   : 0.9893                     
     3rd Qu.: 3.000   3rd Qu.: 1.0000                     
     Max.   :67.000   Max.   :28.0000                     
     NA's   :1924     NA's   :1924                        
     collections_12_mths_ex_med collection_recovery_fee bc_open_to_buy  
     Min.   :0.0000             Min.   :   0.00         Min.   :     0  
     1st Qu.:0.0000             1st Qu.:   0.00         1st Qu.:  1805  
     Median :0.0000             Median :   0.00         Median :  5736  
     Mean   :0.0148             Mean   :  23.11         Mean   : 12108  
     3rd Qu.:0.0000             3rd Qu.:   0.00         3rd Qu.: 15513  
     Max.   :2.0000             Max.   :3492.00         Max.   :248878  
                                                        NA's   :182     
        bc_util       sec_app_earliest_cr_line sec_app_inq_last_6mths
     Min.   :  0.00           :4772            Min.   :0.000         
     1st Qu.: 33.75   Jan-2006:   4            1st Qu.:0.000         
     Median : 59.20   Jul-2005:   4            Median :0.000         
     Mean   : 57.28   Sep-2005:   4            Mean   :0.601         
     3rd Qu.: 82.80   Apr-2001:   3            3rd Qu.:1.000         
     Max.   :157.90   Aug-2004:   3            Max.   :5.000         
     NA's   :185      (Other) : 210            NA's   :4772          
     sec_app_mort_acc sec_app_open_acc sec_app_revol_util sec_app_open_act_il
     Min.   :0.000    Min.   : 2.00    Min.   :  0.00     Min.   : 0.000     
     1st Qu.:0.000    1st Qu.: 7.00    1st Qu.: 42.60     1st Qu.: 1.000     
     Median :1.000    Median :11.00    Median : 64.50     Median : 2.000     
     Mean   :1.368    Mean   :11.56    Mean   : 60.34     Mean   : 3.123     
     3rd Qu.:2.000    3rd Qu.:16.00    3rd Qu.: 80.40     3rd Qu.: 4.000     
     Max.   :8.000    Max.   :36.00    Max.   :117.10     Max.   :17.000     
     NA's   :4772     NA's   :4772     NA's   :4775       NA's   :4772       
     sec_app_chargeoff_within_12_mths sec_app_collections_12_mths_ex_med
     Min.   :0.000                    Min.   :0.000                     
     1st Qu.:0.000                    1st Qu.:0.000                     
     Median :0.000                    Median :0.000                     
     Mean   :0.044                    Mean   :0.083                     
     3rd Qu.:0.000                    3rd Qu.:0.000                     
     Max.   :3.000                    Max.   :2.000                     
     NA's   :4772                     NA's   :4772                      


# Normalization

<h2>3NF


```R
#terms_info table:
df$term_id <- 1:nrow(df)
head(df[,c('term_id','term')])
#primary key(term_id)
```


<table>
<thead><tr><th scope=col>term_id</th><th scope=col>term</th></tr></thead>
<tbody>
	<tr><td>1         </td><td> 36 months</td></tr>
	<tr><td>2         </td><td> 60 months</td></tr>
	<tr><td>3         </td><td> 60 months</td></tr>
	<tr><td>4         </td><td> 36 months</td></tr>
	<tr><td>5         </td><td> 36 months</td></tr>
	<tr><td>6         </td><td> 60 months</td></tr>
</tbody>
</table>




```R
#statuses_info table:
df$status_id <- 1:nrow(df)
head(df[,c('status_id','loan_status')])
#primary key(status_id)
```


<table>
<thead><tr><th scope=col>status_id</th><th scope=col>loan_status</th></tr></thead>
<tbody>
	<tr><td>1          </td><td>Charged Off</td></tr>
	<tr><td>2          </td><td>Charged Off</td></tr>
	<tr><td>3          </td><td>Charged Off</td></tr>
	<tr><td>4          </td><td>Charged Off</td></tr>
	<tr><td>5          </td><td>Charged Off</td></tr>
	<tr><td>6          </td><td>Charged Off</td></tr>
</tbody>
</table>




```R
#loans_purpose table:
head(df[,c('purpose','title')])
#primary key(purpose)
```


<table>
<thead><tr><th scope=col>purpose</th><th scope=col>title</th></tr></thead>
<tbody>
	<tr><td>small_business           </td><td>Working Capital to Grow  </td></tr>
	<tr><td>small_business           </td><td>Concession Stand         </td></tr>
	<tr><td>small_business           </td><td>Business Development Loan</td></tr>
	<tr><td>small_business           </td><td>Charming Business Loan   </td></tr>
	<tr><td>small_business           </td><td>Immediate Loan Relief    </td></tr>
	<tr><td>other                    </td><td>Other Loan               </td></tr>
</tbody>
</table>




```R
#collections table:
df$collection_id <- 1:nrow(df)
head(df[,c('collection_id','collection_recovery_fee','collections_12_mths_ex_med')])
#primary key(collection_id)
```


<table>
<thead><tr><th scope=col>collection_id</th><th scope=col>collection_recovery_fee</th><th scope=col>collections_12_mths_ex_med</th></tr></thead>
<tbody>
	<tr><td>1       </td><td>  2.7900</td><td>0       </td></tr>
	<tr><td>2       </td><td>  6.1500</td><td>0       </td></tr>
	<tr><td>3       </td><td>242.6178</td><td>0       </td></tr>
	<tr><td>4       </td><td>  0.0000</td><td>0       </td></tr>
	<tr><td>5       </td><td>  0.0000</td><td>0       </td></tr>
	<tr><td>6       </td><td>  5.7800</td><td>0       </td></tr>
</tbody>
</table>




```R
#applications table:
df$application_type_id <- 1:nrow(df)
head(df[,c('application_type_id','application_type')])
#primary key(application_type_id)
```


<table>
<thead><tr><th scope=col>application_type_id</th><th scope=col>application_type</th></tr></thead>
<tbody>
	<tr><td>1         </td><td>Individual</td></tr>
	<tr><td>2         </td><td>Individual</td></tr>
	<tr><td>3         </td><td>Individual</td></tr>
	<tr><td>4         </td><td>Individual</td></tr>
	<tr><td>5         </td><td>Individual</td></tr>
	<tr><td>6         </td><td>Individual</td></tr>
</tbody>
</table>




```R
#payments_info table:
df$pymnts_info_id <- 1:nrow(df)
head(df[,c('pymnts_info_id','pymnt_plan','total_pymnt','total_pymnt_inv','last_pymnt_amnt')])
#primary key(pymnts_info_id)
```


<table>
<thead><tr><th scope=col>pymnts_info_id</th><th scope=col>pymnt_plan</th><th scope=col>total_pymnt</th><th scope=col>total_pymnt_inv</th><th scope=col>last_pymnt_amnt</th></tr></thead>
<tbody>
	<tr><td>1      </td><td>n      </td><td>6518.49</td><td>3336.76</td><td>709.96 </td></tr>
	<tr><td>2      </td><td>n      </td><td>2416.59</td><td>2384.31</td><td>356.15 </td></tr>
	<tr><td>3      </td><td>n      </td><td>6589.13</td><td>3413.42</td><td>658.74 </td></tr>
	<tr><td>4      </td><td>n      </td><td>1744.16</td><td>1744.16</td><td>218.02 </td></tr>
	<tr><td>5      </td><td>n      </td><td> 305.16</td><td> 305.16</td><td> 87.54 </td></tr>
	<tr><td>6      </td><td>n      </td><td>1814.67</td><td>1806.17</td><td>423.82 </td></tr>
</tbody>
</table>




```R
#pymnts_schedule table:
head(df[,c('last_pymnt_d','next_pymnt_d','pymnts_info_id')])
#PRIMARY KEY (last_pymnt_d),
#FOREIGN KEY(pymnts_info_id) REFERENCES payments_info
```


<table>
<thead><tr><th scope=col>last_pymnt_d</th><th scope=col>next_pymnt_d</th><th scope=col>pymnts_info_id</th></tr></thead>
<tbody>
	<tr><td>Aug-2010</td><td>Sep-2010</td><td>1       </td></tr>
	<tr><td>Sep-2011</td><td>        </td><td>2       </td></tr>
	<tr><td>Nov-2011</td><td>        </td><td>3       </td></tr>
	<tr><td>Jan-2012</td><td>        </td><td>4       </td></tr>
	<tr><td>Jan-2012</td><td>        </td><td>5       </td></tr>
	<tr><td>Jun-2012</td><td>        </td><td>6       </td></tr>
</tbody>
</table>




```R
#secondary_applicants table:
df$sec_app_id <- 1:nrow(df)
head(df[,c('sec_app_id','sec_app_earliest_cr_line','sec_app_inq_last_6mths','sec_app_mort_acc','sec_app_mort_acc','sec_app_revol_util',
'sec_app_open_act_il' ,'sec_app_chargeoff_within_12_mths','sec_app_collections_12_mths_ex_med')])
#primary key(sec_app_id)
```


<table>
<thead><tr><th scope=col>sec_app_id</th><th scope=col>sec_app_earliest_cr_line</th><th scope=col>sec_app_inq_last_6mths</th><th scope=col>sec_app_mort_acc</th><th scope=col>sec_app_mort_acc.1</th><th scope=col>sec_app_revol_util</th><th scope=col>sec_app_open_act_il</th><th scope=col>sec_app_chargeoff_within_12_mths</th><th scope=col>sec_app_collections_12_mths_ex_med</th></tr></thead>
<tbody>
	<tr><td>1 </td><td>  </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>2 </td><td>  </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>3 </td><td>  </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>4 </td><td>  </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>5 </td><td>  </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>6 </td><td>  </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
</tbody>
</table>




```R
#interest_rates table:
head(df[,c('sub_grade','int_rate')])
#primary key(sub_grade)
```


<table>
<thead><tr><th scope=col>sub_grade</th><th scope=col>int_rate</th></tr></thead>
<tbody>
	<tr><td>E1   </td><td>14.93</td></tr>
	<tr><td>D2   </td><td>14.91</td></tr>
	<tr><td>F5   </td><td>19.74</td></tr>
	<tr><td>C1   </td><td>12.68</td></tr>
	<tr><td>B4   </td><td>11.49</td></tr>
	<tr><td>E1   </td><td>19.99</td></tr>
</tbody>
</table>




```R
#addresses table:
df$address_id<- 1:nrow(df)
head(df[,c('address_id','address_id','addr_state')])
#primary key(address_id)
```


<table>
<thead><tr><th scope=col>address_id</th><th scope=col>address_id.1</th><th scope=col>addr_state</th></tr></thead>
<tbody>
	<tr><td>1 </td><td>1 </td><td>NJ</td></tr>
	<tr><td>2 </td><td>2 </td><td>LA</td></tr>
	<tr><td>3 </td><td>3 </td><td>WA</td></tr>
	<tr><td>4 </td><td>4 </td><td>CA</td></tr>
	<tr><td>5 </td><td>5 </td><td>AZ</td></tr>
	<tr><td>6 </td><td>6 </td><td>FL</td></tr>
</tbody>
</table>




```R
#inquires table:
df$inq_status_id<- 1:nrow(df)
head(df[,c('inq_status_id','inq_last_6mths','inq_last_12m','inq_fi')])
#primary key(inq_status_id)
```


<table>
<thead><tr><th scope=col>inq_status_id</th><th scope=col>inq_last_6mths</th><th scope=col>inq_last_12m</th><th scope=col>inq_fi</th></tr></thead>
<tbody>
	<tr><td>1 </td><td>0 </td><td>NA</td><td>NA</td></tr>
	<tr><td>2 </td><td>3 </td><td>NA</td><td>NA</td></tr>
	<tr><td>3 </td><td>1 </td><td>NA</td><td>NA</td></tr>
	<tr><td>4 </td><td>0 </td><td>NA</td><td>NA</td></tr>
	<tr><td>5 </td><td>0 </td><td>NA</td><td>NA</td></tr>
	<tr><td>6 </td><td>3 </td><td>NA</td><td>NA</td></tr>
</tbody>
</table>




```R
#customer_credit_histories table:
df$credit_id<- 1:nrow(df)
head(df[,c('credit_id', 'earliest_cr_line', 'open_acc', 'open_acc_6m', 'total_acc', 'delinq_2yrs', 'pub_rec', 
           'revol_bal', 'revol_util', 'out_prncp_inv', 'total_rev_hi_lim', 'open_act_il', 'open_il_12m', 
           'open_il_24m', 'tot_hi_cred_lim', 'total_bal_ex_mort', 'total_bal_il', 'il_util', 'all_util', 
           'pct_tl_nvr_dlq', 'percent_bc_gt_75', 'pub_rec_bankruptcies', 'inq_status_id')])
#PRIMARY KEY(credit_id),
#FOREIGN KEY(inq_status_id) REFERENCES inquires
```


<table>
<thead><tr><th scope=col>credit_id</th><th scope=col>earliest_cr_line</th><th scope=col>open_acc</th><th scope=col>open_acc_6m</th><th scope=col>total_acc</th><th scope=col>delinq_2yrs</th><th scope=col>pub_rec</th><th scope=col>revol_bal</th><th scope=col>revol_util</th><th scope=col>out_prncp_inv</th><th scope=col>⋯</th><th scope=col>open_il_24m</th><th scope=col>tot_hi_cred_lim</th><th scope=col>total_bal_ex_mort</th><th scope=col>total_bal_il</th><th scope=col>il_util</th><th scope=col>all_util</th><th scope=col>pct_tl_nvr_dlq</th><th scope=col>percent_bc_gt_75</th><th scope=col>pub_rec_bankruptcies</th><th scope=col>inq_status_id</th></tr></thead>
<tbody>
	<tr><td>1       </td><td>Jun-1990</td><td>18      </td><td>NA      </td><td>35      </td><td>0       </td><td>0       </td><td>56189   </td><td>89.2    </td><td>0       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>0       </td><td>1       </td></tr>
	<tr><td>2       </td><td>Nov-1983</td><td> 9      </td><td>NA      </td><td>18      </td><td>0       </td><td>1       </td><td> 3600   </td><td>18.7    </td><td>0       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>1       </td><td>2       </td></tr>
	<tr><td>3       </td><td>Feb-1991</td><td> 4      </td><td>NA      </td><td>11      </td><td>0       </td><td>0       </td><td> 5409   </td><td>93.3    </td><td>0       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>0       </td><td>3       </td></tr>
	<tr><td>4       </td><td>Jan-1999</td><td> 9      </td><td>NA      </td><td>49      </td><td>2       </td><td>0       </td><td>35294   </td><td>95.9    </td><td>0       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>0       </td><td>4       </td></tr>
	<tr><td>5       </td><td>Mar-1999</td><td> 2      </td><td>NA      </td><td> 4      </td><td>0       </td><td>0       </td><td> 1088   </td><td>83.7    </td><td>0       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>0       </td><td>5       </td></tr>
	<tr><td>6       </td><td>Sep-1996</td><td> 7      </td><td>NA      </td><td>26      </td><td>0       </td><td>0       </td><td>22122   </td><td>93.7    </td><td>0       </td><td>⋯       </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>NA      </td><td>0       </td><td>6       </td></tr>
</tbody>
</table>




```R
#accounts_info table:
df$account_info_id<- 1:nrow(df)
head(df[,c('account_info_id', 'acc_now_delinq', 'acc_open_past_24mths', 'tot_cur_bal','num_rev_accts',
           'num_accts_ever_120_pd','mort_acc','total_cu_tl','open_rv_12m','open_rv_24m','max_bal_bc',
           'num_actv_rev_tl','num_il_tl','num_op_rev_tl','num_rev_tl_bal_gt_0','num_sats','num_tl_120dpd_2m',
           'num_tl_30dpd','num_tl_90g_dpd_24m','num_tl_op_past_12m','total_il_high_credit_limit',
           'total_bc_limit','tax_liens')])
#PRIMARY KEY (account_info_id)
```


<table>
<thead><tr><th scope=col>account_info_id</th><th scope=col>acc_now_delinq</th><th scope=col>acc_open_past_24mths</th><th scope=col>tot_cur_bal</th><th scope=col>num_rev_accts</th><th scope=col>num_accts_ever_120_pd</th><th scope=col>mort_acc</th><th scope=col>total_cu_tl</th><th scope=col>open_rv_12m</th><th scope=col>open_rv_24m</th><th scope=col>⋯</th><th scope=col>num_op_rev_tl</th><th scope=col>num_rev_tl_bal_gt_0</th><th scope=col>num_sats</th><th scope=col>num_tl_120dpd_2m</th><th scope=col>num_tl_30dpd</th><th scope=col>num_tl_90g_dpd_24m</th><th scope=col>num_tl_op_past_12m</th><th scope=col>total_il_high_credit_limit</th><th scope=col>total_bc_limit</th><th scope=col>tax_liens</th></tr></thead>
<tbody>
	<tr><td>1 </td><td>0 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>⋯ </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>0 </td></tr>
	<tr><td>2 </td><td>0 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>⋯ </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>0 </td></tr>
	<tr><td>3 </td><td>0 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>⋯ </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>0 </td></tr>
	<tr><td>4 </td><td>0 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>⋯ </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>0 </td></tr>
	<tr><td>5 </td><td>0 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>⋯ </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>0 </td></tr>
	<tr><td>6 </td><td>0 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>⋯ </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>0 </td></tr>
</tbody>
</table>




```R
#customers table:
df$bankcard_id <- 1:nrow(df)
df$member_id <- 1:nrow(df)
df$loan_id <- 1:nrow(df)
head(df[,c('member_id', 'emp_title', 'emp_length', 'home_ownership', 'annual_inc', 'verification_status', 
           'debt_settlement_flag', 'grade', 'sub_grade', 'address_id', 'loan_id', 'bankcard_id', 
           'account_info_id', 'credit_id')])
#PRIMARY KEY (member_id),
#FOREIGN KEY (sub_grade) REFERENCES interest_rates,
#FOREIGN KEY (address_id) REFERENCES addresses,
#FOREIGN KEY (account_info_id) REFERENCES accounts_info,
#FOREIGN KEY (credit_id) REFERENCES customer_credit_histories
```


<table>
<thead><tr><th scope=col>member_id</th><th scope=col>emp_title</th><th scope=col>emp_length</th><th scope=col>home_ownership</th><th scope=col>annual_inc</th><th scope=col>verification_status</th><th scope=col>debt_settlement_flag</th><th scope=col>grade</th><th scope=col>sub_grade</th><th scope=col>address_id</th><th scope=col>loan_id</th><th scope=col>bankcard_id</th><th scope=col>account_info_id</th><th scope=col>credit_id</th></tr></thead>
<tbody>
	<tr><td>1                 </td><td>Self-employed     </td><td>1 year            </td><td>MORTGAGE          </td><td>60000             </td><td>Not Verified      </td><td>N                 </td><td>E                 </td><td>E1                </td><td>1                 </td><td>1                 </td><td>1                 </td><td>1                 </td><td>1                 </td></tr>
	<tr><td>2                 </td><td>LSU Law Center    </td><td>2 years           </td><td>MORTGAGE          </td><td>40000             </td><td>Verified          </td><td>N                 </td><td>D                 </td><td>D2                </td><td>2                 </td><td>2                 </td><td>2                 </td><td>2                 </td><td>2                 </td></tr>
	<tr><td>3                 </td><td>MarketStrike      </td><td>10+ years         </td><td>MORTGAGE          </td><td>78000             </td><td>Verified          </td><td>N                 </td><td>F                 </td><td>F5                </td><td>3                 </td><td>3                 </td><td>3                 </td><td>3                 </td><td>3                 </td></tr>
	<tr><td>4                 </td><td>Heald College     </td><td>9 years           </td><td>MORTGAGE          </td><td>55200             </td><td>Not Verified      </td><td>N                 </td><td>C                 </td><td>C1                </td><td>4                 </td><td>4                 </td><td>4                 </td><td>4                 </td><td>4                 </td></tr>
	<tr><td>5                 </td><td>                  </td><td>6 years           </td><td>MORTGAGE          </td><td>18000             </td><td>Not Verified      </td><td>N                 </td><td>B                 </td><td>B4                </td><td>5                 </td><td>5                 </td><td>5                 </td><td>5                 </td><td>5                 </td></tr>
	<tr><td>6                 </td><td>Healthcare America</td><td>1 year            </td><td>RENT              </td><td>65000             </td><td>Verified          </td><td>N                 </td><td>E                 </td><td>E1                </td><td>6                 </td><td>6                 </td><td>6                 </td><td>6                 </td><td>6                 </td></tr>
</tbody>
</table>




```R
#bankcards table:
head(df[,c('bankcard_id', 'member_id', 'bc_open_to_buy', 'bc_util', 'num_actv_bc_tl', 'num_bc_sats', 
           'num_bc_tl')])
#PRIMARY KEY (bankcard_id),
#FOREIGN KEY(member_id) REFERENCES customers
```


<table>
<thead><tr><th scope=col>bankcard_id</th><th scope=col>member_id</th><th scope=col>bc_open_to_buy</th><th scope=col>bc_util</th><th scope=col>num_actv_bc_tl</th><th scope=col>num_bc_sats</th><th scope=col>num_bc_tl</th></tr></thead>
<tbody>
	<tr><td>1 </td><td>1 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>2 </td><td>2 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>3 </td><td>3 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>4 </td><td>4 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>5 </td><td>5 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
	<tr><td>6 </td><td>6 </td><td>NA</td><td>NA</td><td>NA</td><td>NA</td><td>NA</td></tr>
</tbody>
</table>




```R
#loans_info table:
head(df[,c('loan_id', 'member_id', 'loan_amnt', 'funded_amnt', 'installment', 'issue_d', 'dti', 
           'total_rec_prncp', 'total_rec_int', 'total_rec_late_fee', 'initial_list_status', 
           'out_prncp', 'last_credit_pull_d', 'chargeoff_within_12_mths', 'disbursement_method', 
           'term_id', 'status_id', 'purpose', 'application_type_id', 'sec_app_id', 'pymnts_info_id', 
           'collection_id')])
#PRIMARY KEY (loan_id),
#FOREIGN KEY (term_id) REFERENCES terms_info,
#FOREIGN KEY(member_id) REFERENCES customers,
#FOREIGN KEY (status_id) REFERENCES statuses_info,
#FOREIGN KEY (purpose) REFERENCES loans_purpose,
#FOREIGN KEY (application_type_id) REFERENCES applications,
#FOREIGN KEY (sec_app_id) REFERENCES secondary_applicants,
#FOREIGN KEY (pymnts_info_id) REFERENCES payments_info,
#FOREIGN KEY (collection_id) REFERENCES collections
```


<table>
<thead><tr><th scope=col>loan_id</th><th scope=col>member_id</th><th scope=col>loan_amnt</th><th scope=col>funded_amnt</th><th scope=col>installment</th><th scope=col>issue_d</th><th scope=col>dti</th><th scope=col>total_rec_prncp</th><th scope=col>total_rec_int</th><th scope=col>total_rec_late_fee</th><th scope=col>⋯</th><th scope=col>last_credit_pull_d</th><th scope=col>chargeoff_within_12_mths</th><th scope=col>disbursement_method</th><th scope=col>term_id</th><th scope=col>status_id</th><th scope=col>purpose</th><th scope=col>application_type_id</th><th scope=col>sec_app_id</th><th scope=col>pymnts_info_id</th><th scope=col>collection_id</th></tr></thead>
<tbody>
	<tr><td>1             </td><td>1             </td><td>10000         </td><td>10000         </td><td>346.32        </td><td>Nov-2008      </td><td>20.58         </td><td>4436.30       </td><td>1788.80       </td><td>34.60038      </td><td>⋯             </td><td>Oct-2016      </td><td>0             </td><td>Cash          </td><td>1             </td><td>1             </td><td>small_business</td><td>1             </td><td>1             </td><td>1             </td><td>1             </td></tr>
	<tr><td>2             </td><td>2             </td><td>15000         </td><td>15000         </td><td>356.15        </td><td>Apr-2011      </td><td> 9.45         </td><td> 867.37       </td><td> 907.53       </td><td> 0.00000      </td><td>⋯             </td><td>Oct-2016      </td><td>0             </td><td>Cash          </td><td>2             </td><td>2             </td><td>small_business</td><td>2             </td><td>2             </td><td>2             </td><td>2             </td></tr>
	<tr><td>3             </td><td>3             </td><td>25000         </td><td>25000         </td><td>658.74        </td><td>Mar-2011      </td><td> 1.83         </td><td>2092.68       </td><td>3164.52       </td><td> 0.00000      </td><td>⋯             </td><td>Oct-2016      </td><td>0             </td><td>Cash          </td><td>3             </td><td>3             </td><td>small_business</td><td>3             </td><td>3             </td><td>3             </td><td>3             </td></tr>
	<tr><td>4             </td><td>4             </td><td> 6500         </td><td> 6500         </td><td>218.02        </td><td>Apr-2011      </td><td>20.39         </td><td>1235.05       </td><td> 502.39       </td><td> 0.00000      </td><td>⋯             </td><td>Feb-2019      </td><td>0             </td><td>Cash          </td><td>4             </td><td>4             </td><td>small_business</td><td>4             </td><td>4             </td><td>4             </td><td>4             </td></tr>
	<tr><td>5             </td><td>5             </td><td> 2200         </td><td> 2200         </td><td> 72.54        </td><td>Sep-2011      </td><td> 3.20         </td><td> 207.83       </td><td>  81.07       </td><td>14.93383      </td><td>⋯             </td><td>Feb-2017      </td><td>0             </td><td>Cash          </td><td>5             </td><td>5             </td><td>small_business</td><td>5             </td><td>5             </td><td>5             </td><td>5             </td></tr>
	<tr><td>6             </td><td>6             </td><td>16000         </td><td>16000         </td><td>423.82        </td><td>Feb-2012      </td><td>18.90         </td><td> 478.96       </td><td> 790.37       </td><td> 0.00000      </td><td>⋯             </td><td>Oct-2016      </td><td>0             </td><td>Cash          </td><td>6             </td><td>6             </td><td>other         </td><td>6             </td><td>6             </td><td>6             </td><td>6             </td></tr>
</tbody>
</table>



# Create database tables

Load the PostgreSQL driver


```R
drv <- dbDriver('PostgreSQL')

con <- dbConnect(drv, dbname = 'loan',
                host = 'localhost', port = 5432,
                user = 'postgres', password = 'pwd4APAN5310')

con <- dbConnect(drv, dbname = 'loan',
                 host = 's19db.apan5310.com', port = 50103,
                 user = 'postgres', password = 'gjw42liv')

```

Pass the SQL statements that create all tables


```R
stmt <- "
CREATE TABLE terms_info (
    term_id int,
    term varchar(10),
    PRIMARY KEY (term_id)
);

CREATE TABLE statuses_info (
status_id int,
    loan_status varchar(100),
    PRIMARY KEY (status_id)
);

CREATE TABLE loans_purpose(
    purpose varchar(100),
    title varchar(100),
    PRIMARY KEY (purpose)
);

CREATE TABLE collections (
    collection_id int,
    collection_recovery_fee int,
    collections_12_mths_ex_med int,
    PRIMARY KEY (collection_id)
);

CREATE TABLE applications(
    application_type_id int,
    application_type varchar(20),
    PRIMARY KEY (application_type_id)
);

CREATE TABLE payments_info(
    pymnts_info_id int,
    pymnt_plan varchar(10),
    total_pymnt numeric(10,2),
    total_pymnt_inv numeric(10,2),
    last_pymnt_amnt numeric(10,2),
    PRIMARY KEY (pymnts_info_id)
);

CREATE TABLE pymnts_schedule(
    last_pymnt_d varchar(20),
    next_pymnt_d varchar(20),
    pymnts_info_id int,
    PRIMARY KEY (last_pymnt_d),
    FOREIGN KEY(pymnts_info_id) REFERENCES payments_info
);



CREATE TABLE secondary_applicants (
    sec_app_id int,
    sec_app_earliest_cr_line varchar(30),
    sec_app_inq_last_6mths int,
    sec_app_mort_acc int,
    sec_app_open_acc int,
    sec_app_revol_util int,
    sec_app_open_act_il int,
    sec_app_chargeoff_within_12_mths int,
    sec_app_collections_12_mths_ex_med int,
    PRIMARY KEY (sec_app_id)
);

CREATE TABLE interest_rates (
    sub_grade varchar(10),
    int_rate float,
    PRIMARY KEY (sub_grade)
);

CREATE TABLE addresses (
    address_id int,
    zip_code varchar(20),
    addr_state varchar(10),
    PRIMARY KEY (address_id)
);

CREATE TABLE inquires (
    inq_status_id int,
    inq_last_6mths int,
    inq_last_12m int,
    inq_fi int,
    PRIMARY KEY (inq_status_id)
);

CREATE TABLE customer_credit_histories(
    credit_id int,
    earliest_cr_line varchar(30),
    open_acc int,
    open_acc_6m int,
    total_acc int,
    delinq_2yrs int ,
    pub_rec int,
    revol_bal int,
    revol_util numeric(10,1),
    out_prncp_inv  numeric(10,2),
    total_rev_hi_lim int,
    open_act_il  int,
    open_il_12m int,
    open_il_24m int,
    tot_hi_cred_lim int,
    total_bal_ex_mort int,
    total_bal_il int,
    il_util int,
    all_util int,
    pct_tl_nvr_dlq numeric(10,1),
    percent_bc_gt_75 numeric(10,1),
    pub_rec_bankruptcies int,
    inq_status_id int,
    PRIMARY KEY(credit_id),
    FOREIGN KEY(inq_status_id) REFERENCES inquires
);

CREATE TABLE accounts_info(
    account_info_id int,
    acc_now_delinq int,
    acc_open_past_24mths int,
    tot_cur_bal int, 
    num_rev_accts int,
    num_accts_ever_120_pd int,
    mort_acc int,
    total_cu_tl int,
    open_rv_12m int,
    open_rv_24m int,
    max_bal_bc int,
    num_actv_rev_tl int,
    num_il_tl int,
    num_op_rev_tl int,
    num_rev_tl_bal_gt_0 int,
    num_sats int,
    num_tl_120dpd_2m int,
    num_tl_30dpd int,
    num_tl_90g_dpd_24m int,
    num_tl_op_past_12m int,
    total_il_high_credit_limit int,
    total_bc_limit int,
    tax_liens int,
    PRIMARY KEY (account_info_id)
);

CREATE TABLE customers(
    member_id int,
    emp_title varchar(100),
    emp_length varchar(100),
    home_ownership  varchar(100),
    annual_inc float,
    verification_status varchar(100),
    debt_settlement_flag varchar(10),
    grade varchar(10),
    sub_grade varchar(10),
    address_id int,
    loan_id int,
    bankcard_id int,
    account_info_id int,
    credit_id int,
    PRIMARY KEY (member_id),
    FOREIGN KEY (sub_grade) REFERENCES interest_rates,
    FOREIGN KEY (address_id) REFERENCES addresses,
    FOREIGN KEY (account_info_id) REFERENCES accounts_info,
    FOREIGN KEY (credit_id) REFERENCES customer_credit_histories
);

CREATE TABLE bankcards (
    bankcard_id int,
    member_id int,
    bc_open_to_buy int,
    bc_util float,
    num_actv_bc_tl int,
    num_bc_sats int,
    num_bc_tl int,
    PRIMARY KEY (bankcard_id),
    FOREIGN KEY(member_id) REFERENCES customers 
);

CREATE TABLE loans_info (
    loan_id int,
    member_id int,
    loan_amnt int,
    funded_amnt int,
    installment float,
    issue_d varchar(100),
    dti float,
    total_rec_prncp float,
    total_rec_int float,
    total_rec_late_fee float,
    initial_list_status varchar(100),
    out_prncp float,
    last_credit_pull_d varchar(100),
    chargeoff_within_12_mths int,
    disbursement_method varchar(100),
    term_id int,
    status_id int,
    purpose varchar(100),
    application_type_id int,
    sec_app_id int,
    pymnts_info_id int,
    collection_id int,
    PRIMARY KEY (loan_id),
    FOREIGN KEY (term_id) REFERENCES terms_info,
    FOREIGN KEY(member_id) REFERENCES customers,
    FOREIGN KEY (status_id) REFERENCES statuses_info,
    FOREIGN KEY (purpose) REFERENCES loans_purpose,
    FOREIGN KEY (application_type_id) REFERENCES applications,
    FOREIGN KEY (sec_app_id) REFERENCES secondary_applicants,
    FOREIGN KEY (pymnts_info_id) REFERENCES payments_info,
    FOREIGN KEY (collection_id) REFERENCES collections
);
"
##Execute the statement to create tables
dbGetQuery(con,stmt)
```

# Extract, Transform and Load Process


```R
head(df)
```


<table>
<thead><tr><th scope=col>member_id</th><th scope=col>loan_amnt</th><th scope=col>funded_amnt</th><th scope=col>installment</th><th scope=col>issue_d</th><th scope=col>dti</th><th scope=col>total_rec_prncp</th><th scope=col>total_rec_int</th><th scope=col>total_rec_late_fee</th><th scope=col>initial_list_status</th><th scope=col>⋯</th><th scope=col>collection_id</th><th scope=col>application_type_id</th><th scope=col>pymnts_info_id</th><th scope=col>sec_app_id</th><th scope=col>address_id</th><th scope=col>inq_status_id</th><th scope=col>credit_id</th><th scope=col>account_info_id</th><th scope=col>bankcard_id</th><th scope=col>loan_id</th></tr></thead>
<tbody>
	<tr><td>1       </td><td>10000   </td><td>10000   </td><td>346.32  </td><td>Nov-2008</td><td>20.58   </td><td>4436.30 </td><td>1788.80 </td><td>34.60038</td><td>f       </td><td>⋯       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td><td>1       </td></tr>
	<tr><td>2       </td><td>15000   </td><td>15000   </td><td>356.15  </td><td>Apr-2011</td><td> 9.45   </td><td> 867.37 </td><td> 907.53 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td><td>2       </td></tr>
	<tr><td>3       </td><td>25000   </td><td>25000   </td><td>658.74  </td><td>Mar-2011</td><td> 1.83   </td><td>2092.68 </td><td>3164.52 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td><td>3       </td></tr>
	<tr><td>4       </td><td> 6500   </td><td> 6500   </td><td>218.02  </td><td>Apr-2011</td><td>20.39   </td><td>1235.05 </td><td> 502.39 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td><td>4       </td></tr>
	<tr><td>5       </td><td> 2200   </td><td> 2200   </td><td> 72.54  </td><td>Sep-2011</td><td> 3.20   </td><td> 207.83 </td><td>  81.07 </td><td>14.93383</td><td>f       </td><td>⋯       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td><td>5       </td></tr>
	<tr><td>6       </td><td>16000   </td><td>16000   </td><td>423.82  </td><td>Feb-2012</td><td>18.90   </td><td> 478.96 </td><td> 790.37 </td><td> 0.00000</td><td>f       </td><td>⋯       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td><td>6       </td></tr>
</tbody>
</table>




```R
df<-read.csv(file='loan_5k_data_new.csv',sep=',',header = T)
names(df)<-tolower(names(df))

df1 <- unique(df[c('term_id', 'term')])
dbWriteTable(con, name = 'terms_info', value = df1, row.names = FALSE, append = TRUE)

df2 <- unique(df[c('status_id', 'loan_status')])
dbWriteTable(con, name = 'statuses_info', value = df2, row.names = FALSE, append = TRUE)

df33 <- unique(df[c('purpose', 'title')])
index3<-duplicated(df33$purpose)
df3<-df33[!index3,]
dbWriteTable(con, name = 'loans_purpose', value = df3, row.names = FALSE, append = TRUE)

df$collection_recovery_fee <- as.integer(df$collection_recovery_fee)
df44 <- unique(df[c('collection_id', 'collection_recovery_fee', 'collections_12_mths_ex_med')])
index4<-duplicated(df44$collection_id)
df4<-df44[!index4,]
dbWriteTable(con, name = 'collections', value = df4, row.names = FALSE, append = TRUE)

df5 <- unique(df[c('application_type_id', 'application_type')])
dbWriteTable(con, name = 'applications', value = df5, row.names = FALSE, append = TRUE)

df66 <- df[c('pymnts_info_id', 'pymnt_plan', 'total_pymnt', 'total_pymnt_inv', 'last_pymnt_amnt')]
index6<-duplicated(df66$pymnts_info_id)
df6<-df66[!index6,]
dbWriteTable(con, name = 'payments_info', value = df6, row.names = FALSE, append = TRUE)

df77 <- df[c('last_pymnt_d', 'next_pymnt_d', 'pymnts_info_id')]
index7<-duplicated(df77$last_pymnt_d)
df7<-df77[!index7,]
dbWriteTable(con, name = 'pymnts_schedule', value = df7, row.names = FALSE, append = TRUE)

df$sec_app_revol_util<- as.integer(df$sec_app_revol_util)
df8 <- unique(df[c('sec_app_id', 'sec_app_earliest_cr_line', 'sec_app_inq_last_6mths', 'sec_app_mort_acc', 'sec_app_open_acc', 'sec_app_revol_util', 'sec_app_open_act_il', 'sec_app_chargeoff_within_12_mths', 'sec_app_collections_12_mths_ex_med')])
dbWriteTable(con, name = 'secondary_applicants', value = df8, row.names = FALSE, append = TRUE)

df99 <- df[c('sub_grade', 'int_rate')]
index9<-duplicated(df99$sub_grade)
df9<-df99[!index9,]
dbWriteTable(con, name = 'interest_rates', value = df9, row.names = FALSE, append = TRUE)

df10 <- unique(df[c('address_id', 'zip_code', 'addr_state')])
dbWriteTable(con, name = 'addresses', value = df10, row.names = FALSE, append = TRUE)

df11 <- unique(df[c('inq_status_id', 'inq_last_6mths', 'inq_last_12m', 'inq_fi')])
dbWriteTable(con, name = 'inquires', value = df11, row.names = FALSE, append = TRUE)

df12 <- unique(df[c('credit_id', 'earliest_cr_line', 'open_acc', 'open_acc_6m', 'total_acc', 'delinq_2yrs', 'pub_rec', 'revol_bal', 'revol_util', 'out_prncp_inv', 'total_rev_hi_lim', 'open_act_il', 'open_il_12m', 'open_il_24m', 'tot_hi_cred_lim', 'total_bal_ex_mort', 'total_bal_il', 'il_util', 'all_util', 'pct_tl_nvr_dlq', 'percent_bc_gt_75', 'pub_rec_bankruptcies', 'inq_status_id')])
dbWriteTable(con, name = 'customer_credit_histories', value = df12, row.names = FALSE, append = TRUE)


df13 <- unique(df[c('account_info_id', 'acc_now_delinq', 'acc_open_past_24mths', 'tot_cur_bal','num_rev_accts','num_accts_ever_120_pd','mort_acc','total_cu_tl','open_rv_12m','open_rv_24m','max_bal_bc','num_actv_rev_tl','num_il_tl','num_op_rev_tl','num_rev_tl_bal_gt_0','num_sats','num_tl_120dpd_2m','num_tl_30dpd','num_tl_90g_dpd_24m','num_tl_op_past_12m','total_il_high_credit_limit','total_bc_limit','tax_liens')])
dbWriteTable(con, name = 'accounts_info', value = df13, row.names = FALSE, append = TRUE)

df14 <- unique(df[c('member_id', 'emp_title', 'emp_length', 'home_ownership', 'annual_inc', 'verification_status', 'debt_settlement_flag', 'grade', 'sub_grade', 'address_id', 'loan_id', 'bankcard_id', 'account_info_id', 'credit_id')])
dbWriteTable(con, name = 'customers', value = df14, row.names = FALSE, append = TRUE)


df15 <- unique(df[c('bankcard_id', 'member_id', 'bc_open_to_buy', 'bc_util', 'num_actv_bc_tl', 'num_bc_sats', 'num_bc_tl')])
dbWriteTable(con, name = 'bankcards', value = df15, row.names = FALSE, append = TRUE)

df16 <- unique(df[c('loan_id', 'member_id', 'loan_amnt', 'funded_amnt', 'installment', 'issue_d', 'dti', 'total_rec_prncp', 'total_rec_int', 'total_rec_late_fee', 'initial_list_status', 'out_prncp', 'last_credit_pull_d', 'chargeoff_within_12_mths', 'disbursement_method', 'term_id', 'status_id', 'purpose', 'application_type_id', 'sec_app_id', 'pymnts_info_id', 'collection_id')])
dbWriteTable(con, name = 'loans_info', value = df16, row.names = FALSE, append = TRUE)


```

disconnet rscript


```R
dbDisconnect(con)
dbUnloadDriver(drv)
```
