Description and code for P(∆i) 

Explanation of dataset and code for the paper entitled “Describing changes in features of psychopathy via an individual-level measure of P(Δ)”
https://doi.org/10.1007/s10940-020-09472-8 ; https://rdcu.be/b6TRj 

This manuscript and its analysis was heavily based on work by Barnes et al. (2017): DOI 10.1007/s10940-016-9298-5 ; https://link.springer.com/content/pdf/10.1007/s10940-016-9298-5.pdf. Highly recommend reading this paper before our own. They also provide code for the aggregate-level measure of P(∆), which our code draws from.

Data description for paper

The Pathways to Desistance Study dataset includes 10 total measurement points. However, the YPI was not assessed at the baseline measurement period. As well, some intervals between measurement points are six months; others 12 months. To ensure equal distance between intervals we examined the, 12-month, 24-month, 36-month, 48-month, 60-month, 72-month, and 84-month measurement periods, which corresponds to six time intervals. Given concerns about extending measures of psychopathy developed for boys to girls, we only examined boys in this analysis. The Pathways data are available from ICPSR. 

The main variables of analysis were YPI total scores and scores on its GM, CU, and II dimensions. Data were imported into Stata and the code below were used to produce RCI values for the within-person change component and the between-person difference component of relative change/stability. Within-person change refers to change in raw scores; between-person difference refers to change in rank order. I attached our .do files for the analysis and posted the code below as well. The code is used to identify instances in which an individual experienced a reliable within-individual change in test score AND a reliable within-individual change in their rank ordering between all contiguous measurement points (e.g., between wave 1 to wave 2, wave 2 to wave 3, and so on). All instances in which both reliable within-individual change in test score AND reliable within-individual change in rank order are used to define relative change. All other cases represent relative stability (i.e., the two categories are exhaustive). 

P(∆i) is calculated as the proportion of all wave to wave comparisons in which relative change occurred (i.e., sumP(∆i) / K where K = number of comparison periods).


YPI Code
*'S' IS USED TO IDENTIFY RAW SCORE, 'R' IS USED TO IDENTIFY RANK ORDER SCORE

* -------------------------
* Christensen & Mendoza's RCI
* where RCI = (t2-t1)/sdiff
* note, numerator is the diff in score between two measurement periods
* denominator is standard error of measurement (SEM), 
* which accounts for measurement error (i.e., alpha reliability)
* -------------------------

* gen variables holding info needed for denominator
foreach var of varlist S*YPI {
quietly sum `var'
quietly gen sd`var'=`r(sd)'
}
capture gen alpha1=.93
capture gen alpha2=.94
capture gen alpha3=.94
capture gen alpha4=.94
capture gen alpha5=.94
capture gen alpha6=.94
capture gen alpha7=.94

* calculate the RCI for each wave-to-wave comparison

* w2-w1
gen 	SYPI_RCI2_1=(S2YPI-S1YPI)/sqrt([sdS2YPI*sqrt(1-alpha2)]^2+[sdS1YPI*sqrt(1-alpha1)]^2)
sum SYPI_RCI2_1, d

* w3-w2
gen SYPI_RCI3_2=(S3YPI-S2YPI)/sqrt([sdS3YPI*sqrt(1-alpha3)]^2+[sdS2YPI*sqrt(1-alpha2)]^2)
sum SYPI_RCI3_2, d

* w4-w3
gen SYPI_RCI4_3=(S4YPI-S3YPI)/sqrt([sdS4YPI*sqrt(1-alpha4)]^2+[sdS3YPI*sqrt(1-alpha3)]^2)
sum SYPI_RCI4_3, d

* w5-w4
gen SYPI_RCI5_4=(S5YPI-S4YPI)/sqrt([sdS5YPI*sqrt(1-alpha5)]^2+[sdS4YPI*sqrt(1-alpha4)]^2)
sum SYPI_RCI5_4, d

* w6-w5
gen SYPI_RCI6_5=(S6YPI-S5YPI)/sqrt([sdS6YPI*sqrt(1-alpha6)]^2+[sdS5YPI*sqrt(1-alpha5)]^2)
sum SYPI_RCI6_5, d

* w7-w6
gen SYPI_RCI7_6=(S7YPI-S6YPI)/sqrt([sdS7YPI*sqrt(1-alpha7)]^2+[sdS6YPI*sqrt(1-alpha6)]^2)
sum SYPI_RCI7_6, d

* calculate the RCI YPI RANK SCORES for each wave-to-wave comparison

* gen variables holding info needed for denominator
foreach var of varlist R*YPI {
quietly sum `var'
quietly gen sd`var'=`r(sd)'
}


*w2-w1
gen RYPI_RCI2_1=(R2YPI-R1YPI)/sqrt([sdR2YPI*sqrt(1-alpha2)]^2+[sdR1YPI*sqrt(1-alpha1)]^2)
sum RYPI_RCI2_1, d

* w3-w2
gen RYPI_RCI3_2=(R3YPI-R2YPI)/sqrt([sdR3YPI*sqrt(1-alpha3)]^2+[sdR2YPI*sqrt(1-alpha2)]^2)
sum RYPI_RCI3_2, d

* w4-w3
gen RYPI_RCI4_3=(R4YPI-R3YPI)/sqrt([sdR4YPI*sqrt(1-alpha4)]^2+[sdR3YPI*sqrt(1-alpha3)]^2)
sum RYPI_RCI4_3, d

* w5-w4
gen RYPI_RCI5_4=(R5YPI-R4YPI)/sqrt([sdR5YPI*sqrt(1-alpha5)]^2+[sdR4YPI*sqrt(1-alpha4)]^2)
sum RYPI_RCI5_4, d

* w6-w5
gen RYPI_RCI6_5=(R6YPI-R5YPI)/sqrt([sdR6YPI*sqrt(1-alpha6)]^2+[sdR5YPI*sqrt(1-alpha5)]^2)
sum RYPI_RCI6_5, d

* w7-w6
gen RYPI_RCI7_6=(R7YPI-R6YPI)/sqrt([sdR7YPI*sqrt(1-alpha7)]^2+[sdR6YPI*sqrt(1-alpha6)]^2)
sum RYPI_RCI7_6, d

*=======================================
*categories of rci raw scores
*=======================================


*creating category values.

*RCI YPI Raw Categories

foreach var of varlist SYPI_RCI2_1 - SYPI_RCI7_6 {
quietly gen `var'reliableChange=2
}

* "tag" reliable changes for case 1
foreach var of varlist SYPI_RCI2_1 - SYPI_RCI7_6 {
replace `var'reliableChange=3 if `var' >=(1.96) 
} 

* "tag" reliable changes for case 2
foreach var of varlist SYPI_RCI2_1 - SYPI_RCI7_6 {
replace `var'reliableChange=1 if `var' <=(-1.96) 
} 


*RCI YPI Rank Categories

foreach var of varlist RYPI_RCI2_1 - RYPI_RCI7_6 {
quietly gen `var'reliableChange=2
}

* "tag" reliable changes for case 1
foreach var of varlist RYPI_RCI2_1 - RYPI_RCI7_6 {
replace `var'reliableChange=3 if `var' >=(1.96) 
} 

* "tag" reliable changes for case 2
foreach var of varlist RYPI_RCI2_1 - RYPI_RCI7_6 {
replace `var'reliableChange=1 if `var' <=(-1.96) 
} 

