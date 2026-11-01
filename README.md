# Ansible Linux Server Hardening

Idempotent Ansible roles that apply a security baseline (CIS-inspired) to Ubuntu/Debian and RHEL-family servers.

## What it does
| Role | Actions |
|---|---|
| common | patching, baseline packages, time sync, sensitive file permissions |
| users | admin user + SSH key, validated sudo drop-in, root lock, password aging |
| ssh_hardening | no root login, key-only auth, `AllowUsers`, MaxAuthTries, idle timeout, `sshd -t` validation |
| firewall | UFW (Debian) / firewalld (RHEL): default deny, allow SSH/HTTP/HTTPS, SSH rate-limit |
| fail2ban | SSH brute-force protection |
| sysctl_hardening | rp_filter, SYN cookies, no redirects/source routing, ASLR |
| auto_updates | unattended security updates |
| auditd | audit rules for identity, sudoers and sshd config changes |

## Usage
```bash
pip install ansible
ansible-galaxy collection install -r requirements.yml
# edit inventory/hosts.ini and group_vars/all.yml (set YOUR public key!)
ansible-playbook site.yml --syntax-check
ansible-playbook site.yml --check --diff     # dry run
ansible-playbook site.yml                    # apply
ansible-playbook site.yml --tags ssh_hardening --limit web01
```

> Warning: keep a second SSH session open while applying SSH/firewall changes, and test on a non-production host first.

## Verify
```bash
sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication'
sudo ufw status verbose        # or: firewall-cmd --list-all
sudo fail2ban-client status sshd
sudo auditctl -l
```

## Skills demonstrated
Ansible roles/handlers/templates, idempotency, Linux security hardening, SSH/firewall/auditd, multi-distro playbooks.
