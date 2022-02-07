# HiggsDNA tutorial feedback

This feedback is based on changes to the `correctionlib_systematics` branch of [`https://gitlab.cern.ch/smay/HiggsDNA`](https://gitlab.cern.ch/smay/HiggsDNA) from around [3 February 2022](https://gitlab.cern.ch/smay/HiggsDNA/-/commit/3b56611f6f4f52580cd565b94e2c4d8226f6477d). 

## Blind option 

The blind option appears to work. Ran the following two commands:

```
python bonus/assess.py --input_dir "tutorial_tth_withSyst" --make_tables --group_procs "OtherH:ggH_M125,VBFH_M125,VH_M125|GJets:GJets_HT-600ToInf,GJets_HT-400To600,GJets_HT-200To400,GJets_HT-100To200,GJets_HT-40To100|VGamma:ZGamma,WGamma|tt+X:TTGG,TTGamma,TTJets" --signals "ttH_M125,OtherH" --output_dir ttH_noBlind  --plots Variables.json
python bonus/assess.py --input_dir "tutorial_tth_withSyst" --make_tables --group_procs "OtherH:ggH_M125,VBFH_M125,VH_M125|GJets:GJets_HT-600ToInf,GJets_HT-400To600,GJets_HT-200To400,GJets_HT-100To200,GJets_HT-40To100|VGamma:ZGamma,WGamma|tt+X:TTGG,TTGamma,TTJets" --signals "ttH_M125,OtherH" --output_dir ttH_Blind --blind  --plots Variables.json
```

![test](Tutorial_Feedback/Diphoton_mass_dataMC_Blind.pdf)
