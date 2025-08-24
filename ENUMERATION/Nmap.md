Start looking for opened ports and info about the machine.

```
nmap -A <MACHINE IP>
```

![[Nmap-20250822201410758.webp]]

## Ports opened

PORT     STATE SERVICE
25/tcp   open  smtp
110/tcp  open  pop3
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
143/tcp  open  imap
445/tcp  open  microsoft-ds
587/tcp  open  submission
3389/tcp open  ms-wbt-server

There two ports having to do with email.

- **25 (SMTP)** → traditionally for server-to-server mail transfer.

- **587 (submission)** → for _client-to-server_ mail submission (like when your email client sends mail through your provider).
	- - It’s the **modern standard port for sending outgoing email** (replacing port 25, which is often blocked or limited by ISPs).
	- Requires **authentication (AUTH LOGIN, AUTH PLAIN, etc.)** before relaying mail.
	- Often uses **STARTTLS** to upgrade to an encrypted connection.

**Next step:** [[Web server]]

