
# collectd-haproxy

This is a [collectd](https://collectd.org) plugin for [HAProxy](https://haproxy.com), for which it has been tested to be working as of version 1.7.5.

It uses the exposed HAProxy socket commands (on defined TCP/Unix sockets) to monitor statistics from the `show info`, `show stat` and `show resolvers` (when on HAProxy 1.8+) commands.
This allows monitoring of the overall service status as well as frontends, backends, servers and resolvers configured.
It also supports multi-process statistics aggregation, allowing to configure multiple sockets to collectd metrics from.

## Usage

Install the source file `haproxy.py` in an arbitrary path `${source_path}`.
You can then configure the plugin the following way:

```bash
cat <<EOF > /etc/collectd/plugins/haproxy.conf
<LoadPlugin python>
    Globals true
</LoadPlugin>

<Plugin python>
    ModulePath "${source_path}"
    Import "haproxy"
    <Module haproxy>
      Socket "/var/run/haproxy/proc1.sock"
      Socket "unix:///var/run/haproxy/proc2.sock"
      Socket "tcp://127.0.0.1:8080"
      ProxyMonitor "backend"
      # ProxyMonitor "server" or "frontend"
      # ProxyIgnore to ignore metrics
      # Verbose to increase verbosity
    </Module>
</Plugin>
EOF
```