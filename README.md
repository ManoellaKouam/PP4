# PP4

## Goal

In this exercise you will:

* Use SSH to connect to remote servers from WSL, macOS, or Linux shells, understanding the handshake and authentication process.
* Generate an Ed25519 SSH key pair and explain the concept of digital signatures.
* Configure your local SSH client via the `~/.ssh/config` file for streamlined access.
* Securely copy files between local and remote hosts using `scp`, including local-to-remote, remote-to-local, and remote-to-remote transfers.
* Automate startup tasks on the remote server by writing a shell script that runs at login and explaining the role of `~/.bashrc` vs. `~/.profile`.

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. Once time is up, stop immediately and record exactly where you paused.

---

## Workflow

1. **Fork** this repository
2. **Modify & commit** your solution
3. **Submit your link for Review**

---

## Prerequisites

* Several starter repos are available here:
  [https://github.com/orgs/STEMgraph/repositories?q=SSH%3A](https://github.com/orgs/STEMgraph/repositories?q=SSH%3A)
* Consult the SSH and SCP man-pages for detailed options and explanations:

  * `man ssh`
  * `man scp`

---

## Tasks

### Task 1: SSH Login

**Objective:** Establish an SSH connection and observe each stage of the process.

1. From your local shell (WSL, macOS Terminal, or Linux), log into the `vorlesungsserver` (or any other remote machine of your choice, e.g. your own raspberry pi):

   ```bash
   ssh youruser@remotehost
   ```
2. Carefully observe and note each step:

   * **TCP connection** to port 22 on `remotehost`.
   * **SSH protocol handshake**: key exchange and algorithm negotiation.
   * **Authentication**: public-key or password exchange.
   * **Shell allocation**: your remote session starts.
3. After login, exit the session with `exit`.

**Provide:**

```bash
# 1) The exact ssh command you ran
       ssh sshuser@192.168.0.102

# 2) A detailed, step-by-step explanation of what happened at each stage

**TCP connection:**  Der ssh-client hat eine Tcp-Verbindung zum port 22 der Ip-Adresse 192.168.0.102 geoffnet
**SSH-Handshake:**  Client und Server haben ihre Protokollversionen ausgetauscht und verschlüsselungsalgorithmen ausgehandelt
**Authentifizeirung:**  ich habe das passwort für den Benutzer "sshuser" eigegeben , und der server hat den Zugriff gewährt.
**Shell-Zuweisung:**  ich habe Zugriff auf eine entfernte Shell-sitzung auf dem Zielgerät erhalten
**Beenden:** ich habe "exit" eigegeben, um die Sitzung ordnungsgemäß zu meinem lokalen Terminal zurückzukehren  
```

---

### Task 2: Ed25519 Key Pair

**Objective:** Create a secure key pair and explain how digital signatures verify identity.

1. Generate an Ed25519 SSH key pair:

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   * Accept the default file location (`~/.ssh/id_ed25519`). Or provide the `-f <filepath>` option additionally.
   * Enter a passphrase when prompted (optional).
2. Locate and inspect your `id_ed25519` (private key) and `id_ed25519.pub` (public key).
3. Install your key on the remote machine (e.g. `vorlesungsserver`.
4. Explain in writing:

   * How the **private key** is used to sign challenges.
   * How the **public key** on the server verifies signatures without revealing the private key.
   * Why Ed25519 is preferred (performance, security).

**Provide:**

```bash
# 1) The ssh-keygen command you ran
ssh-keygen -t ed25519 -C "manue@example.com"
# 2) The file paths of the generated keys
\Users\sshuser/.ssh/id_ed25519
# 3) Your written explanation (3–5 sentences) of the signature process
Der private Schlüssel wird verwendet, um eine Herausforderung(Nachtricht) zu signieren, die vom server wahrend der ssh-Authentifierung erstellt wird. Der server überpruft die Signatur mit dem öffentlichen Schlüssel, ohne den privaten Schlüssel preiszugeben, was durch asymmetrische Kryptographie ermöglicht wird. Ed25519 wird bevorzugt, weil es eine hohe Sicherheit bietet, schnell und widerstandsfähig gegenüber modernen Angriffen ist, während es gleichzeitig eine hohe Leistung und Fehlerresistenz gewährleistet
 ```

---

### Task 3: SSH Config File

**Objective:** Simplify SSH commands via `~/.ssh/config`.

1. Open (or create) `~/.ssh/config` in `vim`.
2. Add entries for your hosts, for example:

   ```text
   Host my-remote
       HostName remote.example.com
       User youruser
       IdentityFile ~/.ssh/id_ed25519

   Host backup-server
       HostName backup.example.com
       User backupuser
       Port 2222
       IdentityFile ~/.ssh/id_ed25519_backup
   ```
3. Save and close the file, then test:

   ```bash
   ssh my-remote
   ssh backup-server
   ```
4. Explain:

   * How SSH reads `~/.ssh/config` and matches hosts.
   * The difference between `HostName` and `Host`.
   * How aliases prevent long commands.

**Provide:**

```text
# 1) The full contents of your ~/.ssh/config
Host my-remote
    HostName 192.168.0.102
    User manue
    IdentityFile ~/.ssh/id_ed25519

Host backup-server
    HostName 192.168.0.102
    User sshuser
    Port 2222
    IdentityFile ~/.ssh/id_ed25519_backup
# 2) A short explanation (3–4 sentences) of how the config simplifies connections

Die Datei ~/.ssh/config ermöglicht es, Benutzerdefinierte Aliase für jeden Remote-Host zu erstellen. SSh liest diese Datei, um den Alias mit den entsprechenden Serverinformationen wie HostName, User und IdentityFile abzugleichen. Dardurch entfällt die Notwendigkeit, lange  ssh-Befehle einzugeben, da du einfach ssh my-remote oder ssh backup-server verwenden kannst.
```

---

### Task 4: SCP File Transfers

**Objective:** Practice copying files securely using `scp`.

1. **Local → Remote**:

   ```bash
   scp /path/to/localfile.txt youruser@remotehost:~/destination/
   ```
2. **Remote → Local**:

   ```bash
   scp youruser@remotehost:~/remotefile.log ./local_destination/
   ```
3. **Remote → Remote** (between two directories on the same remote host):

   ```bash
   scp -r youruser@remotehost:/path/dir1 youruser@remotehost:/path/dir2
   ```
4. For each command:

   * Verify file timestamps and sizes after transfer, using `ls -la`
   * Note any flags you used (e.g., `-r`, `-P` for port).
5. Explain:

   * How `scp` initiates an SSH session for each transfer.
   * The role of encryption in protecting data in transit.

**Provide:**

```bash
# 1) Each scp command you ran

**locale → remote
scp C:/Users/sshuser/Documents/test.txt sshuser@192.168.0.102:~/destination/


**remote → locale
scp sshuser@192.168.0.102:~/log.txt C:/Users/sshuser/Downloads/


**Remote → Remote
scp -r sshuser@192.168.0.102:/home/sshuser/dir1 sshuser@192.168.0.102:/home/sshuser/dir2


# 2) Any flags or options used
-r: um Verzeichnisse rekursiv zu kopieren
-p: gibt den port an, falls der ssh-Server auf einem anderen als ddem standardport (22) läuft

# 3) A brief explanation (2–3 sentences) of scp’s mechanism

SCP verwendet SSh im hintergrund, um eine sichere Verbindung zwischen den host herzustellen. fur jede Übertragung wird eine neue SSH- Sitzung gestartet, die den Benutzer authentifiziert und den Dateitransfert sicher durchführt
```

---

### Task 5: Login Shell Script & Profile Explanation

**Objective:** Automate commands at login and understand shell initialization files.

1. On the **remote** server, create a script `~/login_tasks.sh` containing at least three commands you find useful (e.g., `echo "Welcome $(whoami)"`, `uptime`, `ls ~/projects`). You may either use `vim` or try the following to create a file from your commandline directely:

   ```bash
   cat << 'EOF' > ~/login_tasks.sh
   #!/usr/bin/env bash
   echo "Welcome $(whoami)! Today is $(date)."
   uptime
   ls ~/projects
   EOF
   chmod +x ~/login_tasks.sh
   ```

> The files content should be something akin to:
> ```bash
> #!/usr/bin/env bash
> echo "Welcome $(whoami)! Today is $(date)."
> uptime
> ls ~/projects
> ```

2. Append to your `~/.bashrc` (or `~/.profile` if using a login shell) a line to source this script on each new session:

   ```bash
   echo "source ~/login_tasks.sh" >> ~/.bashrc
   ```
3. Log out and log back in to trigger the script.
4. Explain:

   * The difference between `~/.bashrc` and `~/.profile` (interactive vs. login shells).
   * Why and when each file is read.
   * How sourcing differs from executing.

**Provide:**

```bash
# 1) The contents of login_tasks.sh
#!/usr/bin/env bash
echo "Welcome $(whoami)! Today is $(date)."
uptime
ls ~/projects

# 2) The lines you added to ~/.bashrc or ~/.profile
**echo "source ~/login_tasks.sh" >> ~/.bashrc**
# 3) Your explanation (3–5 sentences) of shell init files and sourcing vs. executing
~/.bashrc wird bei interaktiven shells geladen
~/.profile wird bei login-shells
-source führt das skript im aktuelle shell aus

```

---

**Remember:** Stop working after **90 minutes** and record where you stopped.
