# Prepare an Ubuntu VPS

--8<-- "includes/xc-server-pro.md"

Use this page before [Linux Service](linux-service.md) when IPTVBoss will run on a public VPS. It prepares a new **Ubuntu Server 24.04 LTS** host with a normal administrator account, SSH key access, a firewall, basic SSH protection, automatic security updates, and DNS for Caddy.

This page is for the native Linux service installation. Docker users should follow the prerequisites in the [Docker installation guide](docker.md). macOS and Windows users should use their platform-specific setup page.

You do not need previous Linux or SSH experience. This guide explains which computer each command runs on and what to expect from it.

!!! warning "Keep a recovery path"
    Keep the VPS provider's browser-based console or recovery access available while changing SSH settings. Do not close your original SSH connection until a second connection has been tested successfully.

## Before you begin

You will need:

- a Windows, macOS, or Linux computer;
- a VPS running **Ubuntu Server 24.04 LTS**;
- the VPS public IPv4 address;
- the username, password, or other login information supplied by the VPS provider;
- access to the provider's browser-based console or recovery system; and
- a domain name you control.

### Terms used in this guide

| Term | Meaning |
| --- | --- |
| **Your computer** | The Windows, macOS, or Linux computer in front of you. |
| **VPS** or **server** | The remote Ubuntu computer running at your hosting provider. |
| **Provider dashboard** | The hosting provider's website, where you create, start, stop, or repair the VPS. |
| **Provider console** | An emergency terminal in the provider dashboard. It can usually reach the VPS even when SSH is not working. |
| **Terminal** | The application in which you type commands. On Windows, use PowerShell or Windows Terminal. On macOS and Linux, use Terminal. |
| **SSH** | Secure Shell, the encrypted tool used to control the VPS from your computer. |

### Replace the placeholders

Commands in this guide use the following placeholders:

| Placeholder | Replace it with | Example |
| --- | --- | --- |
| `SERVER_IP` | The VPS public IPv4 address | `203.0.113.10` |
| `SERVER_USER` | Your normal administrator username | `ubuntu` or `bossadmin` |
| `boss.example.com` | The hostname you will use for IPTVBoss | `boss.yourdomain.com` |

Do not type `SERVER_IP` or `SERVER_USER` literally. For example, if the username is `ubuntu` and the IP address is `203.0.113.10`, this command:

```bash
ssh SERVER_USER@SERVER_IP
```

becomes:

```bash
ssh ubuntu@203.0.113.10
```

## 1. Open a terminal on your computer

Open a terminal on the computer from which you will manage the VPS:

- **Windows 10 or 11:** Open **PowerShell** or **Windows Terminal** from the Start menu.
- **macOS:** Open **Terminal** from **Applications > Utilities**.
- **Linux:** Open your distribution's **Terminal** application.

Run this command **on your computer**:

```bash
ssh -V
```

If the response begins with `OpenSSH`, the SSH client is ready.

On current versions of macOS and most Linux distributions, SSH is already installed. If it is missing on Ubuntu or Debian, install it with:

```bash
sudo apt update
sudo apt install openssh-client
```

If Windows reports that `ssh` is not recognized, install **OpenSSH Client**:

1. Open **Settings**.
2. Go to **System > Optional features**.
3. Select **View features** beside **Add an optional feature**.
4. Search for and install **OpenSSH Client**.
5. Close and reopen PowerShell or Windows Terminal.

See [OpenSSH for Windows](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh-overview) if the option is unavailable.

## 2. Create an SSH key on your computer

A public server is constantly probed by automated systems trying usernames and passwords. This guide replaces password-based SSH login with an SSH key.

An SSH key has two parts:

- the **private key** stays on your computer and must never be copied or shared; and
- the **public key** is installed on the VPS and is safe to give to your VPS provider.

