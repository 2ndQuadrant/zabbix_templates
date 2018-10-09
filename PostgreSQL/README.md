# PostgreSQL Template

When building this template, attempts are being made to allow it
to be run as a 'regular user', but there may be some features that
it makes use of that will not work in certain environments.

Optimally, the following steps can be performed to get a fully working
monitor in place:

1. createuser --superuser zabbix
2. createdb --owner=zabbix zabbix

The following PostgreSQL Extensions are used to generate statistics:

- pgstattuple
        - Used by Table Bloat monitoring
- pg_buffercache
        - Buffer Statistics

For most monitoring, it is recommended that the zabbix-agent is on the same
server as the database, but in the case of something like RDS, you would need
to setup a seperate instance that connects into the database server 'remotely'.

In either case, you will need to setup a .pgpass file under the zabbix user on
the machine running zabbix-agentd, to allow for passwordless access to the
database

# Monitoring

In order to reduce the impact on the server, our discovery rules are written, by
default, in such a way to only return what we are generally interested in.  And, 
due to limitations we have yet to figure out a way around with Zabbix, you can
only monitor one database on a server ... once we can figure out how to get around
that, we will obviously update the template accordingly.

As such, at present, there is one MACRO that needs to be set per server being monitored,
and that is {$ACTIVE_DB}, which is the database whose tables / indexes / etc you are
specifically monitoring fow what is listed below.  If you do not set that value, the 
tests below will not work.

## Bloat Monitoring

The thresholds for Bloat Monitoring are set within the Template Macros, and can be
adjusted for each instance accordingly.  There are 4 settings:

- ${BLOAT_CRITICAL_BYTES}
- ${BLOAT_CRITICAL_PERCENT}
- ${BLOAT_WARN_BYTES}
- ${BLOAT_WARN_PERCENT}

The Discovery rule for this is based on the WARN levels:

  if FreeSpace > ${BLOAT_WARN_BYTES} AND PercentFree > ${BLOAT_WARN_PERCENT}

