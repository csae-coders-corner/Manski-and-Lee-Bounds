# Manski-and-Lee-Bounds
Methods for accounting for attrition vary according to whether we assume that attrition is unsystematic between the treatment and control groups or not. There are several standard techniques to deal with attrition and sample selection; I focus in this post on how to code boundary approaches proposed by Manski (1990) and Lee (2009)  .

To illustrate the method, I use data from a randomized controlled trial (RCT) conducted by Blattman et al. (2011, 2014). The data can be downloaded from Harvard Dataverse2.

Suppose we are interested in investigating the impact of randomly assigned cash transfers (given at baseline) on migration (measured at endline). After examining the data, we find that there is a high level of attrition at endline (25% at the first endline) and that this might affect the validity of our results because it is likely that attrition and migration are related, in perhaps a non-systematic way. For example, what if the attrited themselves are the people who migrated?

The first boundary approach (Manski Upper Bound) proposes an extreme assumption that all the attriters in the treatment group are migrants and all the attriters in the control group are not migrants. This generates an upper bound. The lower bound would thus be the exact opposite: all the attriters in the treatment group are non-migrants and all the attriters in the control group are migrants. 

Figure 1.a illustrates how to construct these bounds in Stata3, where the bounds themselves then become the two extreme cases for the outcome under study. 

<img width="256" alt="manski 1" src="https://github.com/csae-coders-corner/Manski-and-Lee-Bounds/assets/148211163/ec7bdc64-7fb6-4571-b783-be75b5be0d44">

Figure 1.b shows the code for our specific example, where the outcome is migration. Columns 1 and 2 of table 1 display the results4.

<img width="263" alt="manski 2" src="https://github.com/csae-coders-corner/Manski-and-Lee-Bounds/assets/148211163/e11e98cf-72bb-48f8-8970-41adee2fee4c">

>[!Note]
> An underlying assumption of the Manski bound is that the outcome variable should be bounded. The second approach discussed below does not need this to be the case. 

The second boundary approach Lee Bounds is a type of trimming procedure, where the group (either control or treatment) that potentially suffers the most from sample attrition is trimmed (Tauchmann, 2014). The lower and upper bound are computed by making assumptions about the missing data of the outcomes of the attrition group. Lee (2009) bounds rely on a few assumptions only, namely random assignment of treatment and monotonicity, where the latter means that the treatment status affects attrition in just one direction. In other words, being assigned to treatment makes attrition either more or less likely for any observation. There is a simple built-in command leebounds in Stata that takes care of the process. But what is leebounds exactly doing?


1. It first calculates the below ratio:

    x=(fraction remaining in less attrited group-fraction remaining in more     attrited group)/(fraction remaining in less attrited group)
	
2. It then drops the lowest percentages of the outcomes from the less attrited group.
3. Afterwards, it creates the first bound by calculating the mean outcome for the trimmed group (less attrited after dropping the lowest percentages), which is then compared to the mean outcomes in the group with lower attrits.
4. To create the lower bound, it drops the highest percentages of the outcomes from the less attrited group.
5. Lastly, it calculates the mean outcome for the trimmed group (less attrited after dropping the highest percentages), which is then compared to the mean outcomes in the group with lower attrits.

Figure 1.a displays the simple code and Figure 1.b shows the code for our specific example. Columns 1 and 2 of table 1 display the results. The series of figures below show the detailed commands in Figures 1.a and 1.b being implemented, followed by running the relevant regressions5.

<img width="326" alt="manski 3" src="https://github.com/csae-coders-corner/Manski-and-Lee-Bounds/assets/148211163/c6203158-36d5-4d87-9234-51081045dd38">

<img width="326" alt="manski 4" src="https://github.com/csae-coders-corner/Manski-and-Lee-Bounds/assets/148211163/0ae483b2-90ae-431f-978d-352237528198">

![manski 5](https://github.com/csae-coders-corner/Manski-and-Lee-Bounds/assets/148211163/b5681745-4a7f-4585-aaf4-99783d74747d)

Table 1- Manski Bounds and Lee Bounds: Impact of cash transfers on migration

| VARIABLES                                       | (1)	Migration Manski Upper Bound	| (2) Migration Manski Lower Bound	| (3) Migration Lee Bound |
|------------------------------------------------ |-----------------------------------|--------------------------------------|-------------------------|	
|Assigned Treatment Status	                      | 0.0701*** (0.0181)                |0.0116 (0.0185)                    	 |                         |
|Lee Bounds (trimming highest ~5% of observations)|                                   |                                      |	0.0186                 |
|Lee Bounds (trimming lowest ~5% of observations) |                                   |                                      | (0.0195)	0.0676***      |
|Constant	                                        | 0.293*** (0.0127)	                | 0.345*** (0.0130)	                   | (0.0191)                |
|Observations	                                    | 2,677	                            | 2,677                                |  2,677                  |
|R-squared	                                      | 0.006                            	| 0.000                                |                         |	

Standard errors in parentheses
*** p<0.01, ** p<0.05, * p<0.1

## References

Behaghel, Luc, Bruno Crepon, Marc Gurgand, and Thomas Le Barbanchon. 2012. “Please Call Again: Correcting Non-Response Bias in Treatment Effect Models.” SSRN Journal. https://doi.org/10.2139/ssrn.2157893.

Blattman, Christopher; Fiala, Nathan; Martinez, Sebastian, 2014, "Northern Uganda Social Action Fund - Youth Opportunities Program", https://doi.org/10.7910/DVN/27898, Harvard Dataverse, V1. 

Blattman, Christopher; Fiala, Nathan; Martinez, Sebastian, 2019, "The long term impacts of grants on poverty: 9-year evidence from Uganda’s Youth Opportunities Program", https://doi.org/10.7910/DVN/V0N0HA  Harvard Dataverse, V1. 

Duru, M., & Kopper, S. (n.d.). Data Analysis. The Abdul Latif Jameel Poverty Action Lab (J-PAL). Retrieved January 27, 2022, from https://www.povertyactionlab.org/resource/data-analysis. 

Lee, David. 2009. “Training, Wages, and Sample Selection: Estimating Sharp Bounds on Treatment Effects.” The Review of Economic Studies 76 (3): 1071–1102. 10.3386/w11721  

McCarthy, Ian, Daniel L. Millimet, and Manan Roy. 2015. “Bounding Treatment Effects: A Command for the Partial Identification of the Average Treatment Effect with Endogenous and Misreported Treatment Assignment.” The Stata Journal 15 (2): 411–36. https://doi.org/10.1177/1536867x1501500205.

Tauchmann, Harald. 2014. “Lee (2009) Treatment-Effect Bounds for Nonrandom Sample Selection.” The Stata Journal 14 (4): 884–94. 10.1177/1536867x1401400411.

**Razan Amine, Research Assistant in Economics, 16 February 2022**
