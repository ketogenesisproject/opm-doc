.. highlight:: perl


****************
check_pgactivity
****************


check_pgactivity - PostgreSQL plugin for Nagios

.. contents:: :depth: 2

SYNOPSIS
========



.. code-block:: perl

   check_pgactivity {-w|--warning THRESHOLD} {-c|--critical THRESHOLD} [-s|--service SERVICE ] [-h|--host HOST] [-U|--username ROLE] [-p|--port PORT] [-d|--dbname DATABASE] [-S|--dbservice SERVICE_NAME] [-P|--psql PATH] [--debug] [--status-file FILE] [--path PATH] [-t|--timemout TIMEOUT]
   check_pgactivity [-l|--list]
   check_pgactivity [--help]



DESCRIPTION
===========


check_pgactivity is designed to monitor PostgreSQL clusters from Nagios. It
offers many options to measure and monitor useful performance metrics.


\ **-s**\ , \ **--service**\  SERVICE

 The nagios service to run. See section SERVICES for a description of
 available services or use \ ``--list``\  for a short service and description
 list.



\ **-h**\ , \ **--host**\  HOST

 Database server host or socket directory (default: "localhost").



\ **-U**\ , \ **--username**\  ROLE

 Database user name (default: "postgres").



\ **-p**\ , \ **--port**\  PORT

 Database server port (default: "5432").



\ **-d**\ , \ **--dbname**\  DATABASE

 Database name to connect to (default: "postgres").

 \ **WARNING**\ ! This is not necessarily one of the database that will be
 checked. See \ ``--dbinclude``\  and \ ``--dbexclude``\  .



\ **-S**\ , \ **--dbservice**\  SERVICE_NAME

 The connection service name from pg_service.conf to use.



\ **--dbexclude**\  REGEXP

 Some services are automatically checking all the databases of your
 cluster (note: that does not mean they always need to connect on all
 of them to check them though). \ ``--dbexclude``\  allows to exclude any
 database whose name matches the given perl regular expression. You
 can repeat this option as many time as needed.

 See \ ``--dbinclude``\  as well. If a database match both dbexclude and
 dbinclude arguments, it is excluded.



\ **--dbinclude**\  REGEXP

 Some services are automatically checking all the databases of your
 cluster(note: that does not mean they always need to connect on all
 of them to check them though). \ ``--dbinclude``\  allows to \ **ONLY**\  check
 databases whose names match the given perl regular expression. You
 can repeat this option as many time as needed.

 See \ ``--dbexclude``\  as well. If a database match both dbexclude and
 dbinclude arguments, it is excluded.



\ **-w**\ , \ **--warning**\  THRESHOLD

 The Warning threshold.



\ **-c**\ , \ **--critical**\  THRESHOLD

 The Critical threshold.



\ **--tmpdir**\  DIRECTORY

 Path to a directory where the script can create temporary files. The
 script relies on the system default temporary directory if possible.



\ **-P**\ , \ **--psql**\  FILE

 Path to the \ ``psql``\  executable (default: "psql").



\ **--status-file**\  PATH

 PATH to the file where service status information will be kept between
 successive calls. Default is to save check_pgactivity.data in the same
 directory as the script.



\ **--dump-status-file**\

 Dump the content of the status file and exit. This is useful for debug purpose.



\ **-t**\ , \ **--timeout**\  TIMEOUT

 Timeout to use (default: "30s"). It can be specified as raw (in seconds) or as
 an interval. This timeout will be used as \ ``statement_timeout``\  for psql and URL
 timeout for \ ``minor_version``\  service.



\ **-l**\ , \ **--list**\

 List available services.



\ **-V**\ , \ **--version**\

 Print version and exit.



\ **--debug**\

 Print some debug messages.



\ **-?**\ , \ **--help**\

 Show this help page.




THRESHOLDS
==========


THRESHOLDS provided as warning and critical values can be a raw numbers,
percentages, intervals or a sizes. Each available service supports one or more
formats (eg. a size and a percentage).


\ **Percentage**\

 If threshold is a percentage, the value should end with a '%' (no space).
 For instance: 95%.