When you connect, the VPS verifies that your computer has the matching private key. Ubuntu recommends the Ed25519 key type for this purpose. See [Ubuntu's SSH key guidance](https://ubuntu.com/server/docs/how-to/security/openssh-server/#ssh-keys) for more information.

The commands below create a dedicated key named `iptvboss_vps_ed25519`. This prevents an existing default SSH key from being overwritten.

### Windows PowerShell

Run **on your computer**:

```powershell
New-Item -ItemType Directory -Force "$HOME\.ssh" | Out-Null
ssh-keygen -t ed25519 -f "$HOME\.ssh\iptvboss_vps_ed25519"
```

### macOS or Linux

Run **on your computer**:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t ed25519 -f ~/.ssh/iptvboss_vps_ed25519
```

!!! warning "Do not overwrite an existing key"
    If `ssh-keygen` says that `iptvboss_vps_ed25519` already exists, enter `n` when asked whether to overwrite it. Reuse that key only if you know it was created for this VPS. Otherwise, create a key with a different filename and use that filename throughout this guide.

When asked for a passphrase, enter a strong passphrase and record it somewhere secure. The passphrase protects the private key if someone gains access to your computer. Nothing appears while you type it. Press **Enter** again when asked to confirm it.

The command creates two files:

| File | Purpose |
| --- | --- |
| `iptvboss_vps_ed25519` | Your private key. Never copy, upload, email, or share it. |
| `iptvboss_vps_ed25519.pub` | Your public key. This is the single line installed on the VPS. |

Display the public key so it can be copied.

**Windows PowerShell:**

```powershell
Get-Content "$HOME\.ssh\iptvboss_vps_ed25519.pub"
```

**macOS or Linux:**

```bash
cat ~/.ssh/iptvboss_vps_ed25519.pub
```

Copy the entire line. It normally begins with `ssh-ed25519`.

## 3. Create the VPS

Create the VPS using the provider's **Ubuntu Server 24.04 LTS** image.

During creation:

1. If the provider asks for an SSH public key, paste the public key copied in [step 2](#2-create-an-ssh-key-on-your-computer).
2. Record the VPS public IPv4 address.
3. Record the initial username. Common usernames include `ubuntu`, `admin`, and `root`.
4. Record any temporary password or other login instructions.
5. Find the provider console or recovery option before continuing.

Prefer a provider-created non-root account such as `ubuntu` with `sudo` access. If the provider gives you only `root`, use it temporarily. You will create a normal administrator account later and will not run IPTVBoss as root.

If the provider has a network or cloud firewall, make sure inbound **TCP port 22** is allowed so that SSH can connect. Ports 80 and 443 will be added in [step 10](#10-configure-the-firewall).

If the VPS already exists, use its current login details and continue with the next step.

## 4. Connect to the VPS for the first time

The provider should tell you whether the SSH public key was installed during creation.

### Connect with the SSH key

If the provider installed the public key, run the command for your operating system **on your computer**.

**Windows PowerShell:**

```powershell
ssh -i "$HOME\.ssh\iptvboss_vps_ed25519" SERVER_USER@SERVER_IP
```

**macOS or Linux:**

```bash
ssh -i ~/.ssh/iptvboss_vps_ed25519 SERVER_USER@SERVER_IP
```

Enter the key passphrase from step 2 if asked.

### Connect with a password

If the provider supplied a username and password instead, run **on your computer**:

```bash
ssh SERVER_USER@SERVER_IP
```

Enter the password supplied by the VPS provider when asked. The terminal will not show dots, stars, or any other characters while you type a password. This is normal. Type the password and press **Enter**.

### Accept the server identity prompt

The first connection normally displays a message similar to:

```text
The authenticity of host '203.0.113.10' can't be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Confirm that the displayed IP address matches the new VPS. If the provider displays an SSH host fingerprint in its dashboard or console, compare it with the fingerprint shown by SSH. Then type:

```text
yes
```

and press **Enter**.

After a successful connection, the terminal prompt changes to one belonging to the VPS. Commands typed in this window now run on the VPS, not on your computer.

Check which account you are using:

```bash
whoami
```

Keep this SSH window open throughout the remaining SSH setup.

## 5. Understand `sudo`

`sudo` runs one command with administrator privileges. It allows a normal account to perform administrative work without logging in as root.

Ubuntu may ask for your account password when you use `sudo`. As with an SSH password, nothing appears while you type it. Enter the password and press **Enter**.

The examples below use `sudo`. If `whoami` displayed `root`, omit `sudo` until the normal administrator account has been created.

## 6. Update the VPS

Run the following commands **on the VPS**:

```bash
sudo apt update
sudo apt full-upgrade -y

sudo apt install -y \
    curl \
    ca-certificates \
    nano \
    ufw \
    fail2ban \
    unattended-upgrades
```

If you are temporarily connected as root, run the same commands without `sudo`.

If the update reports that a reboot is required, run:

```bash
sudo reboot
```

If you are still connected as root, run `reboot` without `sudo`.

The SSH connection will close when the VPS restarts. Wait a minute or two, then reconnect using the same command from step 4.

## 7. Create an administrator account if needed

Run **on the VPS**:

```bash
whoami
```

If it displays a non-root username such as `ubuntu` or `admin`, skip to [step 8](#8-install-and-test-the-ssh-key).

If it displays `root`, create a normal administrator account named `bossadmin`:

```bash
adduser bossadmin
usermod -aG sudo bossadmin
```

Choose a strong password when `adduser` asks for one. This password is used for `sudo`, even after password-based SSH login is disabled. The remaining profile questions are optional; press **Enter** to leave them blank, then enter `Y` to confirm.

For the rest of this guide, your `SERVER_USER` is now `bossadmin`.

Do not close the root connection yet.

## 8. Install and test the SSH key

Skip the installation portion of this step only if both of the following are true:

- you connected as a normal, non-root administrator in step 4; and
- that connection already used `iptvboss_vps_ed25519`.

Otherwise, open a **second terminal on your computer** and use the appropriate command below. Replace `SERVER_USER` and `SERVER_IP` before running it.

### Windows PowerShell

```powershell
Get-Content "$HOME\.ssh\iptvboss_vps_ed25519.pub" | ssh SERVER_USER@SERVER_IP "umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys"
```

### macOS or Linux

```bash
cat ~/.ssh/iptvboss_vps_ed25519.pub | ssh SERVER_USER@SERVER_IP 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys'
```

This connection may ask for the account's temporary password one final time. It copies only the public key.

!!! tip "If password login is unavailable"
    If the provider does not allow this command to connect, use its browser console or your existing root SSH connection. Display and copy the public key on your computer, then install it for `bossadmin` on the VPS:

    ```bash
    install -d -m 700 -o bossadmin -g bossadmin /home/bossadmin/.ssh
    nano /home/bossadmin/.ssh/authorized_keys
    chown bossadmin:bossadmin /home/bossadmin/.ssh/authorized_keys
    chmod 600 /home/bossadmin/.ssh/authorized_keys
    ```

    Paste the complete public key as a single line in `nano`. Press **Ctrl+O**, then **Enter** to save. Press **Ctrl+X** to exit.

### Test the key in a second terminal

Keep the original SSH connection open. In a second terminal **on your computer**, connect using the key.

**Windows PowerShell:**

```powershell
ssh -i "$HOME\.ssh\iptvboss_vps_ed25519" SERVER_USER@SERVER_IP
```

**macOS or Linux:**

```bash
ssh -i ~/.ssh/iptvboss_vps_ed25519 SERVER_USER@SERVER_IP
```

The key's passphrase may be requested. The VPS account password should not be requested.

In the new SSH connection, verify the account and its administrator access:

```bash
whoami
sudo -v
```

The first command must show your normal account, not `root`. The second command should return without an error after accepting the account password, if requested.

Do not continue until this test succeeds. If it fails, keep the original connection open and correct the username, key path, or `authorized_keys` file.

## 9. Harden SSH

Only continue after key login and `sudo` have both been tested in a second terminal.

Create a separate SSH configuration file **on the VPS**:

```bash
sudo nano /etc/ssh/sshd_config.d/00-hardening.conf
```

`nano` is a text editor. Paste these lines:

```text
PermitRootLogin no
PasswordAuthentication no
KbdInteractiveAuthentication no
PubkeyAuthentication yes
X11Forwarding no
```

Press **Ctrl+O**, then **Enter** to save the file. Press **Ctrl+X** to exit.

Check the configuration before applying it:

```bash
sudo sshd -t
```

No output means the configuration passed the check. If an error appears, do not reload SSH. Reopen the file, correct the reported problem, and run the check again.

When the check succeeds, reload SSH:

```bash
sudo systemctl reload ssh
```

Keep both existing connections open. Open one more terminal **on your computer** and test key login again:

**Windows PowerShell:**

```powershell
ssh -i "$HOME\.ssh\iptvboss_vps_ed25519" SERVER_USER@SERVER_IP
```

**macOS or Linux:**

```bash
ssh -i ~/.ssh/iptvboss_vps_ed25519 SERVER_USER@SERVER_IP
```

After the new connection succeeds, the original root or password-based connection can be closed with:

```bash
exit
```

This guide deliberately keeps SSH on port 22. Key authentication, disabled root login, a firewall, and Fail2ban provide useful protection without adding another connection setting to troubleshoot.

## 10. Configure the firewall

The completed installation needs public access to:

| Port | Protocol | Purpose |
| --- | --- | --- |
| `22` | TCP | SSH administration |
| `80` | TCP | HTTP and Caddy certificate validation |
| `443` | TCP | HTTPS access to IPTVBoss |

IPTVBoss itself listens on `127.0.0.1:8001`. That address is reachable only from the VPS, so **do not open port 8001 publicly**.

Run **on the VPS**:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

UFW may display:

```text
Command may disrupt existing ssh connections. Proceed with operation (y|n)?
```

The `OpenSSH` rule was added before enabling the firewall, so type `y` and press **Enter**.

Verify the result:

```bash
sudo ufw status verbose
```

You should see rules equivalent to the following, possibly repeated for IPv6:

```text
22/tcp    ALLOW
80/tcp    ALLOW
443/tcp   ALLOW
```

There should be no rule for port 8001. See the [Ubuntu firewall guide](https://ubuntu.com/server/docs/how-to/security/firewalls/) for more information.

If the VPS provider also supplies a cloud or network firewall, configure these inbound rules in the provider dashboard:

```text
TCP 22
TCP 80
TCP 443
```

Leave other inbound ports closed unless another installed service specifically requires one.

## 11. Enable SSH protection

Fail2ban temporarily blocks addresses that repeatedly fail to authenticate.

Create its SSH configuration **on the VPS**:

```bash
sudo nano /etc/fail2ban/jail.d/sshd.local
```

Add:

```ini
[sshd]
enabled = true
backend = systemd
maxretry = 5
findtime = 10m
bantime = 1h
```

Save with **Ctrl+O**, then **Enter**, and exit with **Ctrl+X**.

Enable Fail2ban and confirm that the SSH jail is active:

```bash
sudo systemctl enable --now fail2ban
sudo fail2ban-client status sshd
```

The output should identify one jail named `sshd`. A banned-address count of zero is normal.

## 12. Enable automatic security updates

Run **on the VPS**:

```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

Select **Yes** when prompted. Ubuntu will then automatically download and install eligible security updates. See the [Ubuntu automatic updates guide](https://ubuntu.com/server/docs/how-to/software/automatic-updates/) for details.

Confirm that the service is enabled:

```bash
sudo systemctl is-enabled unattended-upgrades
```

The expected result is:

```text
enabled
```

## 13. Set up the hostname and DNS

Choose a hostname for IPTVBoss, such as:

```text
boss.example.com
```

Your DNS provider is the service where your domain's DNS records are managed. It may be your domain registrar, Cloudflare, or another DNS service.

In the DNS provider's dashboard, create an `A` record pointing the hostname to the VPS public IPv4 address:

| Type | Host or name | Value | TTL |
| --- | --- | --- | --- |
| `A` | `boss` or `boss.example.com`, depending on the provider | `SERVER_IP` | Automatic or default |

Create an `AAAA` record only if IPv6 is configured and working on the VPS. An incorrect IPv6 record can cause HTTPS connections to fail intermittently.

If the DNS provider offers a proxy option, **DNS only** is the simplest setting during the initial installation. A proxy can be enabled later after direct HTTPS access is working.

Wait for the record to resolve before continuing. From a terminal **on your computer**, run:

```bash
nslookup boss.example.com
```

The result should contain the VPS IPv4 address. DNS changes often appear within a few minutes but can take longer, depending on the provider and the record's previous TTL.

Caddy needs the hostname to resolve to this VPS and needs inbound TCP ports 80 and 443 to obtain and renew its HTTPS certificate.

## 14. Run the pre-flight check

Run this block **on the VPS** as the normal administrator account:

```bash
whoami
grep '^PRETTY_NAME=' /etc/os-release
df -h /
free -h

sudo ufw status verbose
sudo systemctl is-active ssh
sudo systemctl is-active fail2ban
sudo fail2ban-client status sshd
sudo systemctl is-enabled unattended-upgrades
```

The VPS is ready when:

- `whoami` shows the normal, non-root administrator account;
- the operating system is Ubuntu 24.04 LTS;
- the root filesystem has enough free space for the IPTVBoss database and generated output;
- UFW is active and allows only the required inbound ports;
- SSH and Fail2ban both report `active`;
- Fail2ban reports an enabled `sshd` jail; and
- unattended upgrades report `enabled`.

Continue with [Linux Service](linux-service.md) to install the IPTVBoss `.deb`, configure systemd, install Caddy, and open the [Server Console first-time setup](../console/login.md).

## Common connection problems

| Message or symptom | What to check |
| --- | --- |
| `Connection timed out` | Confirm that the VPS is running, `SERVER_IP` is correct, and TCP port 22 is allowed by the provider firewall. |
| `Connection refused` | Confirm that the VPS finished booting and that its SSH service is running through the provider console. |
| `Permission denied (publickey)` | Confirm the provider's username, the private-key path after `-i`, and that the matching public key is in the account's `~/.ssh/authorized_keys` file. |
| A password appears not to type | Password fields in SSH, `sudo`, and Linux terminals display no characters. Type it normally and press **Enter**. |
| Key login stops working after hardening | Keep the original SSH session open. Use it or the provider console to correct `/etc/ssh/sshd_config.d/00-hardening.conf`, run `sudo sshd -t`, and reload SSH. |
