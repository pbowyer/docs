## `dolt init`

Create an empty Dolt data repository

### Synopsis



### Description

This command creates an empty Dolt data repository in the current directory.

Running dolt init in an already initialized directory will fail.


### Arguments and options

`--name`:
The name used in commits to this repo. If not provided will be taken from `user.name` in the global config.

`--email`:
The email address used. If not provided will be taken from `user.email` in the global config.

`--date`:
Specify the date used in the initial commit. If not specified the current system time is used.

`-b`, `--initial-branch`:
The branch name used to initialize this database. If not provided will be taken from `init.defaultbranch` in the global config. If unset, the default initialized branch will be named 'main'.



## `dolt status`

Show the working status

### Synopsis

```bash
dolt status 
```

### Description

Displays working tables that differ from the current HEAD commit, tables that differ from the staged tables, and tables that are in the working tree that are not tracked by dolt. The first are what you would commit by running `dolt commit`; the second and third are what you could commit by running `dolt add .` before running `dolt commit`.

### Arguments and options

No options for this command.

## `dolt add`

Add table contents to the list of staged tables

### Synopsis

```bash
dolt add [&lt;table&gt;...]
```

### Description


This command updates the list of tables using the current content found in the working root, to prepare the content staged for the next commit. It adds the current content of existing tables as a whole or remove tables that do not exist in the working root anymore.

This command can be performed multiple times before a commit. It only adds the content of the specified table(s) at the time the add command is run; if you want subsequent changes included in the next commit, then you must run dolt add again to add the new content to the index.

The dolt status command can be used to obtain a summary of which tables have changes that are staged for the next commit.

### Arguments and options

`<table>`: Working table(s) to add to the list tables staged to be committed. The abbreviation '.' can be used to add all tables.

`-A`, `--all`:
Stages any and all changes (adds, deletes, and modifications).



## `dolt diff`

Show changes between commits, commit and working tree, etc

### Synopsis

```bash
dolt diff [options] [&lt;commit&gt;] [&lt;tables&gt;...]
dolt diff [options] &lt;commit&gt; &lt;commit&gt; [&lt;tables&gt;...]
```

### Description


Show changes between the working and staged tables, changes between the working tables and the tables within a commit, or changes between tables at two commits.

`dolt diff [--options] [<tables>...]`
   This form is to view the changes you made relative to the staging area for the next commit. In other words, the differences are what you could tell Dolt to further add but you still haven't. You can stage these changes by using dolt add.

`dolt diff [--options] <commit> [<tables>...]`
   This form is to view the changes you have in your working tables relative to the named `<commit>`. You can use HEAD to compare it with the latest commit, or a branch name to compare with the tip of a different branch.

`dolt diff [--options] <commit> <commit> [<tables>...]`
   This is to view the changes between two arbitrary `commit`.

The diffs displayed can be limited to show the first N by providing the parameter `--limit N` where `N` is the number of diffs to display.

In order to filter which diffs are displayed `--where key=value` can be used.  The key in this case would be either `to_COLUMN_NAME` or `from_COLUMN_NAME`. where `from_COLUMN_NAME=value` would filter based on the original value and `to_COLUMN_NAME` would select based on its updated value.


### Arguments and options

`-d`, `--data`:
Show only the data changes, do not show the schema changes (Both shown by default).

`-s`, `--schema`:
Show only the schema changes, do not show the data changes (Both shown by default).

`--summary`:
Show summary of data changes

`-r`, `--result-format`:
How to format diff output. Valid values are tabular & sql. Defaults to tabular. 

`--where`:
filters columns based on values in the diff.  See `dolt diff --help` for details.

`--limit`:
limits to the first N diffs.

`-c`, `--cached`:
Show only the unstaged data changes.



## `dolt reset`

Resets staged tables to their HEAD state

### Synopsis

```bash
dolt reset &lt;tables&gt;...
dolt reset [--hard | --soft]
```

### Description

Sets the state of a table in the staging area to be that table's value at HEAD

`dolt reset <tables>...`

This form resets the values for all staged `<tables>` to their values at `HEAD`. It does not affect the working tree or the current branch.

This means that `dolt reset <tables>` is the opposite of `dolt add <tables>`.

After running `dolt reset <tables>` to update the staged tables, you can use `dolt checkout` to check the contents out of the staged tables to the working tables.

`dolt reset .`

This form resets `all` staged tables to their values at HEAD. It is the opposite of `dolt add .`

### Arguments and options

`--hard`:
Resets the working tables and staged tables. Any changes to tracked tables in the working tree since `<commit>` are discarded.

`--soft`:
Does not touch the working tables, but removes all tables staged to be committed.



## `dolt commit`

Record changes to the repository

### Synopsis

```bash
dolt commit [options]
```

### Description


	Stores the current contents of the staged tables in a new commit along with a log message from the user describing the changes.
	
	The content to be added can be specified by using dolt add to incrementally \"add\" changes to the staged tables before using the commit command (Note: even modified files must be \"added\").
	
	The log message can be added with the parameter `-m <msg>`.  If the `<-m>` parameter is not provided an editor will be opened where you can review the commit and provide a log message.
	
	The commit timestamp can be modified using the --date parameter.  Dates can be specified in the formats `<YYYY-MM-DD>`, `<YYYY-MM-DDTHH:MM:SS>`, or `<YYYY-MM-DDTHH:MM:SSZ07:00>` (where `<07:00>` is the time zone offset)."
	

### Arguments and options

`-m`, `--message`:
Use the given `<msg>` as the commit message.

`--allow-empty`:
Allow recording a commit that has the exact same data as its sole parent. This is usually a mistake, so it is disabled by default. This option bypasses that safety.

`--date`:
Specify the date used in the commit. If not specified the current system time is used.

`-f`, `--force`:
Ignores any foreign key warnings and proceeds with the commit.

`--author`:
Specify an explicit author using the standard A U Thor <author@example.com> format.

`-a`, `--all`:
Adds all edited files in working to staged.



## `dolt sql`

Runs a SQL query

### Synopsis