\ **Interval**\

 If THRESHOLD is an interval, the following units are accepted (not case
 sensitive): s (second), m (minute), h (hour), d (day). You can use more than
 one unit per given value. If not set, the last unit is in seconds.
 For instance: "1h 55m 6" = "1h55m6s".



\ **Size**\

 If THRESHOLD is a size, the following units are accepted (not case sensitive):
 b (Byte), k (KB), m (MB), g (GB), t (TB), p (PB), e (EB) or Z (ZB). Only
 integers are accepted. Eg. \ ``1.5MB``\  will be refused, use \ ``1500kB``\ .

 The factor between units is 1024 Bytes. Eg. \ ``1g = 1G = 1024\*1024\*1024.``\




CONNECTIONS
===========


check_pgactivity allows two different connection specifications: by service, or
by specifying values for host, user, port, and database.
Some services can run on multiple hosts, or needs to connect to multiple hosts.

You must specify one of the parameters below if the service needs to connect
to your PostgreSQL instance. In other words, check_pgactivity will NOT look for
the \ ``libpq``\  environment variables.

The format for connection parameters is:


\ **Parameter**\  \ ``--dbservice SERVICE_NAME``\

 Define a new host using the given service. Multiple hosts can be defined by
 listing multiple services separated by a comma. Eg.


 .. code-block:: perl

    --dbservice service1,service2




\ **Parameters**\  \ ``--host HOST``\ , \ ``--port PORT``\ , \ ``--user ROLE``\  or \ ``--dbname DATABASE``\

 One of these parameters is enough to define a new host. If some
 parameters are missing, default values are used.

 If multiple values are given, define as many host as maximum given values.

 Values are associated by position. Eg.:


 .. code-block:: perl

    --host h1,h2 --port 5432,5433


 Means "host=h1 port=5432" and "host=h2 port=5433".

 If the number of values is different between parameters, any host missing a
 parameter will use the first given value for this parameter. Eg.:


 .. code-block:: perl

    --host h1,h2 --port 5433


 Means: "host=h1 port=5433" and "host=h2 port=5433".



\ **Services are defined first**\

 For instance:


 .. code-block:: perl

    --dbservice s1 --host h1 --port 5433


 Means use "service=s1" and "host=h1 port=5433" in this order. If the service
 supports only one host, the second is ignored.



\ **Mutual exclusion between both methods**\

 You can not overwrite services connections variables with parameters \ ``--host HOST``\ , \ ``--port PORT``\ , \ ``--user ROLE``\  or \ ``--dbname DATABASE``\




SERVICES
========


Descriptions and parameters of available services.


\ **archive_folder**\

 Check if all archived WALs exist between the oldest and the latest WAL in the
 archive folder and make sure they are 16MB. The given folder must have archived
 files from ONE cluster. The version of PostgreSQL that created the archives is
 only checked on the last one, for performance consideration.

 This service requires the argument \ ``--path``\  on the command line to specify the
 archive folder path to check.

 Optional argument \ ``--ignore-wal-size``\  skips the WAL size check. This is useful
 if your archived WALs are compressed. Default behaviour is to check the WALs
 size.

 Optional argument \ ``--suffix``\  allows you define the suffix of your archived
 WALs. Useful if they are compressed with an extension (eg. .gz, .bz2, ...).
 Default is no suffix.

 Perfdata contains the number of WALs archived and the age of the most recent
 one.

 Critical and Warning define the max age of the latest archived WAL as an
 interval (eg. 5m or 300s ).



\ **autovacuum**\  (8.1+)

 Check the autovacuum activity on the cluster.

 Perfdata contains the age of oldest running autovacuum and the number of workers
 by type (VACUUM, VACUUM ANALYZE, ANALYZE, VACUUM FREEZE).

 Thresholds, if any, are ignored.



\ **backends**\  (all)

 Check the total number of connections in the PostgreSQL cluster.

 Perfdata contains the number of connections per database.

 Critical and Warning thresholds accept either a raw number or a percentage (eg.
 80%). When a threshold is a percentage, it is compared to the difference
 between the cluster parameters \ ``max_connections``\  and
 \ ``superuser_reserved_connections``\ .



