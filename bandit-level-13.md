OverTheWire: Bandit — Level 13 → Level 14
🎯 Objective
The password for Level 14 is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. Instead of a password, the home directory of bandit13 contains a private SSH (Secure Shell) key. The goal is to use that key to log in as bandit14 and retrieve the password.

📚 Concepts Covered
ConceptExplanationSSH key-based authenticationA method of logging into a server using a cryptographic private key instead of a passwordPrivate keyThe secret half of an SSH key pair — held by the user and never sharedPublic keyThe server-side half — verifies the private key without exposing itssh -iAn SSH flag that specifies an identity file (private key) to use for loginchmod 600Sets file permissions so only the owner can read and write — mandatory for SSH to accept a private key/etc/A system-level directory holding configuration and sensitive files, separate from the home directory

🛠️ Tools Used

ssh — Secure Shell, remote login tool
cat — display file contents
chmod — change file permissions
> — output redirection operator


🔐 Login
bashssh bandit13@bandit.labs.overthewire.org -p 2220
Password: (password obtained from Level 12)

📝 Walkthrough
Step 1 — Confirm the private key exists:
bashls
You will see sshkey.private in the home directory.

Step 2 — Open a new local terminal and pull the key directly to your machine:
bashssh bandit13@bandit.labs.overthewire.org -p 2220 "cat ~/sshkey.private" > bandit14.key

⚠️ OverTheWire blocks SSH connections originating from localhost to conserve resources. The key must be copied to your local machine and used from there — not from within the bandit server.

Breaking down the command:
PartMeaning"cat ~/sshkey.private"Runs this command on the remote server and sends output back> bandit14.keyRedirects that output into a new local file called bandit14.key

Step 3 — Secure the key file:
bashchmod 600 bandit14.key
SSH will refuse to use a private key that is readable by others. chmod 600 restricts access to the owner only. This mirrors real-world practice — AWS enforces the same rule with .pem key files.

Step 4 — Log in as bandit14 using the key:
bashssh -i bandit14.key bandit14@bandit.labs.overthewire.org -p 2220

Step 5 — Retrieve the password:
bashcat /etc/bandit_pass/bandit14

Note: /etc/bandit_pass/bandit14 is not in your home directory. Plain ls will not show it. Use the full path to access it directly.


💡 Key Takeaways

SSH key authentication is more secure than passwords — there is nothing to brute-force or phish.
A private key is useless if its permissions are too open — chmod 600 is non-negotiable.
Files outside your current directory are accessed by their full absolute path, not ls.
The > operator is a clean alternative to opening a text editor when writing content to a file.


✅ Summary
StepActionCommand1Pull key to local machinessh bandit13@... "cat ~/sshkey.private" > bandit14.key2Secure the key filechmod 600 bandit14.key3Log in as bandit14ssh -i bandit14.key bandit14@...4Read the passwordcat /etc/bandit_pass/bandit14

Written by NullScalpel — dual-tracking medicine and cybersecurity, one level at a time.