```bash
dolt sql [--multi-db-dir &lt;directory&gt;] [-r &lt;result format&gt;]
dolt sql -q &lt;query;query&gt; [-r &lt;result format&gt;] -s &lt;name&gt; -m &lt;message&gt; [-b] [&lt;commit&gt;]
dolt sql -q &lt;query;query&gt; --multi-db-dir &lt;directory&gt; [-r &lt;result format&gt;] [-b]
dolt sql -x &lt;name&gt; [&lt;commit&gt;]
dolt sql --list-saved
```

### Description

Runs a SQL query you specify. With no arguments, begins an interactive shell to run queries and view the results. With the `-q` option, runs the given query and prints any results, then exits. If a commit is specified then only read queries are supported, and will run against the data at the specified commit.

By default, `-q` executes a single statement. To execute multiple SQL statements separated by semicolons, use `-b` to enable batch mode. Queries can be saved with `-s`. Alternatively `-x` can be used to execute a saved query by name. Pipe SQL statements to dolt sql (no `-q`) to execute a SQL import or update script. 

By default this command uses the dolt data repository in the current working directory as the one and only database. Running with `--multi-db-dir <directory>` uses each of the subdirectories of the supplied directory (each subdirectory must be a valid dolt data repository) as databases. Subdirectories starting with '.' are ignored.

### Arguments and options

`<commit>`: Commit to run read only queries against.

`-q`, `--query`:
Runs a single query and exits

`-r`, `--result-format`:
How to format result output. Valid values are tabular, csv, json. Defaults to tabular. 

`-s`, `--save`:
Used with --query, save the query to the query catalog with the name provided. Saved queries can be examined in the dolt_query_catalog system table.

`-x`, `--execute`:
Executes a saved query with the given name

`-l`, `--list-saved`:
Lists all saved queries

`-m`, `--message`:
Used with --query and --save, saves the query with the descriptive message given. See also --name

`-b`, `--batch`:
batch mode, to run more than one query with --query, separated by ';'. Piping input to sql with no arguments also uses batch mode

`--disable-batch`:
When issuing multiple statements, used to override more efficient batch processing to give finer control over session

`--multi-db-dir`:
Defines a directory whose subdirectories should all be dolt data repositories accessible as independent databases within 

`-c`, `--continue`:
continue running queries on an error. Used for batch mode only.



## `dolt sql-server`

Start a MySQL-compatible server.

### Synopsis

```bash
dolt sql-server --config &lt;file&gt;
dolt sql-server [-H &lt;host&gt;] [-P &lt;port&gt;] [-u &lt;user&gt;] [-p &lt;password&gt;] [-t &lt;timeout&gt;] [-l &lt;loglevel&gt;] [--multi-db-dir &lt;directory&gt;] [--query-parallelism &lt;num-go-routines&gt;] [-r]
```

### Description

By default, starts a MySQL-compatible server on the dolt database in the current directory. Databases are named after the directories they appear in, with all non-alphanumeric characters replaced by the _ character. Parameters can be specified using a yaml configuration file passed to the server via `--config <file>`, or by using the supported switches and flags to configure the server directly on the command line. If `--config <file>` is provided all other command line arguments are ignored.

This is an example yaml configuration file showing all supported items and their default values:

	log_level: info
	
	behavior:
	  read_only: false
	  autocommit: true
	
	user:
	  name: root
	  password: ""
	
	listener:
	  host: localhost
	  port: 3306
	  max_connections: 100
	  read_timeout_millis: 28800000
	  write_timeout_millis: 28800000
	  tls_key: null
	  tls_cert: null
	  require_secure_transport: null
	
	databases: []
	
	performance:
	  query_parallelism: null



SUPPORTED CONFIG FILE FIELDS:

		`vlog_level` - Level of logging provided. Options are: `trace`, `debug`, `info`, `warning`, `error`, and `fatal`.

		`behavior.read_only` - If true database modification is disabled

		`behavior.autocommit` - If true write queries will automatically alter the working set. When working with autocommit enabled it is highly recommended that listener.max_connections be set to 1 as concurrency issues will arise otherwise

		`user.name` - The username that connections should use for authentication

		`user.password` - The password that connections should use for authentication.

		`listener.host` - The host address that the server will run on.  This may be `localhost` or an IPv4 or IPv6 address

		`listener.port` - The port that the server should listen on

		`listener.max_connections` - The number of simultaneous connections that the server will accept

		`listener.read_timeout_millis` - The number of milliseconds that the server will wait for a read operation

		`listener.write_timeout_millis` - The number of milliseconds that the server will wait for a write operation

		`performance.query_parallelism` - Amount of go routines spawned to process each query

		`databases` - a list of dolt data repositories to make available as SQL databases. If databases is missing or empty then the working directory must be a valid dolt data repository which will be made available as a SQL database
		
		`databases[i].path` - A path to a dolt data repository
		
		`databases[i].name` - The name that the database corresponding to the given path should be referenced via SQL

If a config file is not provided many of these settings may be configured on the command line.

### Arguments and options

`--config`:
When provided configuration is taken from the yaml config file and all command line parameters are ignored.

`-H`, `--host`:
Defines the host address that the server will run on (default `localhost`)

`-P`, `--port`:
Defines the port that the server will run on (default `3306`)

`-u`, `--user`:
Defines the server user (default `root`)

`-p`, `--password`:
Defines the server password (default ``)

`-t`, `--timeout`:
Defines the timeout, in seconds, used for connections
A value of `0` represents an infinite timeout (default `28800000`)

`-r`, `--readonly`:
Disables modification of the database

