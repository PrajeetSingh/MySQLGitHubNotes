## MySQL Configuration Validation

Before we start MySQL instance, we can check for any Warnings or any Errors in our configuration. 
If there is any Error/Typo in configuration of Parameters/Variables in cnf files, then MySQL instance will not start.
But if there are Warnings only, MySQL instance will start, but with help of below command, we can get recommendations to know and fix them, instead of going through mysql log files.

Like, below we don't have any error in configuration of parameters in my.cnf but we get some recommendations.
```sh
# mysqld --validate-config
2025-02-15T17:30:21.919578Z 0 [Warning] [MY-011070] [Server] 'Disabling symbolic links using --skip-symbolic-links (or equivalent) is the default. Consider not using this option as it' is deprecated and will be removed in a future release.
2025-02-15T17:30:21.919589Z 0 [Warning] [MY-010915] [Server] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2025-02-15T17:30:21.920161Z 0 [Warning] [MY-010097] [Server] Insecure configuration for --secure-log-path: Current value does not restrict location of generated files. Consider setting it to a valid, non-empty path.
2025-02-15T17:30:21.920215Z 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
```

Now, I deliberately added a wrong parameter and value "binnlog_format = ROU" and running this command will told me that I have an error at line number 11. 

So, I can fix it, then run validation again and then startup mysql instance.

```sh
# vi /etc/mysql/my.cnf

# mysqld --validate-config
mysqld: [ERROR] Found option without preceding group in config file /etc/mysql/my.cnf at line 11.
mysqld: [ERROR] Fatal error in defaults handling. Program aborted!

# cat -n /etc/mysql/my.cnf
     1  #
     2  # The Percona Server 8.0 configuration file.
     3  #
     4  # For explanations see
     5  # http://dev.mysql.com/doc/mysql/en/server-system-variables.html
     6  #
     7  #
     8  # * IMPORTANT: Additional settings that can override those from this file!
     9  #   The files must end with '.cnf', otherwise they'll be ignored.
    10  #
    11  binnlog_format = ROU                                # <---- Deliberately added this wrong parameter and its value to test this tool.
    12  !includedir /etc/mysql/conf.d/
    13  !includedir /etc/mysql/mysql.conf.d/
    14  !includedir /etc/mysql/percona-server.conf.d/
#
```
Ideal thing is that this command gives no output, that means there is no Warning or Error and MySQL configuration file is perfectly fine.