---
layout: post
title: 'Typora 이미지 업로드시 시놀로지 Nas로 업로드하자 (Typora + Synology + PicGo)'
subtitle: ''
date: 2023-02-13 21:08:44
categories: devlog
tags: typora 글또 issue synology
---

![Typora a markdown editor, markdown reader.](https://typora.io/img/icon_256x256.png)

# 계기

- (관련글) [Typora 이미지 업로드시 GitHub로 업로드하자 (Typora + GitHub + PicGo)](https://mintheon.com/devlog/2023/02/13/Typora-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%97%85%EB%A1%9C%EB%93%9C%EC%8B%9C-GitHub%EB%A1%9C-%EC%97%85%EB%A1%9C%EB%93%9C%ED%95%98%EC%9E%90-(Typora-+-GitHub-+-PicGo))

나는 Typora의 마크다운 에디터를 사용하며 이미지를 붙여넣기하면 자동 업로드 시켜주는 기능을 잘 사용하고 있었다.

![typora_이미지업로드_기능](https://swagger.synology.me:5543/uploads/2023/02/typora_%E1%84%8B%E1%85%B5%E1%84%86%E1%85%B5%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%A5%E1%86%B8%E1%84%85%E1%85%A9%E1%84%83%E1%85%B3_%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC.gif)

바로 요 기능인데 위 영상에서 보다싶이 로컬에서 복사된 이미지가 특정 이미지 서버의 url로 변경된 걸 볼 수 있다. 그래서 이 기능을 사용하면 **간편하게 로컬 이미지를 복사하여 에디터에 붙여넣기만해도 자동으로 특정 사이트에 업로드 해주어 나온 마크다운 하이퍼링크를 복사 붙여넣기 하면 어디서든 쓸 수 있다.**

나는 예전에 ipic이라는 라이브러리를 사용했고 지금은 ipic이라는 도메인으로 업로드가 되지만 기존의 경우 어떤 중국 이미지 사이트에 업로드 되었었다. 당연히 언제까지 이미지가 보존될지, 보안은 괜찮을지 걱정 됐지만 민감한 사진은 업로드 시키지 않는 방식으로 그냥 편하게 사용했는데...

![typora_이미지업로드_기능](https://swagger.synology.me:5543/uploads/2023/02/image-20230211212643545.png)

![유명한 불타는 피자배달짤 내용 유머게시판 퀘이사존](https://img2.quasarzone.co.kr/img/data/editor/1802/f8b678e6289e2e2fd8fcd658f0f7c546_1517706755_5442.gif)

**_띄용스.._**

위와 같이 블로그 내의 모든 사진이 없어지고 휴지조각이 되어버렸다. 이유는 기존에 제공하던 도메인에서 모든 이미지의 사용 접근 권한을 막은 것이다. 해결방법은 몇가지가 있는 것 같긴한데 쓸 수 없는 방법이라 좌절했다.

안그래도 사진 업로드도 불편하고 글을 쓰려면 레포를 clone해야하는 특성상 깃허브 블로그를 때려치고 티스토리로 가려고 했는데 나름 블로그 스킨도 아깝고 ~~휴지조각이 되었지만~~ 그나마 남은 글들도 아까워서 Typora의 업로드 로직을 변경하는걸로 합의했다.

---

<br />

# 방법

Typora의 업로드를 커스텀 하는 방식은 여러가지가 있다.

## 1. ipic, upic 등 이미지 업로드 라이브러리를 사용하는 방법

![image-20230213212550649](https://swagger.synology.me:5543/uploads/2023/02/image-20230213212550649.png)

기존에 내가 사용하던 방법이다.

- **장점**

  - Typora에서 나름 정식 지원을 해서 간편하게 선택하고 설치만 하면 끝이라 간편하다.

- **단점**
  - 업로드 서버가 대부분 중국이다.
  - 사진 제공이 언제까지 지원될지.. 알수없다.

## 2. GitHub Repository에 업로드 하도록 세팅하는 방법

요 방법도 사실 처음에 고려헀으나 GitHub 레포지터리 정책이 애매하여 패스했다. 하지만 개인 Nas가 없는 사람이라면 충분히 써도 괜찮을 듯 하다.

- **장점**

  - 세팅이 그리 어렵지 않다.
  - 깃허브 레포지토리에 사진이 업로드 되어 한눈에 볼 수 있고 관리가 나름 편하다.

- **단점**
  - 깃허브에서는 레포지터리 하나의 용량을 1GB ~ 5GB 정도로 유지하는것을 권장하는데 혹시나 그 이상의 용량이 되었을 경우 어떻게 될 지 모른다.

## 3. 개인 Nas에 업로드 하도록 세팅하는 방법

개인 Nas를 가지고 있는 나에게 최적의 방법이다.

- **장점**

  - 개인 Nas라 민감한 정보가 들어가도 상관없다. 보안적으로도 괜찮다.
  - 특정 폴더에 업로드 시켜 관리가 편하다.

- **단점**
  - 내 Nas 용량을 갉아먹는다.

그리하여 본인은 3번을 택했지만 2번이 궁금하신 분들도 있을테니 [깃허브로 업로드 하는 방법](https://mintheon.com/devlog/2023/02/13/Typora-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%97%85%EB%A1%9C%EB%93%9C%EC%8B%9C-GitHub%EB%A1%9C-%EC%97%85%EB%A1%9C%EB%93%9C%ED%95%98%EC%9E%90-(Typora-+-GitHub-+-PicGo))을 참고하기 바란다.

---

<br />

# 시놀로지 Nas로 Typora 이미지 업로드 하도록 세팅하기

이 방법은 간단하게 **시놀로지 Nas의 FTP 서버를 활용하여 업로드를 시키고, Web 서버를 활용하여 해당 정적 사진 리소스를 받아오도록 하는 방법**이다.

당연히 시놀로지 Nas가 아니더라도 다른 Nas들도 동일한 방식을 사용하면 사용가능하다는 점!

<br />

## 1. FTP 열기

![image-20230213214844418](https://swagger.synology.me:5543/uploads/2023/02/image-20230213214844418.png)

해당 메뉴에서 `FTP 서비스 활성화` 에 체크하고 포트번호와 포트범위를 위와 같이 설정한다. 포트번호가 5542인 이유는 차후에 업로드용으로 사용할 `picgo` 라는 라이브러리 설정과 맞추기 위함이다.

당연하지만.. 현재 내 네트워크에서 포트포워딩이 활성화 되어있다면 거기에 당연히 이번에 열려지는 포트들을 추가해줘야한다.

<br />

## 2. 웹서버를 위한 폴더 생성

![image-20230211174543318](https://swagger.synology.me:5543/uploads/2023/02/q40toc.png)

시놀로지의 제어판에 들어가서 공유 폴더 아이콘을 클릭한다.

![image-20230211174855148](https://swagger.synology.me:5543/uploads/2023/02/6ovbun.png)

나온 화면에서 `생성` 버튼을 눌러 `www/wwwroot/blog` 로 이어지는 폴더를 만들어준다.

![image-20230211180157210](https://swagger.synology.me:5543/uploads/2023/02/24j5t7.png)

만들게 되면 요렇게 나올것이다.

<br />

## 3. 웹서버 패키지 다운로드 및 설정

![image-20230211175022172](https://swagger.synology.me:5543/uploads/2023/02/flqxy8.png)

시놀로지 패키지스토어에서 `Web Station` 을 다운받아준다. 아주 쉽게 nas에 웹 서버를 구축해 주는 패키지이다.

![image-20230211175930822](https://swagger.synology.me:5543/uploads/2023/02/kl47to.png)

다운로드가 다 되었다면 실행시키고, `웹 서비스 포털` 로 들어가서 `생성` 버튼을 눌러 `서비스 포털 만들기` 를 눌러준다.

![image-20230211175959424](https://swagger.synology.me:5543/uploads/2023/02/45da1o.png)

그럼 이런 화면이 뜰텐데 우리는 `가상 호스트`를 선택한다.

![image-20230211233134013](https://swagger.synology.me:5543/uploads/2023/02/image-20230211233134013.png)

그러면 이런 화면이 뜰텐데 `포트기반`을 선택하고 포트는 `HTTPS`를 선택해준다. `HTTP`로 선택할 경우 이미지를 열려는 페이지가 https일 경우에 이미지가 뜨지 않는다.

문서 루트는 웹페이지의 root 경로로 사용이 되는데 이건 위에서 만들어준 폴더로 설정해준다.

<br />

## 4. 웹서버 테스트

자 그럼 위에서 설정해준 폴더 경로에 임의의 사진을 넣어 업로드 시킨다음에 웹에서 잘 불러오는지 테스트한다.

![image-20230211185523662](https://swagger.synology.me:5543/uploads/2023/02/3w9nc9.png)

나는 `www/wwwroot/blog` 하위에 `uploads` 라는 폴더를 만들어 그 안에다가 사진을 넣어두었는데 잘 불러와지는 것을 알 수 있다.

**요게 안된다면 설정이 제대로 잘 되었는지, 외부에서 접근이 가능하게 세팅을 해 놓았는지 네트워크 세팅을 확인**해야한다. _(위에서 설정한 5542, 5543, 4200~4300 포트가 다 열려있어야한다. 당연히 nas 자체도 외부접속이 가능하게 열려 있어야함.)_

<br />

## 5. FTP 업로드만을 위한 전용 계정 생성

귀찮으면 기존에 사용하는 admin 계정을 사용해도 된다. 뒤쪽에서 나오지만 로컬 파일에다가 기입할것이기 때문에 문제는 없다.

하지만 그냥 나는 프로 걱정러이기 때문에 따로 typora ftp 업로드를 위한 계정을 새로 생성했다.

![image-20230213222143207](https://swagger.synology.me:5543/uploads/2023/02/image-20230213222143207.png)

이미 만들어서 빨간색으로 뜨지만 유저명은 `ftpuser`로 설정했다.

![image-20230213222404211](https://swagger.synology.me:5543/uploads/2023/02/image-20230213222404211.png)
![image-20230213222500119](https://swagger.synology.me:5543/uploads/2023/02/image-20230213222500119.png)

**중요한 점은 `www` 공유폴더의 권한에 읽기/쓰기 권한을 주어야하고 FTP 서비스도 사용할 수 있게 허용해야한다.**

<br />

## 6. picgo-core 설치

단순히 이것만 한다고 자동으로 사진이 업로드 되는건 아니다.**사진 업로드와 url 생성을 도와줄 `picgo` 라는 친구를 설치할 것**이다.

이 프로그램은 Command 타입(picgo-core)과 GUI 타입(picgo)이 있는데 **후자의 경우 FTP 설정 지원이 되지 않아 전자를 선택**했다. **나는 mac을 사용하기 때문에.. windows는 따로 적지 않겠다..**

```bash
npm install -g picgo
```

yarn을 사용해도 된다. 혹시나 npm 명령어가 먹히지 않는다면 [nodejs를 최신버전으로 깔길 바란다](https://nodejs.org/en/).

이후에 잘 깔렸는지 확인하기 위해 `picgo --help` 를 추가적으로 쳐보고 잘 나온다면 ftp-uploader 플러그인을 다운한다.

```bash
picgo install picgo-plugin-ftp-uploader
```

설치가 완료되었다!

<br />

## 7. picgo 세팅

![image-20230213221507477](https://swagger.synology.me:5543/uploads/2023/02/image-20230213221507477.png)

Finder를 켜고 `Command + Shift + G` 를 누르면 폴더 이동창이 뜬다. 거기에 `~/.picgo`를 적어서 경로로 이동하자.

그러면 하위에 `config.json` 파일이 있을텐데 그걸 열어서 아래와 같이 설정해주자.

```json
{
  "picBed": {
    "uploader": "ftp-uploader",
    "current": "ftp-uploader",
    "ftp-uploader": {
      "site": "구분가능한 사이트이름",
      "configFile": "현재경로/ftpUploader.json"
    }
  },
  "picgoPlugins": {
    "picgo-plugin-ftp-uploader": true
  }
}
```

위의 `configFile`에 적어줬던 위치에 `ftpUploader.json`을 만들어 줘야하니 해당 경로에 파일을 생성하고 아래와 같이 적는다.

```json
{
  "구분가능한 사이트이름": {
    "url": "https://xxx.xxx.xx:5543",
    "path": "/uploads/{year}/{month}/{fullName}",
    "uploadPath": "/www/wwwroot/blog/uploads/{year}/{month}/{fullName}",
    "host": "xxx.xxx.xx",
    "port": "5542",
    "username": "ftpuser",
    "password": "ftpuser비번"
  }
}

```

_본인은 `blog` 폴더 하위에 `uploads` 폴더를 추가로 생성하였기 때문에 경로가 저렇다. 따로 폴더를 생성한게 아니라면 `uploads` 는 지우거나 대체할것!_

**저장 버튼을 누르고 세팅 끝!**

<br />

## 8. Typora 이미지 업로더 세팅

마지막 단계이다.

![image-20230211205319060](https://swagger.synology.me:5543/uploads/2023/02/image-20230211205319060.png)

`Typora` 의 환경설정을 열고 이미지 탭을 들어간다. 이후에 이미지 업로드 설정에서 `커스텀 명령어` 를 선택하고 command에 `picgo upload` 라고 적어준다.

![image-20230214001432273](https://swagger.synology.me:5543/uploads/2023/02/image-20230214001432273.png)

사실 위처럼 리스트에서 `picgo` 를 선택해도 되긴 하지만 FTP 처리 특성상 Test를 한번 해보는게 좋아서 커스텀으로 진행한다. 차후에 테스트 성공한다면 위처럼 `picgo` 를 선택해도 된다.

이후에 `Test Uploader` 버튼을 누르고.. 아래와 같이 Success가 뜨면 끝!

![image-20230213223141484](https://swagger.synology.me:5543/uploads/2023/02/image-20230213223141484.png)

![image-20230213223936275](https://swagger.synology.me:5543/uploads/2023/02/image-20230213223936275.png)

추가적으로 nas 내에서는 위와같이 업로드 연/월에 따라 폴더가 생성되어 관리가 가능하다.

---

<br />

# 해냈다!

![무야호](https://d2u3dcdbebyaiu.cloudfront.net/uploads/atch_img/30/f7bd4b017cbe82cb40ac47ac72d403d1_crop.jpeg)

이제 이미지 서버에 대한 걱정은 하지 않아도 된다!

대신 이 이미지들은 블로그가 운영되는 동안 계속 관리가 되어야하는데.. **nas가 문제가 생긴다면 블로그 사진이 몽땅 로딩이 되지 않으니 이부분은 감수를 해야한다.(그런데 사용하는 십몇년동안 그런일은 없었다 ^^ㅎ)** 그리고 내 서버이다 보니까.. 잘못 업로드가 되면 사진을 직접 삭제해야하고.. 메모리 관리가 좀 필요하다.

**개인적으로 개인 nas보다는 남의 서버가 훨씬 좋으니 이런 부분에서 스트레스를 받을 것 같다면 그냥 티스토리나 벨로그 같은 다른 서비스를 이용하는게 제일 Best이다.**

**다시 블로그를 만들라고 한다면 난 그냥 티스토리를 만드는게 낫다고 생각**한다...
