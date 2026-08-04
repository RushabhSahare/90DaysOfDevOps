

- Process checks : ps and top.
The ps and top commands are essential Linux utilities for process management: ps gives a static snapshot of current processes, while top provides a live, continuously updating view of system resource usage.

- Service checks: systemctl status` `systemctl list-units`
The systemctl status and systemctl list-units commands are used to introspect, debug, and view the state of systemd units and background services in Linux. While systemctl status provides a deep, detailed dive into a single specific service, systemctl list-units provides a broad overview of all components currently active or managed in the system's memory

- Log checks : `journalctl -u <service>`,
journalctl is a logging service similar to a syslog. The command journalctl can be used to display failures or errors from specific services.



-Mini Troubleshooting Steps
If a service ( dbus, ssh, or cron) isn't behaving as expected, a quick sequence:

running? → systemctl status <service> or service <service> status
Process alive? → pgrep -a <service> or ps aux | grep <service>
What does it say about itself? → journalctl -u <service> -n 50 or tail -n 50 /var/log/<service>.log
Restart and re-check → systemctl restart <service> then repeat steps 1–3
Still broken? → check ports/networking (ss -tulnp) and permissions (ls -la on config/log files) before digging deeper