\ **backends_status**\  (8.2+)

 Check the status of all backends. Depending on your PostgreSQL version,
 statuses are: \ ``idle``\ , \ ``idle in transaction``\ , \ ``idle in transaction (aborted)``\
 (>=9.0 only), \ ``fastpath function call``\ , \ ``active``\ , \ ``waiting for lock``\ ,
 \ ``undefined``\ , \ ``disabled``\  and \ ``insufficient privilege``\ .
 \ **insufficient privilege**\  appears when you are not allowed to see the statuses
 of other connections.

 This service supports the argument \ ``--exclude REGEX``\  to exclude queries
 matching the given regular expression from the check.

 You can use multiple \ ``--exclude REGEX``\  arguments.

 Critical and Warning thresholds are optional. They accept a list of
 'status_label=value' separated by a comma. Available labels are \ ``idle``\ ,
 \ ``idle_xact``\ , \ ``aborted_xact``\ , \ ``fastpath``\ , \ ``active``\  and \ ``waiting``\ . Values
 are raw numbers and empty lists are forbidden. Here is an example:


 .. code-block:: perl

      -w 'waiting=5,idle_xact=10' -c 'waiting=20,idle_xact=30'


 Perfdata contains the number of backends for each status and the oldest one for
 each of them, for 8.2+.

 Note that the number of backends reported in Nagios message \ **includes**\
 excluded backend.



\ **backup_label_age**\  (8.1+)

 Check the age of the backup label file.

 Perfdata returns the age of the backup_label file, -1 if not present.

 Critical and Warning thresholds only accept an interval (eg. 1h30m25s).



\ **bgwriter**\  (8.3+)

 Check the percentage of pages written by backends since last check.

 This service uses the status file (see \ ``--status-file``\  parameter).

 Perfdata contains the ratio per second for each \ ``pg_stat_bgwriter``\  counters
 since last execution. Units Nps for checkpoints, max written clean and fsyncs
 are the number of "events" per second.

 Critical and Warning thresholds are optional. If set, they \ *only*\  accept a
 percentage.



\ **btree_bloat**\

 Estimate bloat on B-tree indexes.

 Warning and critical thresholds accept a comma-separated list of either
 raw number(for a size), size (eg. 125M) or percentage. The thresholds apply to
 \ **bloat**\  size, not object size. If a percentage is given, the threshold will
 apply to the bloat size compared to the total index size. If multiple threshold
 values are passed, check_pgactivity will choose the largest (bloat size) value.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.

 It also supports a \ ``--exclude REGEX``\  parameter to exclude relations matching
  the given regular expression. The regular expression applies to
  "database.schema_name.relation_name". This allows you to filter either on a
 relation name for all schemas and databases, filter on a qualified named relation
 (schema + relation) for all databases or filter on a qualified named relation in
 only one database.

 You can use multiple \ ``--exclude REGEX``\  parameters.

 Perfdata will return the number of indexes of concern, by warning and critical
 threshold per database.

 A list of the bloated indexes detail will be returned after the
 perfdata. This list contains the fully qualified bloated index name, the
 estimated bloat size, the index size and the bloat percentage.



\ **commit_ratio**\  (all)

 Check the commit and rollback rate per second since last call.

 This service uses the status file (see --status-file parameter).

 Perfdata contains the commit rate, rollback rate, transaction rate and rollback
 ratio for each database since last call.

 Critical and Warning thresholds are optional. They accept a list of coma
 separated 'label=value'. Available label are \ **rollbacks**\ , \ **rollback_rate**\
 and \ **rollback_ratio**\ , which will be compared to the number of rollback, the
 rollback rate and the rollback ratio of each database. Warning or critical will
 be raised if reported value is greater than \ **rollbacks**\ , \ **rollback_rate**\  or
 \ **rollback_ratio**\ .



