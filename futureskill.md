1 ทำความรู้จัก Ethical Hacking
- white_hat แฮกเกอร์ที่ทำตามกฎหมาย,gray_hat แฮกเกอที่แอบเจาะระบบโดยไม่แจ้ง,black_hat เจาะระบบเพื่ออาชญากรรม
2 penetration testing ทำเพื่ออะไร
- เพื่อหาช่องโหว่ ก่อนที่จะมี black hat hacker มาเจาะระบบเรา
3 การสืบค้นข้อมูล Reconnaissance
- search หาข้อมูลเป้าหมายจาก google social
- nmap ตรวจสอบ port,service,OS
    Passive Recon – เก็บข้อมูลสาธารณะก่อน เช่น WHOIS, Subdomains, DNS, Social Media
    Active Recon – สแกนระบบ เช่น Port Scanning, Banner Grabbing, Directory Fuzzing
    Analyze & Correlate – นำข้อมูลมาวิเคราะห์เพื่อวางแผนโจมตีขั้นต่อไป
4 OWASP top 10 ช่องโหว่ที่มักพบเจอในเว็บไซต์
 -sql injection โจมตีฐานข้อมูลจากช่องโหว่ sql (ใช้ payload พื้นฐาน ' OR 1=1--)
 -การเปลี่ยน path: /user/1 -> /user/2
 -ดู Traffic ผ่าน Burp Suite → เช็คข้อมูลหลุด
 -insecure design ตรวจสอบ logic เช่น ลืมใส่ 2FA, ไม่มี input validation
 -ตรวจ default password admin:admin
 -Outdate ใช้ Library/Framework เก่า มีช่องโหว่
 -authentication failed ทดสอบ brute force, session hijacking
 -software failures ไม่มีการตรวจสอบความถูกต้องของโค้ดหรือข้อมูล
 -Monitoring Failures พยายาม brute force → ดูการตอบสนอง
 -Server-Side Request Forgery (SSRF) โจมตีให้ Server ส่ง request ไปที่อื่น
5 ลงมือทำ Penetration testing
- burp suite ดัก request
- burp suit ทำ brute force password
- Sensitive Information Disclosure in url
- การใส่ username ผิดๆเพื่อดู log ทำ username enumerate
- store xxs attack เช่น  <a onmouseover="alert=document.cookie">xxs attack</a>
- reflected xxs attack หลอกให้กด link ที่มี script ต่อท้าย เช่น https://learn.futureskill.co/learning/watch/127/<script>alert('XSS')</script>
6 การเขียนรายงาน report
7 mobile penetration testing
- การที่ app เก็บ log ข้อมูลในเครื่อง 