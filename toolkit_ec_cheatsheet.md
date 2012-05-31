# pbs-toolkit and eCompute cheat sheet

## Background

In days of yore, before [PBS Application Services](http://apu.calif.altair.com/twiki/bin/view/GridWorks/AIF), [Compute Manager](http://apu.calif.altair.com/twiki/bin/view/GridWorks/HWETWikiHome), and [PBS Desktop](http://apu.calif.altair.com/twiki/bin/view/GridWorks/PBSDRemoteServers), the available "higher level" layers of application-aware middleware and graphical submission interfaces were the PBS Toolkit and eCompute, respectively.

### PBS Toolkit

The Toolkit is a collection of Perl scripts, Bourne shell scripts, and config files that provide solver-submission functionality on top of PBS. Notable components include:

* `pbs-submit`: A Perl script which takes a number of arguments (e.g. ncpus, memory, input deck name, etc.) This script will validate user input, formulate it into resource requests, and produce a job script based on application templates (called "modfiles" -- see below)
* modfiles (`*.mod` and `modlib.sh`): Application templates used by the toolkit to create PBS job scripts based on validated user inputs.
* Sample epilogue, resourcedef, and `/etc/profile` bits to facilitate PBS interfacing.

#### Toolkit Assumptions

* POSIX is a requirement.  The toolkit was never successfully ported to Windows.
* Files are moved around the complex with `cp` rather than `rcp` or `scp`.
* Compute nodes have access to a shared job staging area from which jobs are submitted and results are returned.
	* Typically this is a large filesystem mounted on the headnode. In some cases, this is a SAN (see caveats below)
	* Local scratch directories on each compute node.  These are crossmounted (typically via [autofs](http://www.autofs.org/autofs-base.html#nfs))

#### Modifications to a typical base PBS installation

* The `PBS.pm` file is replaced by a "beefier" version that stores configuration defaults for the toolkit as well.
* Customizations to `resourcedef` and `pbs_mom`'s config are required to accomodate solver licensing and to allow for app-specific termination scripts, etc.
	