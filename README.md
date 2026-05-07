# dns 서버 
su -

(비밀번호 입력하기)

apt –y install bind9 bind9utils

cd /etc/bind

mv named.conf.options named.conf.options.org

cp named.conf.options.org named.conf.options

vi named.conf.options

:set nu

(21번줄 내용 지우고 그부분부터 아래 코드 작성)

```
dnssec-validation no;
recursion yes;
allow-query { any; };

forwarders {
	8.8.8.8;
	1.1.1.1;
};
forward only;
listen-on-v6 { any; };
```
(위에 코드 따라쓰고 esc 눌러서 명령모드로 돌아온 뒤) :wq

ifconfig ens33 <- ip 주소 확인

(만약 ifconfig 명령어가 없다고 뜨면 apt install net-tools 후 다시 ifconfig ens33)

vi /etc/resolv.conf

(기존 nameserver 적혀있는 부분 주석 처리하세요 -> 주석 처리 방법은 줄 맨앞에 # 붙이기)

```
nameserver {방금 전 실행한 ifconfig ens33에서 나온 ip 주소}
```
(nslookup server 해서 한번 확인하기)

named-checkconf /etc/bind/named.conf.options

systemctl restart named

systemctl enable named

systemctl status named <- 서버 상태 확인

# 마스터 서버
cd /etc/bind

vi named.conf.local

(맨 아래에 코드 추가)

(중요: ** 만약 woojak.com이 아닌 다른 도메인을 쓰고 싶으면 앞으로 나올 모든 woojak.com을 본인이 원하는 도메인으로 바꿔주면 됨 **)
```
zone "woojak.com" IN {
        type master;
        file "/etc/bind/db.woojak.com";
    };
```
(이후 esc 눌러서 명령모드로 돌아온 뒤 저장)
:wq

cp db.local db.woojak.com

vi db.woojak.com

(첨부파일 내용 따라 쓰기)
(첨부파일에서 sy.com을 본인 도메인으로 바꿔서 작성 + ip 주소도 본인 서버 ip 주소로 바꿔서 작성)

named-checkzone woojak.com /etc/bind/db.woojak.com (오타 확인용 OK 뜨면 잘 된거 👍)

# 웹 서버
apt -y install apache2

systemctl restart apache2

systemctl enable apache2

https://{본인 아이피}들어가서 확인하기

cd /var/www/html

vi index.html
