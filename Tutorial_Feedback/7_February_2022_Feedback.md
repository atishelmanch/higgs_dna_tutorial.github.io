# HiggsDNA tutorial feedback

This feedback is based on changes to the `correctionlib_systematics` branch of [`https://gitlab.cern.ch/smay/HiggsDNA`](https://gitlab.cern.ch/smay/HiggsDNA) from around [3 February 2022](https://gitlab.cern.ch/smay/HiggsDNA/-/commit/3b56611f6f4f52580cd565b94e2c4d8226f6477d). 

## Retire / Unretire jobs 

Testing the `--retire_jobs` and `--unretire_jobs` options for `scripts/run_analysis.py`. First ran the command:

```
python scripts/run_analysis.py --log-level "DEBUG" --config "metadata/tutorial/tth_presel_withSyst_2018DataOnly.json" --merge_outputs --output_dir "tutorial_tth_withSyst_dataOnly" --batch_system "condor"
```

After allowings some jobs to begin running, ran the same command but with the `--retire_jobs` flag added. At first there are minutes of delay, and then the manager appears to run and states:

```
DEBUG    [AnalysisManager : __init__] Retiring all unfinished jobs.
```

- Thought: For me, `condor_q` does not show any jobs as `DONE`, only as `RUN` or `IDLE`. Is there a way to implement that? I think it would help for monitoring through condor - even if one can monitor the percentage of finished jobs through `run_analysis.py`. 

## Blind option 

The blind option appears to work. Ran the following two commands:

```
python bonus/assess.py --input_dir "tutorial_tth_withSyst" --make_tables --group_procs "OtherH:ggH_M125,VBFH_M125,VH_M125|GJets:GJets_HT-600ToInf,GJets_HT-400To600,GJets_HT-200To400,GJets_HT-100To200,GJets_HT-40To100|VGamma:ZGamma,WGamma|tt+X:TTGG,TTGamma,TTJets" --signals "ttH_M125,OtherH" --output_dir ttH_noBlind  --plots Variables.json
python bonus/assess.py --input_dir "tutorial_tth_withSyst" --make_tables --group_procs "OtherH:ggH_M125,VBFH_M125,VH_M125|GJets:GJets_HT-600ToInf,GJets_HT-400To600,GJets_HT-200To400,GJets_HT-100To200,GJets_HT-40To100|VGamma:ZGamma,WGamma|tt+X:TTGG,TTGamma,TTJets" --signals "ttH_M125,OtherH" --output_dir ttH_Blind --blind  --plots Variables.json
```

And obtained the following two diphoton mass distributions clearly showing the blinding working as expected:

Blinded             |  Unblinded
:-------------------------:|:-------------------------:
![Blinded](Images/Diphoton_mass_dataMC_Blind.png)  |  ![Unblinded](Images/Diphoton_mass_dataMC_unblinded.png)

And obtained the following two yields tables, where the unblinded table contains more stats for bkg MC and data as expected:

Blinded             |  Unblinded
:-------------------------:|:-------------------------:
![Blinded](Images/data_mc_yield_table_blinded.png)  |  ![Unblinded](Images/data_mc_yield_table_unblinded.png)
