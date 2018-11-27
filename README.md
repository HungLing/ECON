# Instructions on Econometrics Research

# A person only needs to save the 8 excel files, change the working directory to where you save them, and then open the 2 do files.
# One can get the process and the result on STATA following the instructions in this readme file.

# Import & Clean do files
# /*Import and Clean do file*/
# /*Set up dataset*/

# Must firstly set up working directory into where the excel files (data) are stored
pwd
cd "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project"
# Below codes create the .dta files for each of the excel files
local filelist x1 x2 x3 y
foreach i of local filelist{
clear 
import excel using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project/`i'.xlsx", firstrow
save "`i'.dta"
}
use y, clear
merge 1:m year using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\x1.dta"
drop _merge
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1.dta"
use y_x1, clear
merge 1:1 year using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\x2.dta"
drop _merge
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1x2.dta"
use y_x1x2, clear
merge 1:1 year using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\x3.dta"
drop C D E F G H I _merge
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1x2x3.dta"
use y_x1x2x3, clear
browse


# /*Set up dataset 69*/
# Must firstly set up working directory into where the excel files (data) are stored
pwd
cd "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project"
# Below codes create the .dta files for each of the excel files
local filelist x1_69 x2_69 x3_69 y_69
foreach i of local filelist{
clear 
import excel using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project/`i'.xlsx", firstrow
save "`i'.dta"
}
use y_69, clear
merge 1:m year using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\x1_69.dta"
drop _merge
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1_69.dta"
use y_x1_69, clear
merge 1:1 year using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\x2_69.dta"
drop _merge
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1x2_69.dta"
use y_x1x2_69, clear
merge 1:1 year using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\x3_69.dta"
br
drop _merge
br
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1x2x3_69.dta"
use y_x1x2x3_69, clear
br

# /*Combine 69 with previously set dataset*/
append using "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1x2x3.dta"
br
save "C:\Users\HUNG-LING\OneDrive\桌面\Econometrics\Project\y_x1x2x3_whole.dta"

# Result do file
# /*Results do file*/
# /*RESET*/
use y_x1x2x3_whole
reg GDP_GrowthRate WorkHours Salary LaborParticipationRate
predict yhat, xb
gen yhat2= yhat^2
gen yhat3= yhat^3
reg GDP_GrowthRate WorkHours Salary LaborParticipationRate yhat2 yhat3
test yhat2 yhat3
gen WorkHours2= WorkHours^2
reg GDP_GrowthRate WorkHours Salary LaborParticipationRate WorkHours2
drop yhat yhat2 yhat3
predict yhat, xb
gen yhat2= yhat^2
gen yhat3= yhat^3
reg GDP_GrowthRate WorkHours Salary LaborParticipationRate WorkHours2 yhat2 yhat3
test yhat2 yhat3

# /*Check Heteroskedasticity - Breusch Pagan*/
drop yhat yhat2 yhat3
reg GDP_GrowthRate WorkHours Salary LaborParticipationRate WorkHours2
predict r, resid
gen r2= r^2
reg r2 GDP_GrowthRate WorkHours Salary LaborParticipationRate WorkHours2
# /*Is Homoskedasticity*/

# /*Check Weakly Dependent Time Series - Cochrone Orcutt Prais-Winsten Estimation*/
drop r r2
tsset year, year
reg GDP_GrowthRate WorkHours Salary LaborParticipationRate WorkHours2
predict uhat, resid
reg uhat l.uhat

gen N1_GDP_GrowthRate = GDP_GrowthRate-(-.068331)*l.GDP_GrowthRate
gen N1_WorkHours = WorkHours -(-.068331)*l.WorkHours
gen N1_Salary = Salary -(-.068331)*l.Salary
gen N1_LaborParticipationRate = LaborParticipationRate -(-.068331)*l.LaborParticipationRate
gen N1_WorkHours2 = WorkHours2 -(-.068331)*l.WorkHours2

reg N1_GDP_GrowthRate N1_WorkHours N1_Salary N1_LaborParticipationRate N1_WorkHours2
predict N1_uhat, resid
reg N1_uhat l.N1_uhat

