% Read Me
% Dave Walker (walker@altair.com)
% 2012-May-2012

# pbs-toolkit and eCompute cheat sheet

## Background

In days of yore, before [PBS Application Services](http://apu.calif.altair.com/twiki/bin/view/GridWorks/AIF), [Compute Manager](http://apu.calif.altair.com/twiki/bin/view/GridWorks/HWETWikiHome), and [PBS Desktop](http://apu.calif.altair.com/twiki/bin/view/GridWorks/PBSDRemoteServers), the available "higher level" layers of application-aware middleware and graphical submission interfaces were the PBS Toolkit and eCompute, respectively.

## Important Reader's Digest Stuff

Assuming you really don't care at all about the toolkit, but you have a customer who's using it and you just want to muddle through and get to the next ticket, you can probably just get away with absorbing the next few bullets.

* The toolkit is really just a templating system for creating job scripts based on user inputs. Given a few details (e.g. an input deck, cpu count, solver version, memory) the toolkit generates a script (let's call it `foo.scr`) that contains a block of `#PBS` directives and just enough Bourne shell to launch the job and return the results to the point of submission.
* The `.mod` files aren't nearly as hairy as they look. They're loaded with confusing-looking case statements, but most of that is just logic to figure out paths and versions (things that are handled in site-config nowadays), setting up environment variables (handled in `app-conv` nowadays) and building a command line (handled in `start.py` nowadays.)
* If you need to bluff your way through it, submit (as a user) with `pbs-submit --debug`, then when things blow up look for the `.e` file and the `.scr` to figure out what went wrong. 

### PBS Toolkit

The Toolkit is a collection of Perl scripts, Bourne shell scripts, and config files that provide solver-submission functionality on top of PBS. Notable components include:

* `pbs-submit`: A Perl script which takes a number of arguments (e.g. ncpus, memory, input deck name, etc.) This script will validate user input, formulate it into resource requests, and produce a job script based on application templates (called "modfiles" -- see below)
* modfiles (`*.mod` and `modlib.sh`): Application templates used by the toolkit to create PBS job scripts based on validated user inputs.
* Sample epilogue, resourcedef, and `/etc/profile` bits to facilitate PBS interfacing.

#### Toolkit Assumptions

* POSIX is a requirement.  The toolkit was never successfully ported to Windows.
* Files are moved around the complex with `cp` rather than `rcp` or `scp`.
* Have a working debugged passwordless `ssh` setup for the complex, forwards and back. 
* Compute nodes have access to a shared job staging area from which jobs are submitted and results are returned.
	* Typically this is a large filesystem mounted on the headnode. In some cases, this is a SAN (see caveats below)
	* Local scratch directories on each compute node.  These are crossmounted (typically via [autofs](http://www.autofs.org/autofs-base.html#nfs))

#### Modifications to a typical base PBS installation

* Highly recommend that you create a `toolkit` symlink under `$PBS_EXEC` on all machines in the complex (headnode and all moms) that points to a shared filesystem. A separate toolkit directory on every node leads to pain and madness -- don't go there.
* The `PBS.pm` file is replaced by a "beefier" version that stores configuration defaults for the toolkit as well.
	* Since this needs to be present on all nodes, use a symlink to a shared copy.
* Customizations to `resourcedef` and `pbs_mom`'s config are required to accomodate solver licensing and to allow for app-specific termination scripts, etc.  Recommend that you use symlinks to the shared filesystem (mentioned above) to store all the mom configs.
* Place the sample `pbs.sh` and `pbs.csh` in `/etc/profile.d` on all machines in the complex.

#### Modfile Recommendations

* To be written


	