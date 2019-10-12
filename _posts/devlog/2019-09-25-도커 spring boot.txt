---
layout: post
title: "도커 Spring Boot 배포"
subtitle: ""
date: 2019-09-25 20:40:09
categories: devlog
tags: bit spring web
---

이번에 Spring Boot로 만든 게시판을 Docker에 올리고, jenkins를 사용하여 배포 관리하는 단계이다.
https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html

---

1. 해당 프로젝트의 `pom.xml`파일 안을 수정한다.
```xml
	(...)
	<groupId>bit</groupId>
	<artifactId>p1</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging> <!-- 이 부분 -->
	(...)
```

2. 해당 디렉토리 터미널에서 `mvn build` 입력

3. 해당 프로젝트의 `target` 디렉토리에 `.war`파일이 생김  
	(본인은 `p1-0.0.1-SNAPSHOT.war` 파일이었음)

4. 해당 `.war`파일을 docker가 설치되어있는 곳으로 이동  
	(어디에 넣을것인지는 상관없음. 본인은 `/root/docker-test` 폴더에 넣었음)

5. 해당 디렉토리에 Dockerfile 생성  
	- 반드시 `Dockerfile` 이라는 이름으로 docker file 생성
	```
	FROM		9m1i9n1/bit9calendar:1
	MAINTAINER	min@min.com
	RUN		apt-get -y update
	ADD		p1-0.0.1-SNAPSHOT.war app.war
	EXPOSE 		18080
	ENV		JAVA_OPTS=""
	ENTRYPOINT	["java","-jar","/app.war"]
	```

6. 도커에서 dockerfile 실행
	```bash
	docker build --tag test1:0.1 ./
	```
	
	--tag : 생성될 이미지의 태그(이름 임의로 설정 가능하다)

	./ : 현재 명령어가 실행되는 곳의 기준으로 Dockerfile의 위치

7. 도커 이미지 생성 확인
	```
	docker images
	```
	
8. 이미지 실행
	```
	docker run -p 18080:18080 test1:0.1
	```

9. 접속......

---

- 우분투 도커이미지로 실행
	```
	docker run -d -it ${images name} /bin/bash
	```

- 컨테이너에서 명령어 실행
	```
	docker exec -it ${images name} /bin/bash
	```

```
docker images : 이미지를 보여준다.
docker ps -a : 모든 컨테이너 정보다.
docker rm $CONTAINER_ID : 컨테이너를 삭제한다.
docker start $CONTAINER_ID : 컨테이너를 시작한다.
docker stop $CONTAINER_ID : 컨테이너를 중지한다. 
docker logs $CONTAINER_ID : 로그를 확인한다.
docker top $CONTAINER_ID : 프로세서 정보를 확인한다.
docker inspect $CONTAINER_ID : 컨테이너의 모든 정보를 보여준다.(JSON)
docker port $CONTAINER_ID : 포트가 어디로 연결 되었있는지 보여준다.
```

--tag : 생성될 이미지의 태그

08ca988d544141c5acfaf3ae50effld1



-------

젠킨스 

1. jenkins 도커 다운
```
docker run -p 65000:8080 jenkins
```

2. 실행후 포트 접속

3. 젠킨스 bash로 접속
```
docker exec -it jenkins /bin/bash
```

4. 패스워드 찾기
```
cat /var/jenkins_home/secrets/initialAdminPassword
```

5. 입력후 플러그인 설치.
But Jenkins 관리에 들어갔을 때 기존의 버전 image에 있는 jenkins의 버전이 낮아 업데이트가 필요하다면 Jenkins container에 있는 war을 업데이트 하고 설정을 시작해야 한다.

6. 컨테이너에 들어와 있다면 `exit`명령어로 나옴

7. 터미널에 아래 명령어를 입력하여 root 권한으로 진입  
```
docker exec -it -u 0 ${container name} /bin/bash
```

	(docker container 내부에서 작업하기 때문에 루트권한 필요하며 '-u 0'이 루트 권한 획득이다)

