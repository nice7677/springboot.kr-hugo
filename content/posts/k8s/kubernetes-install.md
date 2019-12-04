---
title: "Kubernetes Minikube 설치"
date: 2019-11-18T19:02:57+08:00
---

맥북에서 kubernetes 설치를 위해 상단바에 있는 도커를 클릭후

Preferences... 를 클릭후 kubernetes 버튼을 눌러

Enable Kubernetes를 체크 후 apply를 눌러 쿠버네티스를 활성화 시킨다.

![이미지](/images/kube-install/kubernetes-install.png)

활성화가 되는 도중에는 오른쪽 하단에 Kubernetes is starting 이란 문구가 나오고

활성화가 끝나면 오른쪽 하단에 Kubernetes is running 으로 나온다.

다음으로 kubectl을 설치해 주자 kubernetes cli 툴이다.

```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

설치가 끝나면 version 체크로 확인을 해준다.

```
kubectl version
```
![이미지](/images/kube-install/kubernetes-install-4.png)


Kubectl 설치가 끝나면 Minikube 설치를 해준다.

[install-minikube](https://kubernetes.io/ko/docs/tasks/tools/install-minikube/) 공식 문서

우선 먼저 가상화 지원 여부를 확인하기 위해 명령어를 입력해준다

```
sysctl -a | grep -E --color 'machdep.cpu.features|VMX' 
```
명령어를 실행 후 

**VMX** 이란 녀석에 색으로 강조된 녀석이 있으면 가상화가 활성화 되있는 것 이다.

다음으로 minikube를 설치 해 주어야 하는데

homebrew를 사용하는 방법과 바이너리를 사용해 설치하는 방법이 있다.

##### 1. brew (1번 방법으로 설치를했을때 에러가나면 1-1의 방법으로 실행해 보자.)
```
brew cask install minikube
```

##### 1-1.
```
brew install minikube
```

##### 2. 바이너리
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 \
  && chmod +x minikube
```

다음으로 minikube를 실행 경로에 추가해 주는 작업이 필요하다.

```
sudo mv minikube /usr/local/bin
```

설치가 완료가 된 후 에는 다음 명령어를 실행해 준다.

```
minikube start
```

![이미지](/images/kube-install/kubernetes-install-2.png)

입력 후 다음 과같이 설치가 진행되고 실행이 된다.

그리고 다음과 같이 확인을 해보자

```
kubectl get nodes
```
![이미지](/images/kube-install/kubernetes-install-3.png)

미니쿠베가 잘 올라가있다.

