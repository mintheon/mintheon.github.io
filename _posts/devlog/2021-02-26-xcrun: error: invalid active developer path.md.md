---
layout: post
title: "xcrun: error: invalid active developer path 에러 해결"
subtitle: ""
date: 2021-02-27 22:51:37
categories: devlog
tags: issue git shell
---


## 개요

어느날 뜬금없이 찾아온 에러.

![image-20210227225122834](https://tva1.sinaimg.cn/large/008eGmZEgy1go2flhubyzj324k03egmh.jpg)

`xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun`

깃에 파일을 업로드하려고 `git add .` 를 사용하니 해당 오류가 떴다.

## 원인

`Mac OS`에서 업데이트가 진행될때마다 자주 발생되는 `xcode cli` 관련 이슈이다.

해당 경우에 `git` 을 비롯한 다수 개발툴들이 다 에러를 뱉게 된다.

(`make`, `gcc`  등..)

## 해결

```shell
xcode-select --install
```

`terminal` 에서 해당 명령어로 `xcode cli` 만 따로 설치하여 문제를 해결한다.