8. tmp 폴더로 이동후 jenkins.war 파일 다운
```bash
cd /tmp
wget http://updates.jenkins-ci.org/download/war/2.197/jenkins.war 
```

9. 기존에 있는 war 파일을 업데이트 된 war 파일로 교체
```
mv ./jenkins.war /usr/share/jenkins
```

10. 권한 문제 해결
```
chown jenkins:jenkins /usr/share/jenkins/jenkins.war
```

11. 그후 컨테이너 재 실행
```
exit
docker restart ${container name}
```

12. github repo와 연동하려면 service 등록을 해야하는데 현재는 git app으로 바뀐상황.
아래 링크참고 => https://jojoldu.tistory.com/310
위 방식은 보안이 부족하니 => https://jojoldu.tistory.com/442
또한 jenkins 설정의 configure Global Security 탭의 CSRF Protection 부분 체크 해제해줘야 한다. 이렇게 되면 외부에서 Job에 대한 트리거링이 가능해 진다.
(마지막에 로그아웃 했다가 재로그인 하면 제대로 접속 가능하다)

13. Web Hooks 연동하기 위해 해당 repo의 settings 왼쪽의 webhooks 들어가서 add webhook

14. payload URL : [젠킨스 서버 domain or IP]:[젠킨스 포트]/github-webhook/
	content type = application/json

13. 이제 깃 레파지토리랑 연결시킨다. 왼편의 `새로운 Item` 클릭

14. 이름 정하고 freestyle project 클릭후 ok

15. 연동하고 싶은 git repo url 복사

16. jenkins 페이지 위쪽 탭의 `소스코드 관리`에서 git 선택.

17. repo url 등록하고, 아래의 키그림 그려져있는 add 클릭.

18. 뜨는 팝업창에서 kind 부분은 username with password 선택, username과 password 부분에 깃 계정이름과 비밀번호 입력후 add

19. credentials의 콤보박스에서 내 계정 선택

20. branch의 경우 master push시에 젠킨스 빌드가 관리 되도록 master를 입력함. 만약 develop브랜치에 push시에 빌드되도록 하신다면 `*/develop`을 입력.

21. 빌드 유발(빌드 trigger) 쪽에서는 `GitHub hook trigger for GITScm polling` 선택.

22. 마지막으로 Github에서 push가 올 경우 실행할 빌드를 등록

23. **그전에!!!** 본인은 maven을 사용해야 하기 때문에 mvn 명령어를 입력시켜야함. 
	jenkins에 지원하는 maven을 사용하도록 한다. 
	
24. Jenkins 의 전체 설정 > Global Tool Configuration 로 이동하여 Maven파트의 add maven 클릭.

25. name에 대충 구별하도록 적어주고 install automatically 설정. install from apache의 콤보박스에 원하는 버전으로 설정해준다.
	(본인은 name에 `maven 3.6.2` 적고, 버젼은 `3.6.2`로 깔았다.

26. save 해주고, 여기서만 해주는게 아니고 프로젝트 내에서도 설정해 주어야 한다.

27. 프로젝트로 들어가서 `구성`메뉴 들어간 후에, Build 부분에 `add build step`의 `invoke top-level Maven targets`에서 version 선택해주고 Goals에는 원하는 mvn 명령어를 적어준다.
	자동으로 앞에 `mvn`이 붙으니 제외하고 적을것.

27. 

23. add build step에서 execute shell 등록후 본인은 maven이기 때문에 


처음 설치시 버젼 오류로 플러그인들이 설치 안됨.
업데이트 해줘야함 : https://beomseok95.tistory.com/177
젠킨스 service 등록 : https://jojoldu.tistory.com/310
젠킨스 maven 설치 : https://4urdev.tistory.com/91?category=796940
https://miiingo.tistory.com/171


4f59115