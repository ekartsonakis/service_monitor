# service_monitor

What it is?
===

This is an automatic way to restart your services based on user defined checks. More or less what Monit does but with pure systemd power without external dependencies. This is a spare time project for my VPC so please review and test it before use on production.

Please note that this service is meaningful only if your monitored service is not failing itself but it keeps its status "running" without serving its original purpose. If you have a self failing service, systemd can handle it if you work with service systemd options like  `Restart=on-failure`.  

Installation
===

1. Copy service template as is `service-monitor@.service` file under your `/usr/lib/systemd/system/`. 
2. Rename `sample.monitor` check script to the service name you want to monitor and keep this filename format: `[service_to_monitor].monitor`, For example `cassandra.monitor` 
3. Modify check script and add more actions or checks to meet your needs and place it under `/opt/service-monitor/`. If you change this location edit the service file too.
4. Run `systemctl daemon-reload` and  `systemctl enable cassandra.monitor`  and `systemctl start cassandra.monitor`. Enabling this service is crucial to let it start and stop when the monitored service is doing the same on demand.
5. Repeat from step 2 for other services. You don't need to repeat step 1 since this is a templatized systemd service and systemd will do the work for you when you enable/start your next monitor.

How it works
===
This helper service will start right after your monitored service and after the `init_delay` it will start probing and run your checks. If your checks fail, it will try automatically to restart your monitored service. If this process take place more that `max_retries` in `retry_for_mins` it will permanently stop the monitored service and itself and let sysadmins take action.