\ **configuration**\  (8.0+)

 Check the most important settings.

 Warning and Critical thresholds are ignored.

 Specific parameters are :
 \ ``--work_mem``\ , \ ``--maintenance_work_mem``\ , \ ``--shared_buffers``\ ,\ ``-- wal_buffers``\ ,
 \ ``--checkpoint_segments``\ , \ ``--effective_cache_size``\ , \ ``--no_check_autovacuum``\ ,
 \ ``--no_check_fsync``\ , \ ``--no_check_enable``\ , \ ``--no_check_track_counts``\ .



\ **connection**\  (all)

 Perform a simple connection test.

 No perfdata is returned.

 This service ignore critical and warning arguments.



\ **custom_query**\  (all)

 Perform the given user query.

 The query is specified with the \ ``--query parameter``\ . The first column will be
 used to perform the test for the status if warning and critical are provided.

 The warning and critical arguments are optional. They can be of format integer
 (default), size or time depending on the \ ``--type``\  argument.
 Warning and Critical will be raised if they are greater than the first column,
 or less if the \ ``--reverse``\  option is used.

 All other columns will be used to generate the perfdata. The query must
 display them in the perfdata format, with unit if required (eg. "size=35B").
 If a field contains multiple values, they must be separated by a space.



\ **database_size**\  (8.1+)

 \ **Check the variation**\  of database sizes, and \ **return the size**\  of every
 databases.

 This service uses the status file (see \ ``--status-file``\  parameter).

 Perfdata contains the size of each database.

 Critical and Warning thresholds accept either a raw number, a percentage, or a
 size (eg. 2.5G).  They are applied on the size difference for each database
 since the last execution. The aim is to detect unexpected database size
 variation.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.



\ **hit_ratio**\  (all)

 Check the cache hit ratio on the cluster.

 Perfdata returns the cache hit ratio per database. Template databases and
 databases that do not allow connections will not be checked, nor will the
 databases which have never been accessed.

 Critical and Warning thresholds are optional. They only accept a percentage.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.



\ **hot_standby_delta**\  (9.0)

 Check the data delta between a cluster and its Hot standbys.

 You must give the connection parameters for two or more clusters.

 Perfdata returns the data delta in bytes between the master and each Hot
 standby cluster listed.

 Critical and Warning thresholds can take one or two values separated by a
 comma. If only one value given, it applies to both received and replayed data.
 If two values are given, the first one applies to received data, the second one
 to replayed ones. These thresholds only accept a size (eg. 2.5G).

 This service raise a Critical if it doesn't find exactly ONE valid master
 cluster (ie. critical when 0 or 2 and more masters).



\ **is_hot_standby**\  (9.0+)

 Checks if the cluster is in recovery and accepts read only queries.

 This service ignores critical and warning arguments.

 No perfdata is returned.



\ **is_master**\  (all)

 Checks if the cluster accepts read and/or write queries. This state is reported
 as "in production" by pg_controldata.

 This service ignores critical and warning arguments.

 No perfdata is returned.



\ **is_replay_paused**\  (9.1+)

 Checks if the replication is paused. The service will return UNKNOWN if
 executed on a master server.

 Thresholds are optional. They must be specified as interval. OK will always be
 returned if the standby is not paused, even if replication delta time hits the
 thresholds.

 Critical or warning are raised if last reported replayed timestamp is greater
 than given threshold AND some data received from the master are not applied yet.
 OK will always be returned if the standby is paused, or if the standby has
 already replayed everything from master and until some write activity happens
 on the master.

 Perfdata returned:
   \* paused status (0 no, 1 yes, NaN if master)
   \* lag time (in second)
   \* data delta with master (0 no, 1 yes)



\ **last_analyze**\  (8.2+)

 Check on each databases that the oldest \ ``analyze``\  (from autovacuum or not) is not
 older than the given threshold.

 This service uses the status file (see \ ``--status-file``\  parameter) with
 PostgreSQL 9.1+.

 Perfdata returns oldest \ ``analyze``\  per database in seconds. With PostgreSQL
 9.1+, the number of [auto]analyses per database since last call is also
 returned.

 Critical and Warning thresholds only accept an interval (eg. 1h30m25s)
 and apply to the oldest execution of analyse.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.



