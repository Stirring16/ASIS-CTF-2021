# Gesture

> A gesture is a file that is intended to indicate or emphasize something!

> Note: flag is lowercase string, put the flag in ASIS{flag} too.

> [gesture.img](https://drive.google.com/file/d/1urlIw5rfbdu_jL7cDjLQhhdMGiUzzaJa/view?usp=sharing)

# Analyze

Thử thách này cho mình một file `gesture.img`, dùng binwalk xem bên trong nó chứa gì

![image](https://user-images.githubusercontent.com/62060867/140382447-112d1be3-a9b2-446c-a3f4-dacdee51d9db.png)

 `YAFFS filesystem`, dường như nó là mấu chốt của vấn đề.
 
 Dựa vào keyword `YAFFS` mình có thể trích xuất file `gesture.img1` với công cụ [yaffs2utils](htps://github.com/justsoso8/yaffs2utils) với lệnh:
 
 ```
 unyaffs2

	./unyaffs2 [-h] [-e] [-p pagesize] [-s sparesize] [-o oobimg] [-f file] imgfile dirname
 ```
 
![image](https://user-images.githubusercontent.com/62060867/140385891-75dfa8e8-b2c1-4826-8390-e95a6a7fac60.png)

Sau khi unyaffs2 mình đã có xuất được tất cả YAFFS filesystem

```
┌──(stirring㉿Stirring)-[/mnt/c/Users/thanh/Desktop/ASISCTF/gesture/gesture]
└─$ tree
.
├── ADM
├── Alarms
├── Android
│   └── data
│       ├── com.android.gallery3d
│       │   └── cache
│       ├── com.android.vending
│       │   └── files
│       │       └── installer
│       ├── com.apkpure.aegon
│       │   ├── cache
│       │   │   ├── asset_info_cache
│       │   │   │   ├── 1c286544f8a4db5ecae0c96d5c758087.icon
│       │   │   │   ├── 1c286544f8a4db5ecae0c96d5c758087.info
│       │   │   │   ├── 4deaa020a5fe5b43a62376ac54766628.icon
│       │   │   │   ├── 4deaa020a5fe5b43a62376ac54766628.info
│       │   │   │   └── ff7f7187d501356839db12aafaa5906c.info
│       │   │   ├── download_cache
│       │   │   ├── http_cache
│       │   │   │   ├── 0409c987723620692eb2446719bfde9e.0
│       │   │   │   ├── 0409c987723620692eb2446719bfde9e.1
│       │   │   │   ├── 0492b1230d433a618f1b841487c55742.0
│       │   │   │   └── journal
│       │   │   ├── temp_apk
│       │   │   └── UnityAdsCache
│       │   │       ├── UnityAdsCache-377d9c7a12e2c729a44f402b1e72795fb71dd715a99e2d425dc47f969e416b2f.mp4
│       │   │       ├── UnityAdsCache-cd608877e203e6427d3213ebde497160cf4c1c0f23abe2e006eb01d3f2744da3.mp4
│       │   │       └── UnityAdsWebApp.html
│       │   └── files
│       │       └── Installer
│       │           ├── com.adhoclabs.burner
│       │           ├── com.appsverse.phoner
│       │           ├── com.dv.adm
│       │           ├── com.hushed.release
│       │           ├── com.rarlab.rar
│       │           ├── org.thoughtcrime.securesms
│       │           └── ws.coverme.burnerline
│       ├── com.dv.adm
│       │   └── files
│       ├── com.google.android.gms
│       │   └── files
│       ├── com.google.android.googlequicksearchbox
│       │   └── files
│       │       ├── download_cache
│       │       └── pending_blobs
│       ├── com.google.android.youtube
│       │   ├── cache
│       │   │   ├── cronet_media_cache
│       │   │   └── exo
│       │   └── files
│       ├── com.rarlab.rar
│       │   └── files
│       └── org.thoughtcrime.securesms
│           ├── cache
│           └── files
├── DCIM
├── Documents
├── Download
│   └── RAR_7b077a91.apk
├── Movies
├── Music
├── Notifications
├── Pictures
├── Podcasts
├── Ringtones
└── Signal
    └── Backups
        └── signal-2021-10-21-08-01-13.backup
```

Sau khi dùng lệnh `tree` để xem tổng quát, mình thấy được 1 dữ liệu quan trong 

`gesture\Signal\Backups\signal-2021-10-21-08-01-13.backup`

Ta cần backup-decode file này

Với `Google` dễ dàng mình có  có được [signal-backup-decode](https://github.com/pajowu/signal-backup-decode) 

```
signal-backup-decode [FLAGS] [OPTIONS] <INPUT> <--password <PASSWORD>|--password-file <FILE>|--password-command <COMMAND>>
```

Để run `signal-backup-decode` mình cần có password, tiếp tục tìm kiếm mình phát hiện được: `Android/data/com.android.gallery3d/cache/.nomedia/.latentorism` 

![image](https://user-images.githubusercontent.com/62060867/140392732-9397325a-0700-48d3-aac5-b43716dad86c.png)

`.latentorism` thật chất là `JPEG image` nên mình đổi tên của nó để xem được ảnh.

![st](https://user-images.githubusercontent.com/62060867/140394895-eb65b523-450f-4c3b-9372-fec87cd50474.jpg)


Có vẻ tấm ảnh bị lật, để dễ nhìn hơn mình viết nhanh script để lật hình lại (https://ichi.pro/vi/5-ky-thuat-thao-tac-hinh-anh-huu-ich-bang-python-opencv-88324170610671)

```
import cv2
image = cv2.imread("st.jpg")
flip = cv2.flip(image, 1)
cv2.imwrite("st.jpg", flip)
```
![st](https://user-images.githubusercontent.com/62060867/140394647-bbf4fd06-8717-43e3-91c5-308e74e8f719.jpg)

`70900 49992 98182 87494 78427 17955`

Ok password đã có, tiến hành `backup-decode`

![image](https://user-images.githubusercontent.com/62060867/140454017-ecba03b3-e947-4051-8dc9-3114a244a8f5.png)

Sau khi `backup-decode` mình có được file `signal_backup.db`

Mình sử dụng [SQLite View](https://sqliteonline.com/) để xem dữ liệu

![image](https://user-images.githubusercontent.com/62060867/140454503-07091237-a473-477c-8272-676fb8effea1.png)

Sau khi xem qua 1 số table mình thấy có `sms`

```
SELECT * FROM sms;
```

![image](https://user-images.githubusercontent.com/62060867/140454719-1fe2a794-399b-4e44-89b2-a59c482c2ca2.png)
 
Ở `id 83` mình thấy được 1 chuỗi `morse code` 
```
-. ...-- ...- . .-. ..--.- --... .-. ..- ..... - ..--.- - .---- -. . ..--.- ... . ...-- ..--.- .. ..--.- ....- ..--.- -. ----- - .... .---- -. -.... ..--.- .. ..... ..--.- .-.. ----- ----- ..--.- .--. ...-- .-. -.-. . -. --... ..--.- ... ...-- -.-. ..- .-. .

Opportunity is missed by most people because it is dressed in overalls and looks like work. --Thomas Edison
```

Mình decode bằng https://www.dcode.fr/morse-code

![image](https://user-images.githubusercontent.com/62060867/140455126-a56a4cc8-3bc6-451b-a8a7-dbdf801888f7.png)

Ohh Thank God You're Here:   `ASIS{n3ver_7ru5t_t1ne_se3_i_4_n0th1n6_i5_l00_p3rcen7_s3cure}`