`-l`, `--loglevel`:
Defines the level of logging provided
Options are: `trace', `debug`, `info`, `warning`, `error`, `fatal` (default `info`)

`--multi-db-dir`:
Defines a directory whose subdirectories should all be dolt data repositories accessible as independent databases.

`--no-auto-commit`:
When provided sessions will not automatically commit their changes to the working set. Anything not manually committed will be lost.

`--query-parallelism`:
Set the number of go routines spawned to handle each query (default `2`)

`--max-connections`:
Set the number of connections handled by the server (default `100`)



## `dolt sql-client`

Starts a built-in MySQL client.

### Synopsis

```bash
dolt sql-client [-d] --config &lt;file&gt;
dolt sql-client [-d] [-H &lt;host&gt;] [-P &lt;port&gt;] [-u &lt;user&gt;] [-p &lt;password&gt;] [-t &lt;timeout&gt;] [-l &lt;loglevel&gt;] [--multi-db-dir &lt;directory&gt;] [--query-parallelism &lt;num-go-routines&gt;] [-r]
```

### Description

Starts a MySQL client that is built into dolt. May connect to any database that supports MySQL connections, including dolt servers.

You may also start a dolt server and automatically connect to it using this client. Both the server and client will be a part of the same process. This is useful for testing behavior of the dolt server without the need for an external client, and is not recommended for general usage.

Similar to `dolt sql-server`, this command may use a YAML configuration file or command line arguments. For more information on the YAML file, refer to the documentation on `dolt sql-server`.

### Arguments and options

`--config`:
When provided configuration is taken from the yaml config file and all command line parameters are ignored.

`-H`, `--host`:
Defines the host address that the server will run on (default `localhost`)

`-P`, `--port`:
Defines the port that the server will run on (default `3306`)

`-u`, `--user`:
Defines the server user (default `root`)

`-p`, `--password`:
Defines the server password (default ``)

`-t`, `--timeout`:
Defines the timeout, in seconds, used for connections
A value of `0` represents an infinite timeout (default `28800000`)

`-r`, `--readonly`:
Disables modification of the database

`-l`, `--loglevel`:
Defines the level of logging provided
Options are: `trace', `debug`, `info`, `warning`, `error`, `fatal` (default `info`)

`--multi-db-dir`:
Defines a directory whose subdirectories should all be dolt data repositories accessible as independent databases.

`--no-auto-commit`:
When provided sessions will not automatically commit their changes to the working set. Anything not manually committed will be lost.

`--query-parallelism`:
Set the number of go routines spawned to handle each query (default `2`)

`--max-connections`:
Set the number of connections handled by the server (default `100`)

`-d`, `--dual`:
Causes this command to spawn a dolt server that is automatically connected to.



## `dolt log`

Show commit logs

### Synopsis

```bash
dolt log [-n &lt;num_commits&gt;] [&lt;commit&gt;] [[--] &lt;table&gt;]
```

### Description

Shows the commit logs

The command takes options to control what is shown and how.

### Arguments and options

`-n`, `--number`:
Limit the number of commits to output.

`--min-parents`:
The minimum number of parents a commit must have to be included in the log.

`--merges`:
Equivalent to min-parents == 2, this will limit the log to commits with 2 or more parents.

`--parents`:
Shows all parents of each commit in the log.



## `dolt branch`

List, create, or delete branches

### Synopsis

```bash
dolt branch [--list] [-v] [-a] [-r]
dolt branch [-f] &lt;branchname&gt; [&lt;start-point&gt;]
dolt branch -m [-f] [&lt;oldbranch&gt;] &lt;newbranch&gt;
dolt branch -c [-f] [&lt;oldbranch&gt;] &lt;newbranch&gt;
dolt branch -d [-f] [-r] &lt;branchname&gt;...
```

### Description

If `--list` is given, or if there are no non-option arguments, existing branches are listed. The current branch will be highlighted with an asterisk. With no options, only local branches are listed. With `-r`, only remote branches are listed. With `-a` both local and remote branches are listed. `-v` causes the hash of the commit that the branches are at to be printed as well.

The command's second form creates a new branch head named `<branchname>` which points to the current `HEAD`, or `<start-point>` if given.

Note that this will create the new branch, but it will not switch the working tree to it; use `dolt checkout <newbranch>` to switch to the new branch.

With a `-m`, `<oldbranch>` will be renamed to `<newbranch>`. If `<newbranch>` exists, -f must be used to force the rename to happen.

The `-c` options have the exact same semantics as `-m`, except instead of the branch being renamed it will be copied to a new name.

With a `-d`, `<branchname>` will be deleted. You may specify more than one branch for deletion.

### Arguments and options

`<start-point>`: A commit that a new branch should point at.

`--list`:
List branches

`-f`, `--force`:
Reset `<branchname>` to `<startpoint>`, even if `<branchname>` exists already. Without `-f`, `dolt branch` refuses to change an existing branch. In combination with `-d` (or `--delete`), allow deleting the branch irrespective of its merged status. In combination with -m (or `--move`), allow renaming the branch even if the new branch name already exists, the same applies for `-c` (or `--copy`).

`-c`, `--copy`:
Create a copy of a branch.

`-m`, `--move`:
Move/rename a branch

`-d`, `--delete`:
Delete a branch. The branch must be fully merged in its upstream branch.

`--D`:
Shortcut for `--delete --force`.

`-v`, `--verbose`:
When in list mode, show the hash and commit subject line for each head

`-a`, `--all`:
When in list mode, shows remote tracked branches

`-r`, `--remote`:
When in list mode, show only remote tracked branches. When with -d, delete a remote tracking branch.

`--show-current`:
Print the name of the current branch



## `dolt checkout`

Switch branches or restore working tree tables

### Synopsis

```bash
dolt checkout &lt;branch&gt;
dolt checkout &lt;table&gt;...
dolt checkout -b &lt;new-branch&gt; [&lt;start-point&gt;]
```

### Description


Updates tables in the working set to match the staged versions. If no paths are given, dolt checkout will also update HEAD to set the specified branch as the current branch.

dolt checkout `<branch>`
   To prepare for working on `<branch>`, switch to it by updating the index and the tables in the working tree, and by pointing HEAD at the branch. Local modifications to the tables in the working
   tree are kept, so that they can be committed to the `<branch>`.

dolt checkout -b `<new_branch>` [`<start_point>`]
   Specifying -b causes a new branch to be created as if dolt branch were called and then checked out.

dolt checkout `<table>`...
  To update table(s) with their values in HEAD 

### Arguments and options

`--b`:
Create a new branch named `<new_branch>` and start it at `<start_point>`.



## `dolt merge`

Join two or more development histories together

### Synopsis

```bash
dolt merge [--squash] &lt;branch&gt;
dolt merge --no-ff [-m message] &lt;branch&gt;
dolt merge --abort
```

### Description

Incorporates changes from the named commits (since the time their histories diverged from the current branch) into the current branch.

The second syntax (`<dolt merge --abort>`) can only be run after the merge has resulted in conflicts. dolt merge `--abort` will abort the merge process and try to reconstruct the pre-merge state. However, if there were uncommitted changes when the merge started (and especially if those changes were further modified after the merge was started), dolt merge `--abort` will in some cases be unable to reconstruct the original (pre-merge) changes. Therefore: 