\ **last_vacuum**\  (8.2+)

 Check that the oldest vacuum (from autovacuum or otherwise) in each database
 in the cluster is not older than the given threshold.

 This service uses the status file (see \ ``--status-file``\  parameter) with
 PostgreSQL 9.1+.

 Perfdata returns oldest vacuum per database in seconds. With PostgreSQL
 9.1+, it also returns the number of [auto]vacuums per database since last
 execution.

 Critical and Warning thresholds only accept an interval (eg. 1h30m25s)
 and apply to the oldest vacuum.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.



\ **locks**\  (all)

 Check the number of locks on the hosts.

 Perfdata returns the number of locks, by type.

 Critical and Warning thresholds accept either a raw number of locks or a
 percentage. For percentage, it is computed using the following limits
 for 7.4 to 8.1:


 .. code-block:: perl

    max_locks_per_transaction * max_connections


 for 8.2+:


 .. code-block:: perl

    max_locks_per_transaction * (max_connections + max_prepared_transactions)


 for 9.1+, regarding lockmode :


 .. code-block:: perl

    max_locks_per_transaction * (max_connections + max_prepared_transactions)
  or max_pred_locks_per_transaction * (max_connections + max_prepared_transactions)




\ **longest_query**\  (all)

 Check the longest running query in the cluster.

 Perfdata contains the max/avg/min running time and the number of queries per
 database.

 Critical and Warning thresholds only accept an interval.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.

 It also supports argument \ ``--exclude REGEX``\  to exclude queries matching the
 given regular expression from the check.

 You can use multiple \ ``--exclude REGEX``\  parameters.



\ **max_freeze_age**\  (all)

 Checks oldest database by transaction age.

 Critical and Warning thresholds are optional. They accept either a raw number
 or percentage for PostgreSQL 8.2 and more. If percentage is given, the
 thresholds are computed based on the "autovacuum_freeze_max_age" parameter.
 100% means some table(s) reached the maximum age and will trigger an autovacuum
 freeze. Percentage thresholds should therefore be greater than 100%.

 Even with no threshold, this service will raise a critical alert if one database
 has a negative age.

 Perfdata return the age of each database.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.



\ **minor_version**\  (all)

 Check if the cluster is running the most recent minor version of PostgreSQL.

 Latest version of PostgreSQL can be fetched from PostgreSQL official
 website if check_pgactivity can access it, or is given as a parameter.

 Without \ ``--critical``\  or \ ``--warning``\  parameters, this service attempts
 to fetch the latest version online. You can optionally set the path to
 your prefered program using the parameter \ ``--path``\  (eg.
 \ ``--path '/usr/bin/wget'``\ ). Supported programs are: GET, wget, curl,
 fetch, lynx, links, links2.

 For the online version, a critical alert is raised if the minor version is not
 the most recent.

 If you do not want to (or cannot) query the PostgreSQL website, you
 must provide the expected version using either \ ``--warning``\  OR
 \ ``--critical``\ . The given format must be one or more MINOR versions
 seperated by anything but a '.'.

 For instance, the following parameters are all equivalent:


 .. code-block:: perl

    --critical "9.3.2 9.2.6 9.1.11 9.0.15 8.4.19"
    --critical "9.3.2, 9.2.6, 9.1.11, 9.0.15, 8.4.19"
    --critical 9.3.2,9.2.6,9.1.11,9.0.15,8.4.19
    --critical 9.3.2/9.2.6/9.1.11/9.0.15/8.4.19


 Any value other than 3 numbers separated by dots will be ignored.
 if the running PostgreSQL major version is not found, the service raises an
 unknown status.

 Using the offline version raises either a critical or a warning depending
 on which one has been set.

 Perfdata returns the numerical version of PostgreSQL.



\ **oldest_2pc**\  (8.1+)

 Check the oldest \ *two phase commit transaction*\  (aka. prepared transaction) in
 the cluster.

 Perfdata contains the max/avg age time and the number of prepared
 transaction per databases.

 Critical and Warning thresholds only accept an interval.



