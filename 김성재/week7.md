## 쿠버네티스 실습
설치부터 가로막혔다...
정말 다양한 방법을 시도해봤는데 아무것도 안된다..
스트레스 받아서 수명이 단축될 것 같다.

도커 설치까지는 지금까지 여러번 하던거라서 잘 되었는데
마스터 노드 설정부터 막혔다...

### 문제

영상에서는 로컬 pc의 vm위에다가 영상을 만들었으나 웹에서 하는게 목표였기에 평소 하던대로 aws
프리티어에서 무료로 제공해주는 t2.micro로 실습을 진행했다
```
ubuntu@ip-172-31-0-175:~$ sudo kubeadm init
[init] Using Kubernetes version: v1.31.0
[preflight] Running pre-flight checks
W0818 09:19:40.927200    4746 checks.go:1080] [preflight] WARNING: Couldn't create the interface used for talking to the container runtime: failed to create new CRI runtime service: validate service connection: validate CRI v1 runtime API for endpoint "unix:///var/run/containerd/containerd.sock": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR NumCPU]: the number of available CPUs 1 is less than the required 2
        [ERROR Mem]: the system RAM (957 MB) is less than the minimum 1700 MB
[preflight] If you know what you are doing, you can make a check non-fatal with --ignore-preflight-errors=...
To see the stack trace of this error execute with --v=5 or higher
ubuntu@ip-172-31-0-175:~$
```
그러니까 cpu가 부족하다고 해서ㅠ
```
sudo kubeadm init --ignore-preflight-errors=NumCPU,Mem
```

위와 같은 명령어를 쳐서 실행했더니 역시나 정상적으로 동작하지 않았다
프리티어에서 제공하는 1코어 cpu는 턱없이 부족했나보다
![](https://velog.velcdn.com/images/seongjae6751/post/71b0687d-ec90-4c3e-99aa-80f84b6a9614/image.png)
그래서 큰 맘 먹고 과금을 하기로 결정, t2.medium으로 노드들을 업그레이드 시켰다.

그렇게 다시 영상을 보고 따라 해서
```
kubectl get nodes
```
위 명령어를 다시 해보았으나 돌아오는 답은
```
The connection to the server 172.31.0.175:6443 was refused - did you specify the right host or port?
```
이렇게 삭제도 했다 다시 깔아보고 각종 설정부터 다 봤는데 계속 저 상태에서 그대로다..
![](https://velog.velcdn.com/images/seongjae6751/post/e53be20e-913d-4ba8-b0c0-b14f6f89306d/image.png)

찾아보니까 나 뿐만이 아닌 여러 사람들이 같은 에러를 겪고 있는 듯하다
애초에 시작을 우분투 18.04로 했으면 과제를 무사히 할 수 있었을까..🥲
쿠버네티스가 18.04LTS만 제공하는건 말이 안되는 것 같은데 아직도 뭐가 잘못 된건지 모르겠다

## 출처
* https://www.youtube.com/watch?v=lheclzO-G7k&list=PLApuRlvrZKohaBHvXAOhUD-RxD0uQ3z0c&index=4
