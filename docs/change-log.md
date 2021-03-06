# Change log

## June 8, 2018: 0.9.6
- Auto-delegate common proc config names to aggr
- Add proc.origin to save the original proc id for copied procs
- Remove brings, add proc.infile to swith '{{in.(infile)}}' to job.indir path, original path or realpath of the input file
- Add process lock to avoid the same processes run simultaneously
- Use built-in Box instead of box.Box
- Merge template function Rvec and Rlist into R, add repr
- Fix #29 and #31, and fix other bugs

## Mar 6, 2018: 0.9.5
- Add proc.dirsig to disable/enable calculating signatures from deep directories
- Add Jobmgr class to handle job distribution
- Allow channel.rowAt and colAt to return multiple rows and columns, respectively
- Allow empty channel as input (process will be skipped)
- Refine all tests
- Rewrite thread-safe file system helper functions
- Add specific exception classes
- Report line # when error happens in template
- Add progress bar for jobs
- Allow stdout and stderr file as output

## Dec 27, 2017: 0.9.4
- Add yaml support for config file (#26).
- Allow empty list for input files.
- Merge continuous job ids in log (Make the list shorter).
- More details when internal template failed to render (#25)
- Ignore .yaml config files if yaml module is not installed.
- sleep before testing isRunning to avoid all jobs running it at the same time.
- Use repr to output p.args and p.props.
- Merge Proc attributes profile and runner. Profile is now an alias of runner, and will be removed finally.

## Nov 20, 2017: 0.9.3
- Beautify parameters help page.
- Enable multithreading for job construction and cache checking (set by proc.nthread).
- Uniform multiprocessing/threading.
- Fix Aggr delegate problems.
- Add ProcTree to manage process relations.
- Report processes will not run due to prior processes not ran.
- Add cclean option for enable/disable cleanup (output check/export) if a job is cached.
- Add tooltip for flowchart svg.
- Fix job id not saved for runner_sge.
- Fix resume assignment issue.
- Rewrite proc.log function so that logs from jobs do not mess up when they are multithreaded.
- Fix params loaded from file get overwriten.
- Add coverage report.

## Oct 23, 2017: 0.9.2
- Add profile for Proc so different procs can run with different profiles.
- Add delegate for Aggr.
- Add get, repCol, repRow, rowAt method for Channel.
- Dont't sleep for batch interval if jobs are cached or running.
- Add header argument for Channel.fromFile.
- Fix a bunch of bugs.

## Oct 6, 2017: 0.9.1
- Fix issues reported by codacy
- Fix an issue checking whether output files generated
- Deepcopy args and tplenvs when copy a process
- Refer relative path in p.script (with "file:" prefix) where p.script is defined
- Fix a utils.dictUpdate bug
- Template function 'Rlist' now can deal with python list
- Add log for export using move method (previously missed)
- Allow Aggr instance to set input directly
- Switch default showing of parameters loaded from object or file to False
- Optimize utils.varname
- Add warning if more input data columns than number of input keys
- Fix output channel key sequence does not keep
- Use object id instead of name as key for PyPPL nexts/paths in case tag is set in pipeline configrations

## Sept 22, 2017: 0.9.0
- Change class name with first letter capitalized
- Add resuming from processes (#20)
- Fix #19
- Group log configuration
- Make flowchart themeable and configurable
- Make attributes of `Proc` clearer
- Add tutorials
- Make tests more robust
- Enhancer templating, support Jinja2
- Set attributes of processes in aggregation with `set`

## Aug 4, 2017: 0.8.1
- Add partial echo
- Add interactive log from the script
- Add partial export
- Add log themes and filters
- Add compare to command line tool
- Fix bugs

## Aug 1, 2017: 0.8.0
- Add slurm and dry runner
- Fix bugs when more than 2 input files have same basename
- Add indent mark for script, specially useful for python
- Make stdout/stderr flushes out for instant runners when p.echo = True
- Add `sortby`, `reverse` for `channel.fromPath`
- Add command line argument parse
- Fix a bug that threads do not exit after process is done

## July 18, 2017: 0.7.4
- Docs updated (thanks @marchon for some grammer corrections)
- Some shortcut functions for placeholders added
- Check running during polling removed
- Logfile added
- `p.args['key']` can be set also by `p.args.key` now
- Bug fixes

## July 3, 2017: 0.7.3
- Config file defaults to `~/.pyppl.json` (`~/.pyppl` also works)
- Callfront added
- Empty input allowed
- Same basename name allowed for input files of a job
- Description of a proc added
- Aggr Optimized
- Bug #9 Fixed
- Private key supported for ssh runner
- Feature #7 Implemented

## June 20, 2017: 0.7.2
- Optimize isRunning function (using specific job id)
- Support python3 now
- Test on OSX
- More debug information for caching
- Bug fixes

## June 15, 2017: 0.7.1
- Move pyppl-cli to bin/pyppl
- channel.collapse now return the most common directory of paths
- Report oringinal file of input and bring files
- Show number of omitted logs
- Bug fixes

## June 13, 2017: 0.7.0
- Add colored log
- Put jobs in different directories (files with same basename can be used as input files, otherwise it will be overwritten).
- Add configuration `checkrun` for `pyppl` allow `runner.isRunning` to be disabled (save resources on local machine).
- Add built-in functions for placeholders; lambda functions do not need to call (just define)
- File placeholders (.fn, .bn, .prefix, etc) removed, please use built-in functions instead.
- Add an alias `p.ppldir` for `p.tmpdir` to avoid confusion.
- Update command line tool accordingly
- Split base runner class into two.

## May 30, 2017: 0.6.2
- Update docs and fix compilation errors from gitbook
- Change pyppl.dot to pyppl.pyppl.dot; 
- Add channel.fromFile method; 
- Add aggr.addProc method; 
- Fix proc/aggr copy bugs; 
- Fix utils.varname bugs;
- Fix bugs: channel._tuplize does not change list to tuple any more.
- Add fold/unfold to channel; 
- cache job immediately after it's done; 
- remove proc in nexts of its depends when its depends are reset; 
- add dir for input files, prefix for output files;
- Fix utilfs.dirmtime if file not exists; 
- add pyppl-cli;
- Change rc code, make it consistent with real rc code.

## Apr 27, 2017: 0.6.1
- Overwrite input file if it exists and not the same file; 
- fix varname bug when there are dots in the function name;
- Add brings feature;
- Add features to README, and brings to docs

## Apr 26, 2017: 0.6.0
- Set job signature to False if any of the item is False (that means expected files not exists); - Do cache by job itself; 
- Make it possible to cache and export successful jobs even when some jobs failed
- Host docs in gitbook
- Init job with log func from proc; 
- Add docstring for API generation; 
- Redefine return code for outfile not generated; 
- Error ignore works now; 
- Rewrite runner_local so it fits other runners to extend;
- Fix proc depends on mixed list of procs and aggrs

## Apr 18, 2017: 0.5.0
- Fix local runner not waiting (continuiously submitting jobs);
- Add property alias for aggr; 
- Output cleared if job not cached
- Fix bugs when export if outfiles are links; 
- change default export method to move; 
- add id and tag to calculate suffix for proc; 
- add timer; 
- add isRunning for job so that even if the main thread quit, we can still retrieve the job status;

## Apr 13, 2017: 0.4.0
- Add files (array) support for input; 
- Recursive update for configuration;
- Add aggregations;
- Move functions to utils; 
- Separate run for runners to submit and wait;
- Add use job class for jobs in a proc; 
- Use "1,2 3,4" for channel.fromArgs for multi-width channels; 
- Add rbind, cbind, slice for channel; 
- Add alias for some proc properties; 
- Remove callfront for proc; 
- Add export cache mode; 
- Add gzip export support (#1); 
- Unify loggers; 
- Use job cache instead of proc cache so that a proc can be partly cached; 
- Rewrite buildInput and buildOutput; 
- Use job to construct runners;

## Mar 14, 2017: 0.2.0
- Basic functions

## Jan 27, 2017: Initiate 
