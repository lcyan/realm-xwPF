# Port Traffic Dog restart persistence fix

This local working copy contains changes to `port-traffic-dog.sh` that improve nftables counter persistence across reboot/reload scenarios.

## Changes

- Adds `port-traffic-dog-persist.service` systemd oneshot service:
  - `ExecStart`: restores nftables monitoring rules and saved counter data at boot.
  - `ExecStop`: backs up current nftables counter data during shutdown/reboot.
- Adds CLI commands:
  - `--backup-traffic-data`
  - `--restore-monitoring`
  - `--check-monitoring`
- Runs `init_config` before notification commands:
  - `--send-telegram-status`
  - `--send-wecom-status`
  - `--send-status`
- Makes nftables counter read failures visible in notification logs instead of silently treating all failures as valid zero traffic.
- Prevents missing counter reads from overwriting an existing traffic backup with empty data.

## Verification run

```bash
bash -n port-traffic-dog.sh
shellcheck -S error port-traffic-dog.sh
git diff --check
```

All passed in the local working copy.

Note: full `shellcheck port-traffic-dog.sh` still reports pre-existing warnings/info from the upstream script. The modified script passes ShellCheck at error severity (`-S error`), which is suitable here because the upstream script already has many warnings unrelated to this fix.