`<Warning>`: Running dolt merge with non-trivial uncommitted changes is discouraged: while possible, it may leave you in a state that is hard to back out of in the case of a conflict.


### Arguments and options

`--no-ff`:
Create a merge commit even when the merge resolves as a fast-forward.

`--squash`:
Merges changes to the working set without updating the commit history

`-m`, `--message`:
Use the given `<msg>` as the commit message.

`--abort`:
Abort the current conflict resolution process, and try to reconstruct the pre-merge state.

If there were uncommitted working set changes present when the merge started, `dolt merge --abort` will be unable to reconstruct these changes. It is therefore recommended to always commit or stash your changes before running dolt merge.




## `dolt conflicts cat`

print conflicts

### Synopsis

```bash
dolt conflicts cat [&lt;commit&gt;] &lt;table&gt;...
```

### Description

The dolt conflicts cat command reads table conflicts and writes them to the standard output.

### Arguments and options

`<table>`: List of tables to be printed. '.' can be used to print conflicts for all tables.



## `dolt conflicts resolve`

Removes rows from list of conflicts

### Synopsis

```bash
dolt conflicts resolve &lt;table&gt; [&lt;key_definition&gt;] &lt;key&gt;...
dolt conflicts resolve --ours|--theirs &lt;table&gt;...
```

### Description


When a merge operation finds conflicting changes, the rows with the conflicts are added to list of conflicts that must be resolved.  Once the value for the row is resolved in the working set of tables, then the conflict should be resolved.
		
In its first form `dolt conflicts resolve <table> <key>...`, resolve runs in manual merge mode resolving the conflicts whose keys are provided.

In its second form `dolt conflicts resolve --ours|--theirs <table>...`, resolve runs in auto resolve mode. Where conflicts are resolved using a rule to determine which version of a row should be used.


### Arguments and options

`<table>`: List of tables to be printed. When in auto-resolve mode, '.' can be used to resolve all tables.

`<key>`: key(s) of rows within a table whose conflicts have been resolved

`--ours`:
For all conflicts, take the version from our branch and resolve the conflict

`--theirs`:
For all conflicts, take the version from their branch and resolve the conflict



## `dolt revert`

Undo the changes introduced in a commit

### Synopsis

```bash
dolt revert <revision>...
```

### Description

Removes the changes made in a commit (or series of commits) from the working set, and then automatically commits the
result. This is done by way of a three-way merge. Given a specific commit (e.g. HEAD~1), this is similar to applying the
patch from HEAD~1..HEAD~2, giving us a patch of what to remove to effectively remove the influence of the specified
commit. If multiple commits are specified, then this process is repeated for each commit in the order specified. This
requires a clean working set.

For now, any conflicts or constraint violations that are brought by the merge cause the command to fail.

### Arguments and options

`<revision>`: The commit revisions. If multiple revisions are given, they're applied in the order given.

`--author`:
Specify an explicit author using the standard A U Thor <author@example.com> format.



## `dolt clone`

Clone a data repository into a new directory

### Synopsis

```bash
dolt clone [-remote &lt;remote&gt;] [-branch &lt;branch&gt;]  [--aws-region &lt;region&gt;] [--aws-creds-type &lt;creds-type&gt;] [--aws-creds-file &lt;file&gt;] [--aws-creds-profile &lt;profile&gt;] &lt;remote-url&gt; &lt;new-dir&gt;
```

### Description

Clones a repository into a newly created directory, creates remote-tracking branches for each branch in the cloned repository (visible using `<dolt branch -a>`), and creates and checks out an initial branch that is forked from the cloned repository's currently active branch.

After the clone, a plain `dolt fetch` without arguments will update all the remote-tracking branches, and a `dolt pull` without arguments will in addition merge the remote branch into the current branch.

This default configuration is achieved by creating references to the remote branch heads under `<refs/remotes/origin>`  and by creating a remote named 'origin'.


### Arguments and options

`--remote`:
Name of the remote to be added. Default will be 'origin'.

`-b`, `--branch`:
The branch to be cloned.  If not specified all branches will be cloned.

`--aws-region`

`--aws-creds-type`

`--aws-creds-file`:
AWS credentials file.

`--aws-creds-profile`:
AWS profile to use.



## `dolt fetch`

Download objects and refs from another repository

### Synopsis

```bash
dolt fetch [&lt;remote&gt;] [&lt;refspec&gt; ...]
```

### Description

Fetch refs, along with the objects necessary to complete their histories and update remote-tracking branches.

By default dolt will attempt to fetch from a remote named `origin`.  The `<remote>` parameter allows you to specify the name of a different remote you wish to pull from by the remote's name.

When no refspec(s) are specified on the command line, the fetch_specs for the default remote are used.


### Arguments and options

`-f`, `--force`:
Update refs to remote branches with the current state of the remote, overwriting any conflicting history.



## `dolt pull`

Fetch from and integrate with another repository or a local branch

### Synopsis

```bash
dolt pull &lt;remote&gt;
```

### Description

Incorporates changes from a remote repository into the current branch. In its default mode, `dolt pull` is shorthand for `dolt fetch` followed by `dolt merge <remote>/<branch>`.

More precisely, dolt pull runs `dolt fetch` with the given parameters and calls `dolt merge` to merge the retrieved branch `HEAD` into the current branch.


### Arguments and options

`--squash`:
Merges changes to the working set without updating the commit history

`--no-ff`:
Create a merge commit even when the merge resolves as a fast-forward.

`-f`, `--force`:
Ignores any foreign key warnings and proceeds with the commit.



## `dolt push`

Update remote refs along with associated objects

### Synopsis

```bash
dolt push [-u | --set-upstream] [&lt;remote&gt;] [&lt;refspec&gt;]
```

### Description

Updates remote refs using local refs, while sending objects necessary to complete the given refs.

When the command line does not specify where to push with the `<remote>` argument, an attempt is made to infer the remote.  If only one remote exists it will be used, if multiple remotes exists, a remote named 'origin' will be attempted.  If there is more than one remote, and none of them are named 'origin' then the command will fail and you will need to specify the correct remote explicitly.

