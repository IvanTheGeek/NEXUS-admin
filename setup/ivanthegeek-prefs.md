# IvanTheGeek — System Preferences & Tweaks

Preferences and one-time tweaks applied to dev machines and VMs.
Any AI working on setup tasks should check this first.

---

## Dev VM / Local Machine

| Setting | Value | How |
|---|---|---|
| GRUB timeout | 1 second | `GRUB_TIMEOUT=1` in `/etc/default/grub`, then `update-grub` (applied to both local VM and OVH VPS) |
| polkit (GUI sudo) | Passwordless for `ivan` | `/etc/polkit-1/rules.d/49-nopasswd-ivan.rules` (see below) |

### polkit nopasswd rule

Applies to LMDE7 (and any Debian/Ubuntu system using polkit). Eliminates password prompts in GUI apps like update manager, virt-manager, etc.

```javascript
// /etc/polkit-1/rules.d/49-nopasswd-ivan.rules
polkit.addRule(function(action, subject) {
    if (subject.user === "ivan" && subject.local && subject.active) {
        return polkit.Result.YES;
    }
});
```

Takes effect immediately, no reboot needed.

---

## Forgejo (forgejo.ivanthegeek.com)

| Item | Value |
|---|---|
| URL | `https://forgejo.ivanthegeek.com` |
| SSH | port 222 |
| Claude user token | `~/.config/forgejo-claude.token` |
| Token scope | read/write repos, issues, PRs — use for any Forgejo API work |

When a Claude session needs to call the Forgejo API, read `~/.config/forgejo-claude.token` for the Bearer token. The `claude` user has access to all orgs/repos used for AI work (FnTools, etc.).

---

## In-Progress / Interrupted Work

### PXE / Network Boot

- Was setting up PXE boot for VM provisioning
- Got side-tracked by VirtualBox issues — moved to **virt-manager** (libvirt/KVM)
- PXE work needs to be **redone from scratch** targeting virt-manager, not VirtualBox
- Prior VirtualBox-specific PXE config should be discarded
- Status: **not started** on virt-manager

---

## Remote VPS (OVH)

See `LaundryLog/deploy/vps-setup.md` for full VPS setup details.

| Setting | Value |
|---|---|
| Hostname | `vps-482d130f.vps.ovh.us` / `15.204.119.184` |
| SSH key | `~/.ssh/kvmtest_ssh_ivanthegeek_com` |
| GRUB timeout | 1 second (applied 2026-04-21) |