gen N2_GDP_GrowthRate = N1_GDP_GrowthRate - (-.0343669)*l.N1_GDP_GrowthRate
gen N2_WorkHours = N1_WorkHours - (-.0343669)*l.N1_WorkHours
gen N2_Salary = N1_Salary - (-.0343669)*l.N1_Salary
gen N2_LaborParticipationRate = N1_LaborParticipationRate - (-.0343669)*l.N1_LaborParticipationRate
gen N2_WorkHours2 = N1_WorkHours2 - (-.0343669)*l.N1_WorkHours2

reg N2_GDP_GrowthRate N2_WorkHours N2_Salary N2_LaborParticipationRate N2_WorkHours2
predict N2_uhat, resid
reg N2_uhat l.N2_uhat
# /*Since the rho generated from N2_uhat regression is 0.003, which is close to 0, I stopped*/
# /*Use:reg N2_GDP_GrowthRate N2_WorkHours N2_Salary N2_LaborParticipationRate N2_WorkHours2*/


# /*Test for Highly Persistent Series - Dicky Fuller Test*/
drop uhat N1_uhat N2_uhat
line N2_GDP_GrowthRate year /*Check if there is drift*/
tsset year, year
# /* Is N2_GDP_GrowthRate Highly Persistent? */
reg d.N2_GDP_GrowthRate l.N2_GDP_GrowthRate
dfuller N2_GDP_GrowthRate 
# /*MacKinnon approximate p-value for Z(t) = 0.0066*/
# /*N2_GDP_GrowthRate is not Highly Persistent*/

# /*Is N2_WH HP? */
reg d.N2_GDP_GrowthRate l.N2_GDP_GrowthRate
dfuller N2_WorkHours
# /* MacKinnon approximate p-value for Z(t) = 0.8925*/
# /*N2_WH IS HP*/

# /*Is N2_Salary HP? */
reg d.N2_Salary l.N2_Salary
dfuller N2_Salary 
# /*MacKinnon approximate p-value for Z(t) = 0.0391*/
# /*N2_Salary is not Highly Persistent*/

# /*Check if N2_LPR is HP? */
reg d.N2_LaborParticipationRate l.N2_LaborParticipationRate
dfuller N2_LaborParticipationRate 
# /*MacKinnon approximate p-value for Z(t) = 0.5983*/
# /*N2_LPR IS HP*/

# /*Is WH2 HP? */
reg d.N2_WorkHours2 l.N2_WorkHours2
dfuller N2_WorkHours2
# /*MacKinnon approximate p-value for Z(t) = 0.8535*/
# /*N2_WH2 IS HP*/


# /*Finite Distributed Lag Model - LRP*/
# /*N2_GDP_GR on N2_WH - LRP*/
reg d.N2_GDP_GrowthRate d.N2_WorkHours l.d.N2_WorkHours l2.d.N2_WorkHours l3.d.N2_WorkHours
# /*N2_GDP_GR on N2_WH - LRP - Sum(D+LD)=LRP, only last year counts*/

# /*N2_Salary - LRP*/
reg d.N2_GDP_GrowthRate d.N2_Salary l.d.N2_Salary l2.d.N2_Salary l3.d.N2_Salary
# /*LRP = Sum(D+L1+L2)*/

# /*N2_LPR*/
reg d.N2_GDP_GrowthRate d.N2_LaborParticipationRate l.d.N2_LaborParticipationRate l2.d.N2_LaborParticipationRate l3.d.N2_LaborParticipationRate
# /*LRP = Sum(D+L1)*/

# /*N2_WH2*/
reg d.N2_GDP_GrowthRate d.N2_WorkHours2 l.d.N2_WorkHours2 l2.d.N2_WorkHours2 l3.d.N2_WorkHours2
# /*LRP = Sum(D+L1)*/

# /*Result of the Regressions - for Conclusion*/
# /*The Regression Model for Conclusion*/
reg d.N2_GDP_GrowthRate d.N2_WorkHours l.d.N2_WorkHours d.N2_Salary l.d.N2_Salary l2.d.N2_Salary d.N2_LaborParticipationRate l.d.N2_LaborParticipationRate d.N2_WorkHours2 l.d.N2_WorkHours2


# /*F test for significance - whether to include in the final regression model*/
test d.N2_WorkHours l.d.N2_WorkHours d.N2_Salary l.d.N2_Salary l2.d.N2_Salary d.N2_LaborParticipationRate l.d.N2_LaborParticipationRate d.N2_WorkHours2 l.d.N2_WorkHours2