When the command line does not specify what to push with `<refspec>`... then the current branch will be used.

When neither the command-line does not specify what to push, the default behavior is used, which corresponds to the current branch being pushed to the corresponding upstream branch, but as a safety measure, the push is aborted if the upstream branch does not have the same name as the local one.


### Arguments and options

`-u`, `--set-upstream`:
For every branch that is up to date or successfully pushed, add upstream (tracking) reference, used by argument-less `dolt pull` and other commands.

`-f`, `--force`:
Update the remote with local history, overwriting any conflicting history in the remote.



## `dolt config`

Get and set repository or global options

### Synopsis

```bash
dolt config [--global|--local] --list
dolt config [--global|--local] --add &lt;name&gt; &lt;value&gt;
dolt config [--global|--local] --get &lt;name&gt;
dolt config [--global|--local] --unset &lt;name&gt;...
```

### Description

You can query/set/replace/unset options with this command.
		
	When reading, the values are read from the global and repository local configuration files, and options `<--global>`, and `<--local>` can be used to tell the command to read from only that location.
	
	When writing, the new value is written to the repository local configuration file by default, and options `<--global>`, can be used to tell the command to write to that location (you can say `<--local>` but that is the default).


### Arguments and options

`--global`:
Use global config.

`--local`:
Use repository local config.

`--add`:
Set the value of one or more config parameters

`--list`:
List the values of all config parameters.

`--get`:
Get the value of one or more config parameters.

`--unset`:
Unset the value of one or more config parameters.



## `dolt remote`

Manage set of tracked repositories

### Synopsis

```bash
dolt remote [-v | --verbose]
dolt remote add [--aws-region &lt;region&gt;] [--aws-creds-type &lt;creds-type&gt;] [--aws-creds-file &lt;file&gt;] [--aws-creds-profile &lt;profile&gt;] &lt;name&gt; &lt;url&gt;
dolt remote remove &lt;name&gt;
```

### Description

With no arguments, shows a list of existing remotes. Several subcommands are available to perform operations on the remotes.

`add`
Adds a remote named `<name>` for the repository at `<url>`. The command dolt fetch `<name>` can then be used to create and update remote-tracking branches `<name>/<branch>`.

The `<url>` parameter supports url schemes of http, https, aws, gs, and file. The url prefix defaults to https. If the `<url>` parameter is in the format `<organization>/<repository>` then dolt will use the `remotes.default_host` from your configuration file (Which will be dolthub.com unless changed).

AWS cloud remote urls should be of the form `aws://[dynamo-table:s3-bucket]/database`.  You may configure your aws cloud remote using the optional parameters `aws-region`, `aws-creds-type`, `aws-creds-file`.

aws-creds-type specifies the means by which credentials should be retrieved in order to access the specified cloud resources (specifically the dynamo table, and the s3 bucket). Valid values are 'role', 'env', or 'file'.

	role: Use the credentials installed for the current user
	env: Looks for environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
	file: Uses the credentials file specified by the parameter aws-creds-file
	
GCP remote urls should be of the form gs://gcs-bucket/database and will use the credentials setup using the gcloud command line available from Google.

The local filesystem can be used as a remote by providing a repository url in the format file://absolute path. See https://en.wikipedia.org/wiki/File_URI_scheme

`remove`, `rm`
Remove the remote named `<name>`. All remote-tracking branches and configuration settings for the remote are removed.

### Arguments and options

`<region>`: cloud provider region associated with this remote.

`<creds-type>`: credential type.  Valid options are role, env, and file.  See the help section for additional details.

`<profile>`: AWS profile to use.

`-v`, `--verbose`:
When printing the list of remotes adds additional details.

`--aws-region`

`--aws-creds-type`

`--aws-creds-file`:
AWS credentials file

`--aws-creds-profile`:
AWS profile to use



## `dolt backup`

Manage server backups

### Synopsis

```bash
dolt backup [-v | --verbose]
dolt backup add [--aws-region &lt;region&gt;] [--aws-creds-type &lt;creds-type&gt;] [--aws-creds-file &lt;file&gt;] [--aws-creds-profile &lt;profile&gt;] &lt;name&gt; &lt;url&gt;
dolt backup remove &lt;name&gt;
dolt backup restore &lt;url&gt; &lt;name&gt;
dolt backup sync &lt;name&gt;
```

### Description

With no arguments, shows a list of existing backups. Several subcommands are available to perform operations on backups, point in time snapshots of a database's contents.

`add`
Adds a backup named `<name>` for the database at `<url>`.
The `<url>` parameter supports url schemes of http, https, aws, gs, and file. The url prefix defaults to https. If the `<url>` parameter is in the format `<organization>/<repository>` then dolt will use the `backups.default_host` from your configuration file (Which will be dolthub.com unless changed).
The URL address must be unique to existing remotes and backups.

AWS cloud backup urls should be of the form `aws://[dynamo-table:s3-bucket]/database`. You may configure your aws cloud backup using the optional parameters `aws-region`, `aws-creds-type`, `aws-creds-file`.

aws-creds-type specifies the means by which credentials should be retrieved in order to access the specified cloud resources (specifically the dynamo table, and the s3 bucket). Valid values are 'role', 'env', or 'file'.

	role: Use the credentials installed for the current user
	env: Looks for environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
	file: Uses the credentials file specified by the parameter aws-creds-file
	
GCP backup urls should be of the form gs://gcs-bucket/database and will use the credentials setup using the gcloud command line available from Google.

The local filesystem can be used as a backup by providing a repository url in the format file://absolute path. See https://en.wikipedia.org/wiki/File_URI_scheme

`remove`, `rm`
Remove the backup named `<name>`. All configuration settings for the backup are removed. The contents of the backup are not affected.

`restore`
Restore a Dolt database from a given `<url>` into a specified directory `<url>`.

`sync`
Snapshot the database and upload to the backup `<name>`. This includes branches, tags, working sets, and remote tracking refs.

### Arguments and options

`<region>`: cloud provider region associated with this backup.

`<creds-type>`: credential type.  Valid options are role, env, and file.  See the help section for additional details.

`<profile>`: AWS profile to use.

`-v`, `--verbose`:
When printing the list of backups adds additional details.

