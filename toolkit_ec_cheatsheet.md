# pbs-toolkit and eCompute cheat sheet

## Background

In days of yore, before [PBS Application Services](http://apu.calif.altair.com/twiki/bin/view/GridWorks/AIF), [Compute Manager](http://apu.calif.altair.com/twiki/bin/view/GridWorks/HWETWikiHome), and [PBS Desktop](http://apu.calif.altair.com/twiki/bin/view/GridWorks/PBSDRemoteServers), the available "higher level" layers of application-aware middleware and graphical submission interfaces were the PBS Toolkit and eCompute, respectively.

### PBS Toolkit

The Toolkit is a collection of Perl scripts, Bourne shell scripts, and config files that provide solver-submission functionality on top of PBS. Notable components include:

* `pbs-submit`: A Perl script which takes a number of arguments (e.g. ncpus, memory, input deck name, etc.) This script will validate user input, formulate it into resource requests, and produce a job script based on application templates (called "modfiles" -- see below)
* modfiles (`*.mod` and `modlib.sh`): Application templates used by the 