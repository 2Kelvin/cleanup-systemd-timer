# Automated Log Cleanup System

A robust, systemd-driven automation tool designed to manage disk space by purging old log archives on a monthly schedule. This implementation uses a systemd timer to ensure reliability even across system reboots.

## 📂 Project Structure

* `cleanup.service`: Defines the execution logic and the script to be run.
* `cleanup.timer`: Defines the schedule and persistence logic.
* `/usr/local/bin/cleanup-tool`: The executable tool that performs the deletion.

## 🚀 Setup & Installation

Follow these steps to clone the project and install the cleanup tool on your local system.

### 1. Clone the Repository
```bash
git clone https://github.com/2Kelvin/archives-cleanup.git
# follow the project's guide for setup and installation
```

## ⚙️ Configuration

### 1. Service File (`cleanup.service`)
The service is configured as a `oneshot` type, meaning it executes the command and exits immediately without leaving a background process resident in memory.

```ini
[Unit]
Description=Delete old log archives

[Service]
Type=oneshot
ExecStart=/usr/local/bin/cleanup-tool /var/log
```

### 2. Timer File (`cleanup.timer`)
The timer is set to trigger monthly. With `Persistent=true`, if the system is powered off during the scheduled time, systemd will trigger the service immediately upon the next boot.

```ini
[Unit]
Description=Run clean up of logs once every month

[Timer]
OnCalendar=monthly
Persistent=true
Unit=cleanup.service

[Install]
WantedBy=timers.target
```

## 🚀 Deployment Instructions

Follow these steps to install and activate the automation:

1. **Copy Files**: 
   Place both the `.service` and `.timer` files into `/etc/systemd/system/`:
   ```bash
   sudo cp {cleanup.timer,cleanup.service} /etc/systemd/system/
   ```

2. **Reload Daemon**
   Inform systemd of the new configuration files:
   ```bash
   systemctl daemon-reload
   ```

3. **Enable and Start**
   Enable the timer so it persists across reboots and trigger it immediately:
   ```bash
   systemctl enable --now cleanup.timer
   ```

## 🛠️ Maintenance & Updates

If you modify the timer or service configuration, apply the changes using:

[!NOTE] You do not need to run the enable command again after edits unless you have renamed the files.

## 🔍 Monitoring & Debugging

To verify the status of your timer and check when it is scheduled to run next, use the following commands:

| Command | Purpose |
| :--- | :--- |
| `systemctl list-timers --all` | View all active timers and their next scheduled run. |
| `systemctl status cleanup.timer` | Check if the timer is active and "waiting." |
| `journalctl -u cleanup.service` | View the execution logs/output of the cleanup script. |

## 🔍 Pro-tip: Dry run
If you ever want to test if cleanup.service logic actually works without waiting an entire month for the timer to trigger, you can manually trigger the service part alone like this:
```bash
systemctl start cleanup.service
```
This runs the script immediately regardless of the timer schedule.