`--aws-region`

`--aws-creds-type`

`--aws-creds-file`:
AWS credentials file

`--aws-creds-profile`:
AWS profile to use



## `dolt login`

Login to DoltHub

### Synopsis

```bash
dolt login [&lt;creds&gt;]
```

### Description

Login into DoltHub using the email in your config so you can pull from private repos and push to those you have permission to.


### Arguments and options

`<creds>`: A specific credential to use for login.



## `dolt creds new`

Create a new public/private keypair for authenticating with doltremoteapi.

### Synopsis



### Description

Creates a new keypair for authenticating with doltremoteapi.

Prints the public portion of the keypair, which can entered into the credentials settings page of dolthub.

### Arguments and options

No options for this command.

## `dolt creds rm`

Remove a stored public/private keypair.

### Synopsis

```bash
dolt creds rm &lt;public_key_as_appears_in_ls&gt;
```

### Description

Removes an existing keypair from dolt's credential storage.

### Arguments and options

No options for this command.

## `dolt creds ls`

List keypairs available for authenticating with doltremoteapi.

### Synopsis

```bash
dolt creds ls [-v | --verbose]
```

### Description

Lists known public keys from keypairs for authenticating with doltremoteapi.

The currently selected keypair appears with a `*` next to it.

### Arguments and options

`-v`, `--verbose`:
Verbose output, including key id.



## `dolt creds check`

Check authenticating with a credential keypair against a doltremoteapi.

### Synopsis

```bash
dolt creds check [--endpoint doltremoteapi.dolthub.com:443] [--creds &lt;eak95022q3vskvumn2fcrpibdnheq1dtr8t...&gt;]
```

### Description

Tests calling a doltremoteapi with dolt credentials and reports the authentication result.

### Arguments and options

`--endpoint`:
API endpoint, otherwise taken from config.

`--creds`:
Public Key ID or Public Key for credentials, otherwise taken from config.



## `dolt creds use`

Select an existing dolt credential for authenticating with doltremoteapi.

### Synopsis

```bash
dolt creds use &lt;public_key_as_appears_in_ls | public_key_id_as_appears_in_ls&gt;
```

### Description

Selects an existing dolt credential for authenticating with doltremoteapi.

Can be given a credential's public key or key id and will update global dolt
config to use the credential when interacting with doltremoteapi.

You can see your available credentials with 'dolt creds ls'.

### Arguments and options

No options for this command.

## `dolt creds import`

Import a dolt credential from an existing .jwk file.

### Synopsis

```bash
dolt creds import [--no-profile] [&lt;jwk_filename&gt;]
```

### Description

Imports a dolt credential from an existing .jwk file.

Dolt credentials are stored in the creds subdirectory of the global dolt config
directory as files with one key per file in JWK format. This command can import
a JWK from a file or stdin and places the imported key in the correct place for
dolt to find it as a valid credential.

This command will set the newly imported credential as the used credential if
there are currently not credentials. If this command does use the new
credential, it will call doltremoteapi to update user.name and user.email with
information from the remote user profile if those fields are not already
available in the local dolt config.

### Arguments and options

`<jwk_filename>`: The JWK file. If omitted, import operates on stdin.

`--no-profile`:
If provided, no attempt will be made to contact doltremoteapi and update user.name and user.email.



## `dolt ls`

List tables

### Synopsis

```bash
dolt ls [--options] [&lt;commit&gt;]
```

### Description

With no arguments lists the tables in the current working set but if a commit is specified it will list the tables in that commit.  If the `--verbose` flag is provided a row count and a hash of the table will also be displayed.

If the `--system` flag is supplied this will show the dolt system tables which are queryable with SQL.  Some system tables can be queried even if they are not in the working set by specifying appropriate parameters in the SQL queries. To see these tables too you may pass the `--verbose` flag.

If the `--all` flag is supplied both user and system tables will be printed.


### Arguments and options

`-v`, `--verbose`:
show the hash of the table

`-s`, `--system`:
show system tables

`-a`, `--all`:
show system tables



## `dolt schema export`

Exports a table's schema.

### Synopsis

```bash
dolt schema export &lt;table&gt; &lt;file&gt;
```

### Description



### Arguments and options

`<table>`: table whose schema is being exported.

`<file>`: the file that the schema will be written to.



## `dolt schema import`

Creates a new table with an inferred schema.

### Synopsis

```bash
dolt schema import [--create|--replace] [--force] [--dry-run] [--lower|--upper] [--keep-types] [--file-type <type>] [--float-threshold] [--map &lt;mapping-file&gt;] [--delim &lt;delimiter&gt;]--pks &lt;field&gt;,... &lt;table&gt; &lt;file&gt;
```

### Description

If `--create | -c` is given the operation will create `<table>` with a schema that it infers from the supplied file. One or more primary key columns must be specified using the `--pks` parameter.

If `--update | -u` is given the operation will update `<table>` any additional columns, or change the types of columns based on the file supplied.  If the `--keep-types` parameter is supplied then the types for existing columns will not be modified, even if they differ from what is in the supplied file.

If `--replace | -r` is given the operation will replace `<table>` with a new, empty table which has a schema inferred from the supplied file but columns tags will be maintained across schemas.  `--keep-types` can also be supplied here to guarantee that types are the same in the file and in the pre-existing table.

A mapping file can be used to map fields between the file being imported and the table's schema being inferred.  This can be used when creating a new table, or updating or replacing an existing table.

A mapping file is json in the format:

	{
		"source_field_name":"dest_field_name"
		...
	}

where source_field_name is the name of a field in the file being imported and dest_field_name is the name of a field in the table being imported to.


In create, update, and replace scenarios the file's extension is used to infer the type of the file.  If a file does not have the expected extension then the `--file-type` parameter should be used to explicitly define the format of the file in one of the supported formats (Currently only csv is supported).  For files separated by a delimiter other than a ',', the --delim parameter can be used to specify a delimiter.

If the parameter `--dry-run` is supplied a sql statement will be generated showing what would be executed if this were run without the --dry-run flag

