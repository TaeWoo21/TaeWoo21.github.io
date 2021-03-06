---
layout: post
categories: posts
title: OpenTSDB Install-(3) Linux에서 OpenTSDB 설치
comments: true
---

이 문서에서는 OpenTSDB를 git repository에서 clone 받아 설치하는 방법부터 configure 파일을 이용한 OpenTSDB 설정법, 마지막으로 OpenTSDB가 제대로 설치되었는지 확인하는 방법까지 설명한다.

이 문서는 기본적으로 [OpenTSDB 설치 Document](http://opentsdb.net/docs/build/html/installation.html)를 참고하여 작성하였다. 또한 Linux 배포판 중의 하나인 Ubuntu를 기준으로 작성하였다.


## [ OpenTSDB 구동 및 설치 조건 확인 ]
- Linux 시스템 (~~Widows에서도 manual building으로 구동할 수 있다고는 하나, 이 문서에서는 다루지 않는다.~~)
- JDK(Java Development Kit) 1.6 이상
- HBase 0.92 이상
- GnuPlot 4.2 이상
- Autotools (autoconf, automake, libtool)
- Make
- Python
- Git
- 인터넷 연결

<br/>

이전의 과정에서 JDK 설치 방법과 HBase 설치 방법을 자세히 다루었으므로 이 문서에서는 생략하도록 한다. 
<br/><br/>

패키지들을 설치하기 전에, 패키지 리스트를 업데이트 시켜준다.

```
sudo apt-get update
```

GnuPlot 설치

```
sudo apt-get install gnuplot
```

GNU 빌드시스템(GNU build system), 간단히 얘기해서 Autotools 설치

```
sudo apt-get install autoconf automake libtool
```

Make 설치

```
sudo apt-get install make

# 꼭 필요는 없지만 make와 함께 사용되는 다른 툴들도 함께 설치하는 것을 추천
sudo apt-get install build-essential
```
<br/>
Linux라면 Python은 기본적으로 설치되어 있을테니까 Pass
<br/><br/>

Git 설치

```
sudo apt-get install git
```

## [ OpenTSDB 다운로드 및 설치 ]
OpenTSDB를 설치하기 위해 만반의 준비를 다 했으니, 이제 본격적으로 설치를 진행해보도록 하자.

<p align="center">
	<img src="../../assets/img/post/install_opentsdb_3_opentsdb.png" alt="opentsdb img"/>
</p>

### 1. Download (git clone)

OpenTSDB를 clone 해 올 위치로 이동한다.

```
cd /usr/local
```

OpenTSDB repository에서 clone 해온다. 그러면 현재 디렉토리 내에 opentsdb라는 폴더가 생성된다. 필자의 경우 /usr/local/opentsdb 폴더가 생성되었다.

```
sudo git clone git://github.com/OpenTSDB/opentsdb.git
```

### 2. Install

git clone으로 생성된 폴더로 이동하여 build.sh 파일을 실행시킨다. 만일 다음 코드가 정상적으로 실행되었다면, 아래의 사진처럼 결과가 나올 것이다.

```
cd /usr/local/opentsdb
sudo /usr/local/opentsdb/build.sh
```

![build.sh success](../../assets/img/post/install_opentsdb_3_build_success.png)

### 3. Configurations

OpenTSDB에서 설정해주어야 할 것은 크게 세가지가 있다.  opentsdb.conf 파일수정, OpenTSDB의 스크립트를 이용하여 HBase에 테이블 생성하기, 그리고 시스템 시간 설정해주기. configuration은 대부분 [http://opentsdb.net/docs/build/html/user_guide/configuration.html](http://opentsdb.net/docs/build/html/user_guide/configuration.html) 이 페이지 참고.

#### 3.1 opentsdb.conf 파일 수정

먼저 opentsdb.conf 파일을 수정해보자.  파일의 내용 중 각각에 해당하는 내용을 찾아서 '(변경후)' 내용으로 수정하면 된다. 

```
cd /usr/local/opentsdb/src
sudo vi /usr/local/opentsdb/src/opentsdb.conf
```

(Required) OpenTSDB가 연결될 포트 번호를 지정해준다.

```
(변경 전)
# --------- NETWORK ----------
# The TCP port TSD should use for communications
# *** REQUIRED ***
tsd.network.port =

->

(변경 후)
# --------- NETWORK ----------
# The TCP port TSD should use for communications
# *** REQUIRED ***
tsd.network.port = 4242
```

(Required) OpenTSDB web UI의 javascript 파일 등의 static file들이 있는 위치를 찾아서 지정해준다. 필자의 경우에는 아래의 사진과 같은 파일들이 담긴 staticroot 폴더가 /usr/local/opentsdb/build/staticroot 위치에 있었다. 그리고 OpenTSDB의 캐시파일이 저장될 위치를 지정해준다.

```
(변경 전)
# ----------- HTTP -----------
# The location of static files for the HTTP GUI interface.
# *** REQUIRED ***
tsd.http.staticroot =

# Where TSD should write it's cache files to
# *** REQUIRED ***
tsd.http.cachedir =

->

(변경 후)
# ----------- HTTP -----------
# The location of static files for the HTTP GUI interface.
# *** REQUIRED ***
tsd.http.staticroot = /usr/local/opentsdb/build/staticroot

# Where TSD should write it's cache files to
# *** REQUIRED ***
tsd.http.cachedir = /tmp/opentsdb
```

![staticroot files](../../assets/img/post/install_opentsdb_3_staticroot.png)

(Optional) 새로운 metric을 입력받았을 때, 자동으로 OpenTSDB에 생성해줄 수 있도록 수정해준다. 이는 Default 값이 False로 되어있다. 이유는 외부로부터 알 수 없는 데이터나 공격을 받는 것을 막아주기 위해 있는 기능인 듯 하다. 우리는 테스트를 보다 간단하게 하기 위해 그냥 True로 바꿔준다.

```
(변경 전)
# --------- CORE ----------
# Whether or not to automatically create UIDs for new metric types, default
# is False
#tsd.core.auto_create_metrics = false

->

(변경 후)
# --------- CORE ----------
# Whether or not to automatically create UIDs for new metric types, default
# is False
tsd.core.auto_create_metrics = true
```

(Optional) 자동으로 데이터베이스에서 압축이 일어나지 않도록 수정한다. OpenTSDB에 입력하는 데이터의 양이 많지 않을 경우는 상관 없지만, 짧은 시간안에 많은 양의 데이터를 OpenTSDB에 입력하려고 할 경우에는 문제가 생길 가능성이 있다. 데이터 압축에 사용되는 리소스 때문에 JVM 환경에 따라 STW('Stop The World') 상황이 발생할 가능성이 높아지고 결국 문제가 발생하게 되는 것이다. 자동으로 압축이 일어나도록 설정하면 저장되어 있는 데이터를 쿼리할 때 속도가 빨라지겠지만, 위와 같은 상황에는 문제가 발생할 가능성이 크다.

```
(변경 전)
# --------- STORAGE ----------
# Whether or not to enable data compaction in HBase, default is True
#tsd.storage.enable_compaction = true

->

(변경 후)
# --------- STORAGE ----------
# Whether or not to enable data compaction in HBase, default is True
tsd.storage.enable_compaction = false
```

(Optional) 이제부터는 기존 파일에 없는 내용을 추가로 적어서 수정해야 하는데, 이 수정 내용들은 필자의 경험상 필요했던 내용들이다. 'tsd.storage.fix_duplicates = true'는 데이터가 덮어씌어졌을 때, 최신의 값으로 유지하는 것이다 'tsd.http.request.enable_chunked = true'는 한번에 여러 개의 Data Point를 저장할 수 있도록 허용하는 것이다. 'tsd.http.request.max_chunk = 100000000'는 HTTP Request의 최대 바디 사이즈를 늘려주는 것이다. 여기서 특정값인 100000000은 특별한 의미가 없고 필자가 큰 숫자를 아무렇게나 입력해 넣은 것이다.

```
(추가)
# --------- USER ADDED ----------------------------------
# Accpet the last written value when parsing data points with duplicate timestamps
tsd.storage.fix_duplicates = true

# Enable incoming chunk support for the HTTP RPC
tsd.http.request.enable_chunked = true

# The maximum request body size to support for incoming HTTP request 
tsd.http.request.max_chunk = 100000000
```

#### 3.2 OpenTSDB의 스크립트를 이용하여 HBase에 테이블 생성

일단 start-hbase.sh라는 쉘스크립트를 통해 HBase를 실행시킨다. 필자의 경우 아래의 경로에 HBase 실행 파일이 있다.

```
cd /usr/local/hbase/hbase-2.1.2/bin
sudo /usr/local/hbase/hbase-2.1.2/bin/start-hbase.sh
```

HBase를 실행시킨채로 create_table.sh라는 쉘스크립트를 통해 OpenTSDB 테이블을 생성한다. 이 작업은 OpenTSDB를 설치하는 맨 처음에 딱 한 번만 수행하면 된다. HBASE_HOME에는 HBase가 설치되어 있는 경로를 적어주면 된다.

```
cd /usr/local/opentsdb/src
env COMPRESSION=NONE HBASE_HOME=/usr/local/hbase/hbase-2.1.2 /usr/local/opentsdb/src/create_table.sh
```

아래의 사진과 같은 결과가 나온다면 성공적으로 OpenTSDB 테이블이 생성된 것이다

![create table success](../../assets/img/post/install_opentsdb_3_create_table.png)

#### 3.3 Ubuntu 시스템 시간 확인 및 수정

OpenTSDB에 Timestamp로 저장되어 있는 데이터를 OpenTSDB web UI로 쿼리하거나 나타낼때, 설정된 timezone에 맞춰서 표현된다. 똑같은 timestamp가 설정되어 있는 timezone에 따라 다른 시간으로 표현될 수 있는 것이다. OpenTSDB 설정 파일인 opentsdb.conf 파일에서 tsd.core.timezone 인자로 특별히 timezone을 설정하지 않는 이상 OpenTSDB가 설치된 시스템의 timezone이 default로 설정된다. 
<br/><br/>

이 문서에서는 한국 기준시(KST)에 맞게 설정하는 방법에 대하여 다루도록 하겠다. 만일 한국 기준시로 설정하고 싶다면, 아래와 같이 명령어를 입력했을 때 아래와 같은 결과가 나와야 한다. 아래의 결과에서 핵심은 원하는 기준인 'KST'가 포함되어 있는가이다.

```
taewoo@ubuntu:~$ date
Wed Jan 16 18:17:58 KST 2019
```

만일 한국 기준시로 설정하고 싶은데 결과가 위와 같지 않다면, 다음과 같이 따라하길 바란다. 우분투 환경의 경우 timezone을 변경하는 방법이 크게 두가지가 있다. 첫 번째는 간단하게 명령어 한줄로 변경하는 방법이다.

```
$ sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

다른 한가지 방법은 tzselect 명령어를 통해서 CUI를 통해 세팅하는 방법이다. 명령어를 입력한 뒤, 아래의 사진과 같이 Asia 대륙과 Korea(South)를 설정해준다.

```
taewoo@ubuntu:~$ tzselect
```

![create table success](../../assets/img/post/install_opentsdb_3_config_timezone2.png)

위의 사진에 나온 설명처럼 설정 후 설명처럼 home 디렉토리의 .profile 파일을 수정한다.

```
taewoo@ubuntu:~$ sudo vi ~/.profile
```

vi 에디터로 열린 .profile 파일의 맨 아래에 아래의 내용을 붙여넣는다.

```
TZ='Asia/Seoul'; export TZ
```

### 4. Check

참고로 OpenTSDB를 실행시키기 전에는 무조건 hbase_start.sh 쉘스크립트를 이용하여 HBase를 실행시켜야 한다. 실행시킨 뒤, 약 10초 정도 뒤에 OpenTSDB를 실행시켜주면 된다.
<br/><br/>

OpenTSDB를 실행시키기 위해 실행파일이 있는 위치로 이동한다. 그리고 해당하는 명령어를 입력한다. \-\-config 인자를 이용하여 3번 configuration에서 수정한 opentsdb.conf 파일의 위치를 알려준다.

```
cd /usr/local/opentsdb/build
sudo /usr/local/opentsdb/build/tsdb tsd --config=/usr/local/opentsdb/src/opentsdb.conf
```

명령어 입력 후 아래의 사진처럼 맨 아래에 'Ready to Serve on ...' 이라는 문구가 나오고 커서가 정지해 있다면 정상적으로 OpenTSDB가 구동된 것이다.

![OpenTSDB ready](../../assets/img/post/install_opentsdb_3_tsdb_ready.png)

이렇게 OpenTSDB를 구동한 뒤 http://localhost:4242/ 에 접속했을 때, 아래와 같이 OpenTSDB web UI가 나온다면 설치에 성공한 것이다.

![OpenTSDB web UI](../../assets/img/post/install_opentsdb_3_tsdb_webui.png)