\ **oldest_idlexact**\  (8.3+)

 Check the oldest \ *idle*\  transaction.

 Perfdata contains the max/avg age and the number of idle transactions
 per databases.

 Critical and Warning thresholds only accept an interval.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.



\ **pg_dump_backup**\

 Check the age and size of backups.

 This service uses the status file (see \ ``--status-file``\  parameter).

 The \ ``--path``\  argument contains the location to the backup folder. The supported
 format is a glob pattern to match every folder or file you need to check. If
 appropriate, the probe should be run as user with sufficient privileges to check
 for the existence of files.

 The \ ``--pattern``\  is required, and must contain a regular expression matching
 the backup file name, extracting the database name from the first matching
 group. For example, the pattern "(\w+)-\d+.dump" can be used to match dumps of
 the form:


 .. code-block:: perl

      mydb-20150803.dump
      otherdb-20150803.dump
      mydb-20150806.dump
      otherdb-20150806.dump
      mydb-20150807.dump


 Optionally, a \ ``--global-pattern``\  option can be supplied to check for an
 additional global file.

 The \ ``--critical``\  and \ ``--warning``\  thresholds are optional. They accept a list
 of 'metric=value' separated by a comma. Available metric are \ ``oldest``\  and
 \ ``newest``\ , respectively the age of the oldest and newest backups, and \ ``size``\ ,
 which must be the maximum variation of size since the last check, expressed
 as a size or a percentage.

 This service supports the arguments \ ``--dbinclude``\  and \ ``--dbexclude``\ , to
 respectively test for the presence of include or exclude files.

 The argument \ ``--exclude``\  allows to exclude file younger than the given
 interval. This is useful to ignore files from a backup in progress. Eg., if
 your backup process takes 2h, set this to '125m'.

 Perfdata returns the age of the oldest and newest backups, as well as the size
 of the newest backups.



\ **pga_version**\

 Checks if this script is running the given version of check_pgactivity.
 You must provide the expected version using either \ ``--warning``\  OR
 \ ``--critical``\ .

 No perfdata is returned.



\ **ready_archives**\  (8.1+)

 Check the number of WAL files ready to archive.

 Perfdata returns the number of WAL files waiting to be archived.

 Critical and Warning thresholds only accept a raw number of files.



\ **replication_slots**\  (9.4+)

 Check the number of WAL retained by each replication slots.

 Perfdata returns the number of WAL that each replication slot has to keep.

 Critical and Warning thresholds are optional. If provided, the number of WAL
 kept by each replication slot will be compared to the threshold.
 These thresholds only accept a raw number.



\ **streaming_delta**\  (9.1+)

 Check the data delta between a cluster and its standbys in Streaming Replication.

 Optional argument \ ``--slave``\  allows you to specify some slaves that MUST be
 connected. This argument can be used as many times as desired to check multiple
 slave connections, or you can specify multiple slaves connections at one time,
 using comma separated values. Both methods can be used in a single call. The
 given value must be of the form "APPLICATION_NAME IP".
 Either of the two following examples will check for the presence of two slaves:


 .. code-block:: perl

    --slave 'slave1 192.168.1.11' --slave 'slave2 192.168.1.12'
    --slave 'slave1 192.168.1.11','slave2 192.168.1.12'


 Perfdata returns the data delta in bytes between the master and all standbys
 found and the number of slaves connected.

 Critical and Warning thresholds can take one or two values separated by a
 comma. If only one value is supplied, it applies to both flushed and replayed
 data. If two values are supplied, the first one applies to flushed data,
 the second one to replayed data.
 These thresholds only accept a size (eg. 2.5G).



