# ansible-fail2ban

fail2ban is an intrusion prevention software framework

## Requirements

На Debian/Ubuntu роль ставит `fail2ban` и `ipset`: штатный для нас `banaction = iptables-ipset`
без бинаря `ipset` молча не банит (`/bin/sh: ipset: not found`, `Set f2b-sshd doesn't exist`).

* Ansible 3.0.0+;

## Example configuration

```yaml
---
fail2ban:
# Enable fail2ban service or not
- enable: 'true'
# Restart fail2ban service or not
  restart: 'true'
# Install fail2ban package or not
  install_package: 'true'
# 'present' (do nothing if package is already installed) or 'latest' (always
# upgrade to last version)
  package_state: 'latest'
  settings:
# Verbosity level of log output: 'CRITICAL', 'ERROR', 'WARNING', 'NOTICE',
# 'INFO', 'DEBUG', 'TRACEDEBUG', 'HEAVYDEBUG'. Default is 'ERROR'
  - loglevel: 'ERROR'
# May be: 'filename', 'SYSLOG', 'STDERR' or 'STDOUT' (the default). If fail2ban
# running as systemd-service, for logging to the systemd-journal, the logtarget
# could be set to 'STDOUT'
    logtarget: 'STDOUT'
# This is used for communication with the fail2ban server daemon. Do not remove
# this file when fail2ban is running. It will not be possible to communicate
# with the server afterwards. Default is '/var/run/fail2ban/fail2ban.sock'
    socket: '/var/run/fail2ban/fail2ban.sock'
# PID filename. This is used to store the process ID of the fail2ban server.
# Default: '/var/run/fail2ban/fail2ban.pid'
    pidfile: '/var/run/fail2ban/fail2ban.pid'
# Database filename. Default is '/var/lib/fail2ban/fail2ban.sqlite3'.
# This defines where the persistent data for fail2ban is stored. This persistent
# data allows bans to be reinstated and continue reading log files from the last
# read position when fail2ban is restarted. A value of None disables this
# feature
    dbfile: '/var/lib/fail2ban/fail2ban.sqlite3'
# Max number of matches stored in database per ticket. This option sets the max
# number of matched log-lines could be stored per ticket in the database. This
# also affects values resolvable via tags 'ipmatches' and 'ipjailmatches' in
# actions. Default is 10
    dbmaxmatches: '10'
# Database purge age in seconds. This sets the age at which bans should be
# purged from the database. Default is 86400 (24hours)
    dbpurgeage: '86400'
# Stack size of each thread in fail2ban. This specifies the stack size (in KiB)
# to be used for subsequently created threads, and must be 0 or a positive
# integer value of at least 32
    stacksize: ''
  jail:
# Name of the filter -filename of the filter without the '.conf' or '.local'
# extension. Only one filter can be specified
  - filter: '%(__name__)s[mode=%(mode)s]'
# Filename(s) of the log files to be monitored. Globs - paths containing '*'
# and '?' or '[0-9]' - can be used however only the files that exist at start
# up matching this glob pattern will be considered. Optional space separated
# option 'tail' can be added to the end of the path to cause the log file to be
# read from the end, else default 'head' option reads file from the beginning
    logpath: ''
# Encoding of log files used for decoding. Default value of 'auto' uses current
# system locale
    logencoding: ''
# Force the time zone for log lines that don't have one. If this option is not
# specified, log lines from which no explicit time zone has been found are
# interpreted by fail2ban in its own system time zone, and that may turn to be
# inappropriate. While the best practice is to configure the monitored
# applications to include explicit offsets, this option is meant to handle cases
# where that is not possible. The supported time zones in this option are those
# with fixed offset: 'Z', 'UTC[+-]hhmm' (you can also use GMT as an alias to
# UTC). This option has no effect on log lines on which an explicit time zone
# has been found. Examples: 'UTC', 'UTC+0200', 'GMT-0100'
    logtimezone: ''
# Banning action, default is 'iptables-multiport'
    banaction: ''
# The same as 'banaction' but for some 'allports' jails like 'pam-generic' or
# 'recidive'. Default is 'iptables-allports'
    banaction_allports: ''
# Action(s) from /etc/fail2ban/action.d/ without the '.conf' or '.local'
# extension. Arguments can be passed to actions to override the default values
# from action file
    action: '%(action_)s'
# boolean value (default is 'true') indicates the banning of own IP addresses
# should be prevented
    ignoreself: 'true'
# list of IPs not to ban. They can include a DNS resp. CIDR mask too. The
# option affects additionally to 'ignoreself' (if 'true') and don't need to
# contain own DNS resp. IPs of the running host
    ignoreip: ''
# Command that is executed to determine if the current candidate IP for banning
# (or failure-ID for raw IDs) should not be banned. The option affects
# additionally to 'ignoreself' and 'ignoreip' and will be first executed if both
# don't hit. IP will not be banned if command returns successfully (exit code
# 0). Like ACTION FILES, tags like <ip> are can be included in the
# 'ignorecommand' value and will be substituted before execution
    ignorecommand: ''
# Provide cache parameters (default disabled) for ignore failure check (caching
# of the result from 'ignoreip', 'ignoreself' and 'ignorecommand')
    ignorecache: ''
# Effective ban duration (in seconds or time abbreviation format)
    bantime: ''
# Time interval (in seconds or time abbreviation format) before the current
# time where failures will count towards a ban
    findtime: ''
# Number of failures that have to occur in the last findtime seconds to ban
# then IP
    maxretry: ''
# Backend to be used to detect changes in the 'logpath'. It defaults to 'auto'.
# Available options are listed below:
# 'pyinotify' - requires pyinotify (a file alteration monitor) to be installed.
# If pyinotify is not installed, fail2ban will use 'auto'
# 'gamin' - requires Gamin (a file alteration monitor) to be installed. If Gamin
# is not installed, fail2ban will use 'auto'
# 'polling' - uses a polling algorithm which does not require external
# libraries
# 'systemd' - uses systemd python library to access the systemd journal.
# Specifying 'logpath' is not valid for this backend and instead utilises
# journalmatch from the jails associated filter config
    backend: ''
# Use DNS to resolve HOST names that appear in the logs. By default it is 'warn'
# which will resolve hostnames to IPs however it will also log a warning. If
# you are using DNS here you could be blocking the wrong IPs due to the
# asymmetric nature of reverse DNS (that the application used to write the
# domain name to log) compared to forward DNS that fail2ban uses to resolve this
# back to an IP (but not necessarily the same one). Ideally you should configure
# your applications to log a real IP. This can be set to 'yes' to prevent
# warnings in the log or 'no' to disable DNS resolution altogether (thus
# ignoring entries where hostname, not an IP is logged)
    usedns: ''
# regex (Python regular expression) to be added to the filter's failregexes
# (see failregex in section FILTER FILES for details)
    failregex: ''
# regex which, if the log line matches, would cause fail2ban not consider that
# line. This line will be ignored even if it matches a failregex of the jail or
# any of its filters
    ignoreregex: ''
# max number of matched log-lines the jail would hold in memory per ticket. By
# default it is the same value as 'maxretry' of jail (or default). This option
# also affects values resolvable via tag <matches> in actions
    maxmatches: ''
# Enables the jails. By default all jails are disabled, and it should stay this
# way. Enable only relevant to your setup jails
    enabled: 'false'
# "mode" defines the mode of the filter (see corresponding filter
# implementation for more info)
    mode: 'normal'
# The same as 'jail', but for each filters, like 'sshd', 'postfix'
  jails:
    - name: 'sshd'
        enabled: 'true'
        filter: 'sshd'
        banaction: 'iptables'
        backend: 'systemd'
        maxretry: '5'
        findtime: '86400'
        ignoreself: 'true'
        bantime: '86400'
        ignoreip:
            - '192.168.0.0/16'
            - '198.19.0.0/16'
            - '10.0.0.0/8'
  actions:
    - name: 'pbr'
      includes:
        - before: 'action-before.conf'
          after: 'action-after.conf'
      definintions:
        - actionstart: 'ip route add unreachable 0.0.0.0/0 table <tableid>'
          actionstop: 'ip route delete 0.0.0.0/0 table <tableid>'
          actionban: 'ip rule add to <ip> ipproto <protocol> sport <port> lookup <tableid>'
          actionunban: 'ip rule delete to <ip> ipproto <protocol> sport <port> lookup <tableid>'
          actioncheck: ''
      init:
        - tableid: '777'
          port: '22'
          protocol: 'tcp'
```
