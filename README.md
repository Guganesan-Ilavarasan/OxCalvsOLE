## OxCalvsOLE</br>

### multicoremasterrun.R:</br>
</br>
<div align="justify">
   
This main run script file handles all the commands to other modules attached for analysis for the OxCal and OLE approaches. Accordingly, the module first assigns all the user-defined parameters to run the entire analysis, namely, ``n <- 100`` – this assigns how many iterations of analysis (simulations) would be needed, `nsamples <- 100` – this assigns total now many sample sizes the analysis would run with, either 10, 50 or 100 for this study. ``true.start <- 5000`` and ``true.end <- 3000``, these two would pass the true start and end dates from within, which the random generator would later simulate dates.<br/>
</div>
</br>

<div align="justify">

``rateofadoption <- 0.1`` – another crucial variable in the study; this controls the rate at which the adoption of technology is controlled within the distribution of the dates. This will be assigned to the ``k`` value of the logistic growth simulator, from which random dates are generated; the higher the number, the faster the adoption rate is. As part of this study, three values are used: 0.01, 0.05 and 0.1. A logistic growth distribution was used instead of a uniform or normal as it would best replicate a society’s onset of using a technology with a low yet non-zero start and a steady <i>S</i> curve upwards followed by either a stagnation or a drop. This is achieved through ``nibleCarbon`` package’s ``rLogisticGrowth()`` function. Then, the derived calendar dates are back-calibrated into radiocarbon ages through ``uncalibrate()``. Finally, ``error <- 20`` assigns the error rate, attributed to all the randomly generated calendar dates to mimic the calibration error margin during calibration.<br/>
</div>
</br>

```
calender.dates<<-replicate(n,rLogisticGrowth(a=lower_earliest,b=upper_latest,k=carrying_capacity,r=growth_rate))
radiocarbon.ages  <<- uncalibrate(calender.dates)$rCRA
```
</br>


<div align="justify">

In brief, the script first assigns empty data frames to capture the results of all four approaches. Then, it assigns the inbuilt functions, ``mainrun()`` and ``genscript()``, followed by a parallel computing facilitation functions ``runfun()`` and ``runfun1()``. Consequently, it first calls the ``mainrun()`` with passed-in arguments of ``nsamples``, ``true.start``, ``true.end``, ``rateofadoption`` and ``error`` to generate a list of random calendar dates (saved in object ``radiocarbon.ages``) and the error dates (``error.dates``) into times the total simulation ``n``. So, a list of 100 lists of 100 dates for ``n <- 100`` and ``nsamples <- 100``. This is done through the external module ``randomgen.R``’s ``simdates()`` function, whose composition will be briefed soon. The derived dates generate their respective OxCal scripts through the ``genscript()`` function, which again utilises the custom ``oxcalScript.R`` script’s ``oxcalScriptGen()`` function.</br>
</div>
</br>

<div align="justify">

From the derived <i>.OXCAL</i> files, the parallel computing functions ``runFun`` and ``runFun1`` are called to run both the OxCal models and their results are saved in lists called ``out`` and ``out1``. Later, these lists are derived out through ``for loops`` to be cleaned through ``oxcalcleaner`` and ``oxcalcleanerunif`` called from external modules of the same names. Then, the now accumulated results in data frames called ``trap_df`` and ``unif_df`` are written out as <i>.CSV</i>s were directed to be named according to the sample no. and adoption rate their analysis ran with, e.g., <i>sample_100_rate_0.01_Trap_OxCal.CSV</i>. Consequently, with the parallel approach now over, another ``for loop`` is initiated to loop through all the lists of dates in ``radiocarbon.ages`` to run both the methods of OLE, `OLE.Median` and `OLE.CI`, also their results being written out into <i>.CSV</i>s in the same manner as OxCal’s results as soon as their loop is over <i>(Figure below)</i>.</br>
</div>
</br>

>Flowchart of the OxCal modules</br>

![image](https://github.com/Guganesan-Ilavarasan/OxCalvsOLE/assets/85569213/941dfe00-40f9-4d25-8f00-edb028cdb17c)

