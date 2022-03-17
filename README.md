# AliceO2
In this repository you can find instructions on how to get started with O2 Framework. Keep in mind that this framework is constantly evolving and many files in this repository can be outdated.
## How to install O2 Framework?
Build O2 with alibuild as explained [here](https://aliceo2group.github.io/analysis-framework/docs/installing/). You need to follow instructions [here](https://alice-doc.github.io/alice-analysis-tutorial/building/custom.html) but do not follow instructions on "build packages" there. You also need to acquire GRID certificate, it's explained in detail [here](https://alice-doc.github.io/alice-analysis-tutorial/start/cert.html).
Keep in mind most of us use Ubuntu 18.04 / 20.04.
## How to obtain data files?
To get AOD file go [here](https://alimonitor.cern.ch/trains/train.jsp?train_id=132) and scroll down. Choose train number you're interested in and click on run number (for example 246) and then on Test Results (in the middle). Scroll down and find full train option and then click on output. Now you're supposed to see a lot of files, but you're only interested in `AO2D.root` and it's usually the first. Then you need to only click on it and it will start to download.
Information about trains (job details) can be found [here](https://alimonitor.cern.ch/job_details.jsp).
### Possible errors
If you can't even enter alimonitor it is possible that you haven't added your GRID certificate to your browser correctly.
## How to create?
You can find useful examples on how to write your code in `~/alice/O2Physics/Tutorials/src/`. 
Then in for example: `~/alice/O2Physics/Tutorials/src/` place your code in C++. Remember to always change CMakeList in `~/alice/O2Physics/Tutorials/` when you add new file to your analysis. Without that step, you won't be able to build it. 
## How to build?
In `~/alice/sw/BUILD/O2Physics-latest-master/O2Physics` type `alienv load ninja/latest O2Physics/latest ` and then `ninja stage/bin/your-analysis-file` to build your code. 
Keep in mind that if you add a new file or modify CMakeList you need to use `cmake .` 
Then `cp` it to directory with AOD file. (You can skip this step when you use `ninja install` instead of `ninja`) 
## How to run a code?
In your dir with AOD file type: <br>
`alienv enter O2Physics/latest` and  `alien-token-init your-cern-nickname`<br>
Then to run your code you only need to type: <br>
`./o2-analysistutorial-simple-analysis --aod-file <aod_file_name> -b` <br>
But sometimes you need to add more for it to work. When you see `Exception caught: Couldn't get TTree [sth]` you need to add it to your analysis. <br>
For example while performing PID analysis. In this case you need to type for example: <br>
`./o2-analysistutorial-simple-analysis --aod-file <aod_file_name> --aod-memory-rate-limit 100000000000000 -b | o2-analysis-pid-tpc -b | o2-analysis-pid-tof -b | o2-analysis-trackselection -b | o2-analysis-tracksextention -b ` <br>
But keep in mind not everything might be useful in your case and `-b` stops GUI from showing.
### Possible errors
While trying to run various O2 tasks/tutorials you can run into fatal errors with non-existing CCDB entries for specific data. <br>
Usually adding `--isRun2MC` to executables (likely `o2-analysis-timestamp`) might help. We also added `--isMC` to o2-analysis-event-selection. <br>
For example: `./o2-analysis-mm-dndeta --aod-file AODmc2.root --aod-memory-rate-limit 100000000000000 -b | o2-analysis-pid-tpc -b | o2-analysis-pid-tof -b | o2-analysis-trackselection -b | o2-analysis-trackextension -b | o2-analysis-event-selection --isMC -b | o2-analysis-timestamp --isRun2MC -b `
## Results
Running command `./o2-analysistutorial-simple-analysis --aod-file <aod_file_name> -b` (and others of that type) creates .root file and .json file. <br>
To enter AnalysisResults.root file type: `root -l` and then `TBrowser`. (`-l` is optional but it runs root without additional information about it).<br>
You can change histograms manually or you can write macros and run them on .root file. <br>
More on root and how to use it [here](https://root.cern/manual/first_steps_with_root/). <br>
## Helpful commands and pages
Sometimes you might find `grep` command useful. It's especially useful, when you want to find tutorials with functions you are interested in. <br>
Additional Testing O2 Framework page - [here](https://twiki.cern.ch/twiki/bin/viewauth/ALICE/AliceO2WP14AF).
# Femtodream / Alifemto
Codes are available here: `O2Physics/PWGCF/FemtoDream/`<br>
First of all you need to cut your AOD using this command: <br>
`o2-analysis-timestamp --aod-file AO2D.root -b | o2-analysis-multiplicity-table -b | o2-analysis-event-selection --syst pp -b | o2-analysis-trackextension -b | o2-analysis-pid-tpc -b | o2-analysis-pid-tof -b | o2-analysis-weak-decay-indices -b | o2-analysis-lf-lambdakzerobuilder -b | o2-analysis-cf-femtodream-producer --aod-writer-keep AOD/FEMTODREAMPARTS/0,AOD/FEMTODREAMCOLS/0 --aod-writer-resfile FemtoAO2D -b --aod-memory-rate-limit 600000000` <br>
Then you just need to run your code, for example something like that:<br>
`o2-analysis-cf-femtodream-hash -b | o2-analysis-cf-femtodream-pair-track-track --aod-file FemtoAO2D.root --aod-memory-rate-limit 600000000 --ConfCutPartTwo 5543046 --ConfCutPartOne 5543046`<br>
