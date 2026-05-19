# IvanTheGeek — System Preferences & Tweaks

Preferences and one-time tweaks applied to dev machines and VMs.
Any AI working on setup tasks should check this first.

---

## New Dev Machine Bootstrap

Checklist for bringing a fresh dev VM to parity with the primary machine.
Perform the one-time tweaks under [Dev VM / Local Machine](#dev-vm--local-machine) too.

### Files to transfer from the existing machine

| File | Purpose |
|---|---|
| `~/.ssh/kvmtest_ssh_ivanthegeek_com` | Private key for VPS, Forgejo, all `*.ivanthegeek.com` (no `.pub` kept locally) |
| `~/.ssh/id_ed25519` (+ `.pub`) | GitHub |
| `~/.ssh/config` | Host block routing `*.ivanthegeek.com` to the kvmtest key |
| `~/.ssh/known_hosts` | Optional — skips fingerprint prompts |
| `~/.config/forgejo-claude.token` | Forgejo API (`claude` user) |
| `~/.config/forgejo-claude-ivantg.token` | Forgejo API (`ivantg` user) |

All of the above: `chmod 600` after copying.

### Reference facts

| Item | Value |
|---|---|
| VPS IPv4 | `15.204.119.184` (`vps-482d130f.vps.ovh.us`) |
| VPS IPv6 | `2604:2dc0:202:300::268b` |
| Forgejo SSH | `forgejo.ivanthegeek.com` port `222` |
| Git identity | `IvanTheGeek` / `ivan@claude.ai.irxops.com` |
| Workspace repo | `git@github.com:IvanTheGeek/NEXUS-Ecosystem.git` → clone to `~/nexus` |

### Setup steps

1. **Base packages**: `git`, `curl`, `openssh-client`, `rsync`, `build-essential`, `gh`, Node.js (npm), .NET SDK 10 (for F# work)
2. **Drop in SSH material** from the table above, `chmod 600` each
3. **Verify SSH**:
   - `ssh ivan@15.204.119.184`
   - `ssh -T -p 222 git@forgejo.ivanthegeek.com`
   - `ssh -T git@github.com`
4. **One-time system tweaks** — see [Dev VM / Local Machine](#dev-vm--local-machine): GRUB timeout, polkit nopasswd rule
5. **Install Claude Code**: `curl -fsSL https://claude.ai/install.sh | bash`, then run `claude` once and log in. Do **not** copy `~/.claude/` — let it rebuild fresh (sessions, plugins, and project memory are machine-local)
6. **`gh auth login`** with the GitHub account
7. **`git config --global` name/email** per reference facts
8. **Drop in Forgejo tokens** from the table, `chmod 600`
9. **Clone the workspace**: `git clone git@github.com:IvanTheGeek/NEXUS-Ecosystem.git ~/nexus`, then clone any sub-projects needed locally

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
