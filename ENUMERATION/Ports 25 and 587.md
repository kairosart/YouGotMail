
## Port 25

This port doesn't respond to AUTH LOGIN, so you can't do anything there.

## Port 587

#Attacking_machine
1. Connect with **netcat**:
    `nc 10.10.63.223 587`
	You should see:
	`220 BRICK-MAIL ESMTP`

2. Say hello:
    `EHLO test.com`
	Server should respond with something like:
	`250-BRICK-MAIL 250-SIZE 20480000 250-AUTH LOGIN 250 HELP`

You won't see response.

### Possible reasons for "no response"

**Line endings**  
    SMTP requires `\r\n` (carriage return + newline).  
    If you’re using `nc` or `telnet` and just hitting Enter, sometimes only `\n` is sent.

Use plain `nc` but forcing CRLF:

```
{ printf "EHLO test.com\r\n"; sleep 1; } | nc 10.10.63.223 587
```

![[Ports 25 and 587-20250824204235261.webp]]

Now, if you type AUTH LOGIN you won't have any response either.

### Why this matters

If you only send `AUTH LOGIN\n`, the server just sits there, waiting for the proper `\r\n`. That’s why you see “no response”.

---

## swaks

To save yourself the hassle, you can also use **swaks** (if installed):

```
swaks --server <MACHINE IP> --port 587 --auth LOGIN
```

This handles all the CRLF endings and base64 automatically.

You'll be asked for:
(This is only a random response)

![[Ports 25 and 587-20250824205553180.webp]]

![[Ports 25 and 587-20250824205731053.webp]]

Now you have a **complete exchange**, and it explains exactly what’s happening:

334 VXNlcm5hbWU6        ← Server asking for username (base64) 
YWRtaW4=               ← You sent "admin" (base64) 
334 UGFzc3dvcmQ6        ← Server asking for password (base64) 
cGFzc3dvcmQ=            ← You sent "password" (base64) 
535 Authentication failed. ← Password (or username) is wrong 
221 goodbye              ← Connection closed`

### Analysis

1. **AUTH LOGIN works** ✅
    
    - The server responds with `334 …` prompts correctly.
        
    - This proves **port 587 is fully functional** for SMTP authentication.
        
2. **Login failed** ❌
    
    - Either the username `admin` or password `password` is not valid.
        
    - You need either a correct combination from a list or a different username.
        
3. **No server misconfiguration**
    
    - This is **normal behavior**.
        
    - Nmap showed `AUTH LOGIN` correctly, and your netcat/swaks test confirms it.

