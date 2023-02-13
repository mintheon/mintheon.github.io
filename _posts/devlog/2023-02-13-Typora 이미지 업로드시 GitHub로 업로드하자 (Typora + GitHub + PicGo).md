---
layout: post
title: 'Typora 이미지 업로드시 GitHub로 업로드하자 (Typora + GitHub + PicGo)'
subtitle: ''
date: 2023-02-13 21:08:44
categories: devlog
tags: typora 글또 issue github
---

![Typora a markdown editor, markdown reader.](https://typora.io/img/icon_256x256.png)

# 계기

- (관련글) [Typora 이미지 업로드시 시놀로지 Nas로 업로드하자 (Typora + Synology + PicGo)](https://mintheon.com/devlog/2023/02/13/Typora-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%97%85%EB%A1%9C%EB%93%9C%EC%8B%9C-%EC%8B%9C%EB%86%80%EB%A1%9C%EC%A7%80-Nas%EB%A1%9C-%EC%97%85%EB%A1%9C%EB%93%9C%ED%95%98%EC%9E%90-(Typora-+-Synology-+-PicGo))

후.. 이 글을 작성하게 된 계기는 위 글을 참조하라....

사실 시놀로지 nas만을 위한 방법의 위 글만 적었었는데 GitHub를 사용하는 방법도 궁금하실까 싶어 추가적으로 글을 작성한다.

**기존 깃허브의 issue탭을 사용해서 이미지 업로드를 하셨던 분들이라면.. 꼭 사용해보시길!!!**

---

<br />

# 방법

위 글과 중복이지만 이 글로만 찾아온 사람이 있을 것 같아 동일하게 작성해본다.

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

---

<br />

# GitHub 레포에 이미지 업로드 하도록 세팅하기

이 방법은 GitHub Repo를 하나 파서 거기에 업로드 하도록 하는 방법이다.

<br />

## 1. 레포지토리 만들기

![image-20230213224831210](https://swagger.synology.me:5543/uploads/2023/02/image-20230213224831210.png)

**이름은 `blog-images`로 지었다. (스샷의 repo이름은 오타)** Readme file을 만드는 부분에 체크해준다.

<br />

## 2. 토큰 생성

![image-20230213225112587](https://swagger.synology.me:5543/uploads/2023/02/image-20230213225112587.png)

프로필을 클릭하여 나온 메뉴에서 세팅으로 들어간다.

![image-20230213225211976](https://swagger.synology.me:5543/uploads/2023/02/image-20230213225211976.png)

이후 왼쪽 네비바의 `Developer Settings` 로 진입한다.

![image-20230213225450446](https://swagger.synology.me:5543/uploads/2023/02/image-20230213225450446.png)

여기서 `Tokens(classic)` 에서 새 토큰 만들기 클릭!

![image-20230213225605733](https://swagger.synology.me:5543/uploads/2023/02/image-20230213225605733.png)

이름은 `typora-image-uploader`로 하고 만료기간은.. ~~노빠꾸~~ `No expiration` 으로 설정했다. scopes 는 `repo` 를 전체 선택해주면 된다.

![image-20230213225825907](https://swagger.synology.me:5543/uploads/2023/02/image-20230213225825907.png)

토큰이 완성되었다! 저 초록색 박스에 뜬 토큰은 다시 볼 수 없으니 바로 복사해서 메모장에 붙여놓자.

<br />

## 3. picgo-core 설치

단순히 이것만 한다고 자동으로 사진이 업로드 되는건 아니다.**사진 업로드와 url 생성을 도와줄 `picgo` 라는 친구를 설치할 것**이다.

이 프로그램은 Command 타입(picgo-core)과 GUI 타입(picgo)이 있는데 `Typora`는 과거에 GUI 버전을 지원했지만 현재는 Command 버전만 지원한다. 그래서 해당 버전을 설치한다. **나는 mac을 사용하기 때문에.. windows는 따로 적지 않겠다..**


```bash
npm install -g picgo
```

yarn을 사용해도 된다. 혹시나 npm 명령어가 먹히지 않는다면 [nodejs를 최신버전으로 깔길 바란다](https://nodejs.org/en/).

이후에 잘 깔렸는지 확인하기 위해 `picgo --help` 를 추가적으로 쳐보고 잘 나온다면 설치가 완료되었다!

<br />

## 4. picgo 세팅

![image-20230213221507477](https://swagger.synology.me:5543/uploads/2023/02/image-20230213221507477.png)

Finder를 켜고 `Command + Shift + G` 를 누르면 폴더 이동창이 뜬다. 거기에 `~/.picgo`를 적어서 경로로 이동하자.

그러면 하위에 `config.json` 파일이 있을텐데 그걸 열어서 아래와 같이 설정해주자.

```json
{
  "picBed": {
    "current": "github",
    "github": {
      "repo": "깃허브ID/Repository이름",
      "token": "토큰",
      "path": "images/",
      "customUrl": "https://raw.githubusercontent.com/깃허브ID/Repository이름/branch명",
      "branch": "branch명(기본 main)"
    }
  },
  "picgoPlugins": {}
}
```

repository 내의 임의 폴더에만 업로드를 시키고 싶다면 `path`에 해당 폴더 경로를 적어줄 것.
본인은 `images`라는 폴더에 넣기 위해 경로를 저렇게 해놨다. 사용하지 않을경우 `/` 로 적어주면 된다.

**저장 버튼을 누르고 세팅 끝!**

<br />

## 5. Typora 세팅

![image-20230214001432273](https://swagger.synology.me:5543/uploads/2023/02/image-20230214001432273.png)

이미지 업로더를 `picgo-core`로 변경해주면 끝!

![iShot_2023-02-14_00.13.56](https://swagger.synology.me:5543/uploads/2023/02/iShot_2023-02-14_00.13.56.gif)

**이미지를 이렇게 붙여넣게 되면 github의 이미지 링크가 뜨며 업로드**된다.

![image-20230214001541023](https://swagger.synology.me:5543/uploads/2023/02/image-20230214001541023.png)

**레포지터리를 확인하면 해당 레포의 경로에 이미지들이 있는걸 확인할 수 있다. 단순히 Typora의 저 이미지 마크다운을 그대로 사용하면 끝!**

---

# 마무리

![따봉도치야 고마워](https://post-phinf.pstatic.net/MjAxOTA4MTBfOSAg/MDAxNTY1NDEzMDk4MzQz.oWK3cwFpOic6J7Mmd8pZNE6G_4jhxmYj8Iu_Jz3Xvzcg.c0HA1q4Sl-AfSDm_y676x1ED0VGGL4BEdk8_qodBajwg.JPEG/2w4w02ks9531p3v874c7.jpg?type=w1200) 

~~따봉도치야 고마워!~~

**`Typora`를 통해 아주 쉽게 이미지 업로드를 하는 방법을 알아봤다!** 개인적으로 마크다운 툴 중에서 제일 좋아라 하는 툴이기 때문에 다들 많이 이용하면 좋겠다.

기존엔 beta버전이라 공짜였지만 지금은 유료라는게 조금 흠이지만 계속 쓸거라 생각하면 투자하기 괜찮다.

**제일 Best는 이런 부분에서 스트레스를 받을 것 같다면 그냥 티스토리나 벨로그 같은 다른 서비스를 이용하는것이다 ^^!!! (짱짱)**
