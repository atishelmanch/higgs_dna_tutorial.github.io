# HiggsDNA Tutorial Notes

The version these comments are for should roughly be from [here](https://github.com/atishelmanch/higgs_dna_tutorial.github.io/commit/b9d5181b54862775f358c4ced0212dd896c4e829) (around 27-31 January 2022). 

From a first time use, in the end I was able to make the expected plot for the ttH tutorial example, with a few MC and one data job failing. 

## Setup

- I’m running these steps on lxplus 
- Minor convenience related comment: I noticed I was not able to clone recursively via ssh protocol, i.e. `git clone --recursive ssh://git@gitlab.cern.ch:7999/smay/HiggsDNA.git` without having to log in manually due to the submodule jsonpog-integration. Does it have to be this way? (Perhaps not a big deal if the login only has to happen once or rarely)
- For reference, the size of my higgs-dna conda environment is 1.7 GB
- On lxplus, I was able to run “conda activate higgs-dna” successfully without any permission errors. 
- When running setup.sh, I get:
  - `gzip: jsonpog-integration/POG/TAU/2016postVFP_UL/tau.json already exists; do you wish to overwrite (y or n)?`
  - `gzip: jsonpog-integration/POG/TAU/2016preVFP_UL/tau.json already exists; do you wish to overwrite (y or n)?`
  - `gzip: jsonpog-integration/POG/TAU/2017_UL/tau.json already exists; do you wish to overwrite (y or n)?`
  - `gzip: jsonpog-integration/POG/TAU/2018_UL/tau.json already exists; do you wish to overwrite (y or n)?`
- I’m not sure if this is from a previous installation of HiggsDNA or something like that, so I just chose to overwrite. 

## Running ttH analysis example

### Locally
- Trying: `python scripts/run_analysis.py --config metadata/tutorial/tth_preselection.json`
- Just to report some expected behavior, when running this an error comes up because no voms proxy is set:
`/afs/cern.ch/work/a/atishelm/public/Higgs_DNA_Tutorial/Error_From_no_voms.log`
- I like all of the default DEBUG output! E.g. the selection efficiencies. Could be useful for quick checks during an analysis. I wonder if a feature to output these values to a file/table (for example just efficiency values, not the full log) would be useful? I see this can be done later for yields. 
- Purely from curiosity, might not be important at all: Why does 2018 data in this setup consist of so many more input files than 2016/2017, and take about 5x longer to run? Naively I would expect similar processing times since the lumi is only a factor of ~ 1.4 greater than 2017. Based on the following log output:
`/afs/cern.ch/work/a/atishelm/public/Higgs_DNA_Tutorial/Data_output.log`

### On HTCondor
- Running the command:
```
python scripts/run_analysis.py --log-level "DEBUG" --config "metadata/tutorial/tth_presel_withSyst.json" --merge_outputs --output_dir "tutorial_tth_withSyst" --batch_system "condor"`
```
- If I understand what’s going on correctly, I notice it seems to take a fair bit of time to find the files for given datasets. Are these files being searched for on the fly rather than through use of something like the flashgg catalogs? 
- By default, the output from the automated proxy copy attempt: 
```
DEBUG    [CondorManager : prepare_inputs] Copying proxy /tmp/x509up_u95168 to home area /afs/cern.ch/work/a/atishelm/private/HiggsDNA/ in order to copy to jobs.                                                      managers.py:386cp: cannot create regular file ‘/afs/cern.ch/work/a/atishelm/private/HiggsDNA/’: Not a directory
```
- This specifies a non-existent directory. This was run in `/afs/cern.ch/work/a/atishelm/private/HiggsDNA_Tutorial/HiggsDNA`
- Looks like the reason for this issue is that my second to last directory contains the “HiggsDNA” string, which is parsed [here](https://gitlab.cern.ch/smay/HiggsDNA/-/blob/2e8054b808f2c872eb7940aca799f9f9754c3b68/higgs_dna/job_management/managers.py#L338). Any particular reason this isn’t just set to “pwd” or “pwd” + “/”? I guess with the current way you can be in some nested directory within HiggsDNA and still run run_analysis.py. 
- It seems the job submission (after locating the files) takes a fair amount of time. Perhaps this is something that may be improved from the Massi updates? 
- After completing jobs for:
  - Data_2016
  - Data_2017
  - ttH_M125_2016
  - ttH_M125_2017
  - ttH_M125_2018
  - ggH_M125_2016
  - ggH_M125_2017
  - ggH_M125_2018
  - Diphoton_2016
- Condor priority goes from ~0 to ~1.25 million, and remaining jobs stay idle. Maybe just due to miscellaneous HTCondor factors.
- If I close the JobsManager : summarize running / printout, is there a way to relaunch it? 
  - Sam: Yes - just run the same command used to launch the jobs.
- There seems to be one problematic Data_2018 job where I’ve placed the err and out files here:
  - `/afs/cern.ch/work/a/atishelm/public/Higgs_DNA_Tutorial/Data_2018_job_8614187.0.err`
  - `/afs/cern.ch/work/a/atishelm/public/Higgs_DNA_Tutorial/Data_2018_job_8614187.0.out`
- As pointed out by Sam, the output summary.json and merged files will not be created by run_analysis.py until all jobs are finished or retired. Forced that in this instance by commanding condor_rm and allowing run_analysis.py to rerun until all jobs were retired. 

## 3.5 Assessing the outputs
- When saving data yields, is there an option to make sure the data is blinded? 
- A minor comment, but I notice in the “Putting it all together” python assess command, the input directory is different from before (ends with _v2) 
- Maybe it would be useful to include a command for making the data / MC plots? As a first time user it wasn’t obvious to me. I suppose this addition is correct (and what worked for me):
`--plots Variables.json`
- I like the `--cuts` option! 
- I'm able to succesfully produce the expected plots