`--float-threshold` is the threshold at which a string representing a floating point number should be interpreted as a float versus an int.  If FloatThreshold is 0.0 then any number with a decimal point will be interpreted as a float (such as 0.0, 1.0, etc).  If FloatThreshold is 1.0 then any number with a decimal point will be converted to an int (0.5 will be the int 0, 1.99 will be the int 1, etc.  If the FloatThreshold is 0.001 then numbers with a fractional component greater than or equal to 0.001 will be treated as a float (1.0 would be an int, 1.0009 would be an int, 1.001 would be a float, 1.1 would be a float, etc)


### Arguments and options

`<table>`: Name of the table to be created.

`<file>`: The file being used to infer the schema.

`-c`, `--create`:
Create a table with the schema inferred from the `<file>` provided.

`-u`, `--update`:
Update a table to match the inferred schema of the `<file>` provided

`-r`, `--replace`:
Replace a table with a new schema that has the inferred schema from the `<file>` provided. All previous data will be lost.

`--dry-run`:
Print the sql statement that would be run if executed without the flag.

`--keep-types`:
When a column already exists in the table, and it's also in the `<file>` provided, use the type from the table.

`--file-type`:
Explicitly define the type of the file if it can't be inferred from the file extension.

`--pks`:
List of columns used as the primary key cols.  Order of the columns will determine sort order.

`-m`, `--map`:
A file that can map a column name in `<file>` to a new value.

`--float-threshold`:
Minimum value at which the fractional component of a value must exceed in order to be considered a float.

`--delim`:
Specify a delimiter for a csv style file with a non-comma delimiter.



## `dolt schema show`

Shows the schema of one or more tables.

### Synopsis

```bash
dolt schema show [&lt;commit&gt;] [&lt;table&gt;...]
```

### Description

`dolt schema show` displays the schema of tables at a given commit.  If no commit is provided the working set will be used.

A list of tables can optionally be provided.  If it is omitted all table schemas will be shown.

### Arguments and options

`<table>`: table(s) whose schema is being displayed.

`<commit>`: commit at which point the schema will be displayed.



## `dolt schema tags`

Shows the column tags of one or more tables.

### Synopsis

```bash
dolt schema tags [-r &lt;result format&gt;] [&lt;table&gt;...]
```

### Description

`dolt schema tags` displays the column tags of tables on the working set.

A list of tables can optionally be provided.  If it is omitted then all tables will be shown. If a given table does not exist, then it is ignored.

### Arguments and options

`<table>`: table(s) whose tags will be displayed.

`-r`, `--result-format`:
How to format result output. Valid values are tabular, csv, json. Defaults to tabular.



## `dolt table import`

Imports data into a dolt table

### Synopsis

```bash
dolt table import -c [-f] [--pk &lt;field&gt;] [--schema &lt;file&gt;] [--map &lt;file&gt;] [--continue] [--file-type &lt;type&gt;] &lt;table&gt; &lt;file&gt;
dolt table import -u [--map &lt;file&gt;] [--continue] [--file-type &lt;type&gt;] &lt;table&gt; &lt;file&gt;
dolt table import -r [--map &lt;file&gt;] [--file-type &lt;type&gt;] &lt;table&gt; &lt;file&gt;
```

### Description

If `--create-table | -c` is given the operation will create `<table>` and import the contents of file into it.  If a table already exists at this location then the operation will fail, unless the `--force | -f` flag is provided. The force flag forces the existing table to be overwritten.

The schema for the new table can be specified explicitly by providing a SQL schema definition file, or will be inferred from the imported file.  All schemas, inferred or explicitly defined must define a primary key.  If the file format being imported does not support defining a primary key, then the `--pk` parameter must supply the name of the field that should be used as the primary key.

If `--update-table | -u` is given the operation will update `<table>` with the contents of file. The table's existing schema will be used, and field names will be used to match file fields with table fields unless a mapping file is specified.

During import, if there is an error importing any row, the import will be aborted by default.  Use the `--continue` flag to continue importing when an error is encountered.

If `--replace-table | -r` is given the operation will replace `<table>` with the contents of the file. The table's existing schema will be used, and field names will be used to match file fields with table fields unless a mapping file is specified.

If the schema for the existing table does not match the schema for the new file, the import will be aborted by default. To overwrite both the table and the schema, use `-c -f`.

A mapping file can be used to map fields between the file being imported and the table being written to. This can be used when creating a new table, or updating or replacing an existing table.

A mapping file is json in the format:

	{
		"source_field_name":"dest_field_name"
		...
	}

where source_field_name is the name of a field in the file being imported and dest_field_name is the name of a field in the table being imported to.

In create, update, and replace scenarios the file's extension is used to infer the type of the file.  If a file does not have the expected extension then the `--file-type` parameter should be used to explicitly define the format of the file in one of the supported formats (csv, psv, json, xlsx).  For files separated by a delimiter other than a ',' (type csv) or a '|' (type psv), the --delim parameter can be used to specify a delimiter

### Arguments and options

`<table>`: The new or existing table being imported to.

`<file>`: The file being imported. Supported file types are csv, psv, and nbf.

`-c`, `--create-table`:
Create a new table, or overwrite an existing table (with the -f flag) from the imported data.

`-u`, `--update-table`:
Update an existing table with the imported data.

`-f`, `--force`:
If a create operation is being executed, data already exists in the destination, the force flag will allow the target to be overwritten.

`-r`, `--replace-table`:
Replace existing table with imported data while preserving the original schema.

`--continue`:
Continue importing when row import errors are encountered.

`-s`, `--schema`:
The schema for the output data.

`-m`, `--map`:
A file that lays out how fields should be mapped from input data to output data.

`-pk`, `--pk`:
Explicitly define the name of the field in the schema which should be used as the primary key.

`--file-type`:
Explicitly define the type of the file if it can't be inferred from the file extension.

`--delim`:
Specify a delimiter for a csv style file with a non-comma delimiter.



## `dolt table export`

Export the contents of a table to a file.

### Synopsis

```bash
dolt table export [-f] [-pk &lt;field&gt;] [-schema &lt;file&gt;] [-map &lt;file&gt;] [-continue] [-file-type &lt;type&gt;] &lt;table&gt; &lt;file&gt;
```

### Description

`dolt table export` will export the contents of `<table>` to `<|file>`

See the help for `dolt table import` as the options are the same.


### Arguments and options

`<table>`: The table being exported.

`<file>`: The file being output to.

`-f`, `--force`:
If data already exists in the destination, the force flag will allow the target to be overwritten.

`--continue`:
Continue exporting when row export errors are encountered.

`-s`, `--schema`:
The schema for the output data.

`-m`, `--map`:
A file that lays out how fields should be mapped from input data to output data.

`-pk`, `--pk`:
Explicitly define the name of the field in the schema which should be used as the primary key.

`--file-type`:
Explicitly define the type of the file if it can't be inferred from the file extension.



## `dolt table rm`

Removes table(s) from the working set of tables.

### Synopsis

```bash
dolt table rm &lt;table&gt;...
```

### Description

`dolt table rm` removes table(s) from the working set.  These changes can be staged using `dolt add` and committed using `dolt commit`

### Arguments and options

`<table>`: The table to remove



## `dolt table mv`

Renames a table

### Synopsis

```bash
dolt table mv [-f] &lt;oldtable` &lt;newtable`
```

### Description


The dolt table mv command will rename a table. If a table exists with the target name this command will 
fail unless the `--force|-f` flag is provided.  In that case the table at the target location will be overwritten 
by the table being renamed.

The result is equivalent of running `dolt table cp <old> <new>` followed by `dolt table rm <old>`, resulting 
in a new table and a deleted table in the working set. These changes can be staged using `dolt add` and committed
using `dolt commit`.

### Arguments and options

`<oldtable>`: The table being moved.

`<newtable>`: The new name of the table

`-f`, `--force`:
If data already exists in the destination, the force flag will allow the target to be overwritten.



## `dolt table cp`

Makes a copy of a table

### Synopsis

```bash
dolt table cp [-f] &lt;oldtable&gt; &lt;newtable&gt;
```

### Description

The dolt table cp command makes a copy of a table at a given commit. If a commit is not specified the copy is made of the table from the current working set.

If a table exists at the target location this command will fail unless the `--force|-f` flag is provided.  In this case the table at the target location will be overwritten with the copied table.

All changes will be applied to the working tables and will need to be staged using `dolt add` and committed using `dolt commit`.


### Arguments and options

`<oldtable>`: The table being copied.

`<newtable>`: The destination where the table is being copied to.

`-f`, `--force`:
If data already exists in the destination, the force flag will allow the target to be overwritten.



## `dolt tag`

List, create, or delete branches

### Synopsis

```bash
dolt tag [--list] [-v] [-a] [-r]
dolt tag [-f] &lt;branchname&gt; [&lt;start-point&gt;]
dolt tag -m [-f] [&lt;oldbranch&gt;] &lt;newbranch&gt;
dolt tag -c [-f] [&lt;oldbranch&gt;] &lt;newbranch&gt;
dolt tag -d [-f] [-r] &lt;branchname&gt;...
```

### Description

If `--list` is given, or if there are no non-option arguments, existing branches are listed. The current branch will be highlighted with an asterisk. With no options, only local branches are listed. With `-r`, only remote branches are listed. With `-a` both local and remote branches are listed. `-v` causes the hash of the commit that the branches are at to be printed as well.

The command's second form creates a new branch head named `<branchname>` which points to the current `HEAD`, or `<start-point>` if given.

Note that this will create the new branch, but it will not switch the working tree to it; use `dolt checkout <newbranch>` to switch to the new branch.

With a `-m`, `<oldbranch>` will be renamed to `<newbranch>`. If `<newbranch>` exists, -f must be used to force the rename to happen.

The `-c` options have the exact same semantics as `-m`, except instead of the branch being renamed it will be copied to a new name.

With a `-d`, `<branchname>` will be deleted. You may specify more than one branch for deletion.

### Arguments and options

`<ref>`: A commit ref that the tag should point at.

`-m`, `--message`:
Use the given `<msg>` as the tag message.

`-v`, `--verbose`:
list tags along with their metadata.

`-d`, `--delete`:
Delete a tag.



## `dolt blame`

Show what revision and author last modified each row of a table

### Synopsis

```bash
dolt blame [&lt;rev&gt;] &lt;tablename&gt;
```

### Description

Annotates each row in the given table with information from the revision which last modified the row. Optionally, start annotating from the given revision.

### Arguments and options

No options for this command.

## `dolt read-tables`

Fetch table(s) at a specific commit into a new dolt repo

### Synopsis

```bash
dolt read-tables [--dir <directory>] &lt;remote-url&gt; &lt;commit&gt; [&lt;table&gt;...]
```

### Description

A shallow clone operation will retrieve the state of table(s) from a remote repository at a given commit. Retrieved data is placed into the working state of a newly created local Dolt repository. Changes to the data cannot be submitted back to the remote repository, and the shallow clone cannot be converted into a regular clone of a repository.

### Arguments and options

`<remote-repo>`: Remote repository to retrieve data from

`<commit>`: Branch or commit hash representing a point in time to retrieve tables from

`<table>`:  Optional tables to retrieve.  If omitted, all tables are retrieved.

`-d`, `--file`:
directory to create and put retrieved table data.



## `dolt gc`

Cleans up unreferenced data from the repository.

### Synopsis

```bash
dolt gc [--shallow]
```

### Description

Searches the repository for data that is no longer referenced and no longer needed.

If the `--shallow` flag is supplied, a faster but less thorough garbage collection will be performed.

### Arguments and options

`-s`, `--shallow`:
perform a fast, but incomplete garbage collection pass



## `dolt filter-branch`

Edits the commit history using the provided query

### Synopsis

```bash
dolt filter-branch [--all] &lt;query&gt; [&lt;commit&gt;]
```

### Description

Traverses the commit history to the initial commit starting at the current HEAD commit. Replays all commits, rewriting the history using the provided SQL query.

If a `<commit-spec>` is provided, the traversal will stop when the commit is reached and rewriting will begin at that commit, or will error if the commit is not found.

If the `--all` flag is supplied, the traversal starts with the HEAD commits of all branches.


### Arguments and options

`-a`, `--all`:
filter all branches



## `dolt merge-base`

Find the common ancestor of two commits.

### Synopsis

```bash
dolt merge-base &lt;commit spec&gt; &lt;commit spec&gt;
```

### Description

Find the common ancestor of two commits, and return the ancestor's commit hash.'

### Arguments and options

No options for this command.
