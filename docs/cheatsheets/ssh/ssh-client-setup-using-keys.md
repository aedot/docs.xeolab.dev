---
title: Ubuntu SSH Server Permission Denied (publickey)
outdated: false
---

Check /etc/ssh/sshd_config file
```
sudo nano /etc/ssh/sshd_config
```

- If you login as root, make sure `PermitRootLogin yes`.
- If you login with password, makr sure `PasswordAuthentication yes`.
- If you just edited /etc/ssh/sshd_config, run `sudo systemctl reload sshd` for the changes to take effect.