\ **table_bloat**\

 Estimate bloat on tables.

 Warning and critical thresholds accept a comma-separated list of either
 raw number(for a size), size (eg. 125M) or percentage. The thresholds apply to
 \ **bloat**\  size, not object size. If a percentage is given, the threshold will
 apply to the bloat size compared to the table + TOAST size.
 If multiple threshold values are passed, check_pgactivity will choose the
 largest (bloat size) value.

 This service supports both \ ``--dbexclude``\  and \ ``--dbinclude``\  parameters.

 This service supports a \ ``--exclude REGEX``\  parameter to exclude relations
 matching the given regular expression. The regular expression applies to
 "database.schema_name.relation_name". This allows you to filter either on a
 relation name for all schemas and databases, filter on a qualified named relation
 (schema + relation) for all databases or filter on a qualified named relation in
 only one database.

 You can use multiple \ ``--exclude REGEX``\  parameters.

 \ **Warning**\ : With a non-superuser role, this service can only check the tables
 the given role is granted to read!

 Perfdata will return the number of tables matching the warning and critical
 thresholds, per database.

 A list of the bloated tables detail will be returned after the
 perfdata. This list contains the fully qualified bloated table name, the
 estimated bloat size, the table size and the bloat percentage.



\ **temp_files**\  (8.1+)

 Check the number and size of temp files.

 This service uses the status file (see \ ``--status-file``\  parameter) for 9.2+.

 Perfdata returns the number and total size of temp files found in
 \ ``pgsql_tmp``\  folders. They are aggregated by database until 8.2, then
 by tablespace (see GUC temp_tablespaces).

 Starting with 9.2, perfdata returns as well the number of temp files per
 database since last run, the total size of temp file per database since last
 run and the rate at which temp files were generated.

 Critical and Warning thresholds are optional. They accept either a number
 of file (raw value), a size (unit is \ **mandatory**\  to define a size) or both
 values separated by a comma.

 Threshols applied on current temp files beeing created AND the number/size
 of temp files created since last execution.



\ **wal_files**\  (8.1+)

 Check the number of WAL files.

 Perfdata returns the total number of WAL files, current number of written WAL,
 the current number of recycled WAL and the rate of WAL written to disk since
 last execution on master clusters.

 Critical and Warning thresholds accept either a raw number of files or a
 percentage. In case of percentage, the limit is computed based on:


 .. code-block:: perl

    100% = 1 + checkpoint_segments * (2 + checkpoint_completion_target)


 For PostgreSQL 8.1 and 8.2:


 .. code-block:: perl

    100% = 1 + checkpoint_segments * 2


 If \ ``wal_keep_segments``\  is set for 9.0 and above, the limit is the greatest
 of the following formulas :


 .. code-block:: perl

    100% = 1 + checkpoint_segments * (2 + checkpoint_completion_target)
    100% = 1 + wal_keep_segments + 2 * checkpoint_segments





EXAMPLES
========



Execute service "last_vacuum" on host "host=localhost port=5432":


 .. code-block:: perl

    check_pgactivity -h localhost -p 5492 -s last_vacuum -w 30m -c 1h30m




Execute service "hot_standby_delta" between hosts "service=pg92" and "service=pg92s":


 .. code-block:: perl

    check_pgactivity --dbservice pg92,pg92s --service hot_standby_delta -w 32MB -c 160MB




Execute service "streaming_delta" on host "service=pg92" to check its slave "stby1" with the IP address "192.168.1.11":


 .. code-block:: perl

    check_pgactivity --dbservice pg92 --slave "stby1 192.168.1.11" --service streaming_delta -w 32MB -c 160MB




Execute service "hit_ratio" on host "slave" port "5433, excluding database matching the regexps "idelone" and "(?i:sleep)":


 .. code-block:: perl

    check_pgactivity -p 5433 -h slave --service hit_ratio --dbexclude idelone --dbexclude "(?i:sleep)" -w 90% -c 80%




Execute service "hit_ratio" on host "slave" port "5433, only for databases matching the regexp "importantone":


 .. code-block:: perl

    check_pgactivity -p 5433 -h slave --service hit_ratio --dbinclude importantone -w 90% -c 80%





VERSION
=======


check_pgactivity version 1.25, released on Thu Jan 28 2016.


LICENSING
=========


This program is open source, licensed under the PostgreSQL license.
For license terms, see the LICENSE provided with the sources.


AUTHORS
=======


Author: Open PostgreSQL Monitoring Development Group
Copyright: (C) 2012-2016 Open PostgreSQL Development Group


