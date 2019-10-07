1. 도커, 자바깔기.

(자바 까는법은 알아서.. jdk 1.8 까세용)
자바까는법 참고
https://medium.com/@js230023/%EB%A6%AC%EB%88%85%EC%8A%A4-java-jdk-%EC%84%A4%EC%B9%98-1fe2c8fef35

2. 젠킨스 깔기
```bash
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install jenkins
```

3. 깃 깔기
```
yum install git
```

4. 도커 깔기
```
sudo yum -y install docker
systemctl start docker
```

3. 설치 확인 
```
rpm -qa | grep jenkins
```
기본 디렉토리는 `/var/lib/jenkins` 이다.

4. 젠킨스는 8080을 쓰는데 충돌할 일이 많아서 다른 포트로 설정해준다.
```
vi /etc/sysconfig/jenkins
```
JENKINS_PORT="8080"

부분을 변경 => JENKINS_PORT="8787"

5. 당연히 aws 방화벽 8787 풀어주자.

5-1. 로컬 centos면 방화벽 열어야한다.

	1. 없으면 
		```bash
		sudo yum install firewalld
		systemctl enable firewalld
		systemctl start firewalld
		```


	2. 있으면 
		```bash
		firewall-cmd --zone=public --add-port=8080/tcp --permanent
		firewall-cmd --reload
		```

6. 젠킨스 구동
```
service jenkins start
```

7. 8787포트로 접속하여 젠킨스 잘 뜨는지 확인.

8. 들어가면 unlock jenkins 라는 헤더에 빨간색으로 경로 뜨고 administrator 패스워드 뜰것이다.
	해당 패스워드를 넣어주어야 한다. 패스워드를 찾아보자.
	```
	cat /var/lib/jenkins/secrets/initialAdminPassword
	```
	나온 이상한 값을 복사해서 해당 페이지 input창에 넣어주자.

9. 입력후에 뜨는 customize jenkins 버튼 중 왼쪽꺼 누르자.
	(왼쪽은 필요한 plugin을 다 깔아주는 모드, 오른쪽은 하나도 안깔린 모드)
	
10. jenkins 메인화면이 떴다. 이제 git hub랑 연동하자.
	깃 허브 프로젝트는 우리꺼 써도 되긴한데 걍 샘플 스프링 부트 repo를 만드는것을 추천함.
	(메인페이지만 있는 간단한 프로젝트.. 아마 대현이 행님이 제공해 주실것임 ^^*)
	

11. https://jojoldu.tistory.com/442
여기서 4번 테스트 전까지 하자.
일단 여기서 젠킨스 로그아웃 하고 다시 로그인하자.

12. 젠킨스 페이지에서 왼쪽부분의 `새로운 Item` 클릭.

13. name은 대충 써주고 제일 위의 freestyle project 클릭하고 ok

14. 위쪽의 `소스코드관리`탭 눌러주고 `git` 라디오 버튼 누르고 `reopsitory url`에 git hub repo url,
	credentials에는 콤보박스 눌러서 방금 등록한 그거 선택.
	
15. 바로 빝에 `Branches to build`항목이 있는데 이부분은 어떤 브랜치에 push되면 build 될건지 설정하는것임.
branch의 경우 master push시에 젠킨스 빌드가 관리 되도록 `*/master`를 입력함. 만약 develop브랜치에 push시에 빌드되도록 하신다면 `*/develop`을 입력.

16. push 시에 trigger 오도록 빌드 유발 쪽에서 `GitHub hook trigger for GITScm polling` 설정.

17. 일단 build쪽에 명령어 아무것도 입력하지않고 저장.

19. 스프링 부트는 maven 써야하기때문에 maven 설정 해주어야함.

20. 메이븐 설치 = https://4urdev.tistory.com/91?category=796940

21. 된김에 메이븐을 프로젝트에 설정해줘야한다.
	Build 탭에 `add build step` 버튼 누르고 `Invoke top-level Maven targets` 선택.
		maven 버전 콤보박스 내려서 원하는거 적어주고. Goals에 `clean package` 적어줌.

22. 젠킨스 프로젝트 왼쪽 메뉴에서 `Build Now` 눌러서 빌드 되는지 확인

23. denied 문제 뜨면 
```
sudo groupadd docker
sudo usermod -aG docker jenkins
```
하고 젠킨스 재시작

젠킨스에게 root 권한 주는 방법도 있음.
https://stackoverflow.com/questions/29926773/run-shell-command-in-jenkins-as-root-user

웹훅 참고 : https://kutar37.tistory.com/entry/Jenkins-Github-%EC%97%B0%EB%8F%99-%EC%9E%90%EB%8F%99%EB%B0%B0%ED%8F%AC-3
23. 되면 일단 젠킨스랑 git 연동 되었음. 근데 현재는 push 했을때 자동으로 받아오질 못함.

24. 그거 설정하려면 hooks 필요.

25. 젠킨스 메인에서 `jenkins 관리 - 시스템설정` 으로 가서 `jenkins Location` 수정.
	건드리지않았다면 localhost일것이다. AWS는 잘 되어있을것이다. 로컬은 외부접속 아이피 적어주기.

25. Web Hooks 연동하기 위해 해당 git repo의 settings 왼쪽의 webhooks 들어가서 add webhook

26. payload URL : [젠킨스 서버 domain or IP]:[젠킨스 포트]/github-webhook/
	content type = application/json
	
	마지막에 `/` 빼먹지마라.
	당연히 포트도 열어야함
	
	계속 빨간불 뜨면 
	
27. 설정하고 아까 추가한 repo 의 webhook으로 들어갔을때 아래와 같이 뜬다.
https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F99C565405C6133CB210BF8
초록체크 뜨면 됨. 오류나면 검색하세요.

28. 대충 git push 했을때 젠킨스에서 build 되는지 확인하세용~!~!~!~! 콘솔로 들어갔을떄 build 되면 끝~!~!

29. 젠킨스 슬랙 연동
https://dnight.tistory.com/entry/Jenkins-Slack-%EC%95%8C%EB%A6%BC-%EC%97%B0%EB%8F%99

jenkins 연동은 여기까지. war파일 실행은 알아서~ tomcat 쓰면 jenkins에서 알아서 배포해줘서 편함~!~!


**팁**
- failed; error='Cannot allocate memory'같은 거지같은 에러가 나오면 메모리 부족때문이다.
https://www.oops4u.com/2238

```
echo 1 > /proc/sys/vm/overcommit_memory
```

- 쉘 스크립트에 지대한 공을 올린 사이트
https://www.jianshu.com/p/3a5f7db4e9e2

- 이런 에러 나오면 도커 실행이 안되고 있는것임. systemctl enable docker 해줄것.
```
Sep 28, 2019 3:20:48 PM com.spotify.docker.client.shaded.org.apache.http.impl.execchain.RetryExec execute
INFO: I/O exception (java.io.IOException) caught when processing request to {}->unix://localhost:80: No such file or directory
```
