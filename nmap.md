nmap -sS target.com
ใช้ scan port open/close
nmap -sS -sV target.com
ใช้ scan port open/close และ version
nmap -sS -sV --script vuln target.com
ใช้ scan แสดงช่องโหว่
ืnmap -sA target
ใช้แสดงว่ามี firewall หรือไม่แต่ไม่ได้โชว์ open/close

Recon เบื้องต้น (default scripts):
sudo nmap -sS -sC -sV target
ตรวจหาช่องโหว่เชิงลึก (vuln scripts):
sudo nmap -sS -sV --script vuln target
ถ้าต้องการข้อมูลเชิงลึกมากขึ้นและยอมรับความดัง/ความเสี่ยง:
sudo nmap -A --script vuln,target -p22,80,443 target

-sV 
ให้ Nmap ตรวจว่า service ที่พอร์ต 80 เป็นอะไร เช่น:

nginx 1.18

Apache httpd 2.4.7

Microsoft IIS 10

หรือแม้แต่ custom service

-sC
ให้รันสคริปต์ NSE แบบ default เช่น:

http-title (หาชื่อเว็บ)

http-server-header (header ของ server)

http-methods (อนุญาต method อะไรบ้าง เช่น GET, POST, PUT)

banner check

และสคริปต์พื้นฐานอื่น ๆ

เหมือน “ตรวจเบื้องต้นโดยอัตโนมัติ”

= เหมือน --script=default