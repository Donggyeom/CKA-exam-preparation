2022.10.13
쿠버네티스 구성요소
  마스터 노드
  작업자 노드
마스터노드
  ETCD 클러스터 : key-value 형태의 정보 저장 공간
  kube-스케쥴러
  컨트롤러 매니저
    노드 컨트롤러 : 노드 관리
    복제 컨트롤러 : 복제 그룹에서 항상 동작하도록
  kube-API 서버 : 클러스터 내의 모든 작업을 조정
도커 : 컨테이너 런타임 엔진
  * 지원하는 다른 런타임 엔진 : ContainerD, Rocket
    작업자 노드
    Kublet : 작업 노드의 선장. 마스터 노드에 상태 보고. kube-API 서버의 지시를 듣고 노드에 컨테이너를 배포하거나 파괴.
    Kube-proxy 서버 : 각 작업 노드 간의 통신에 사용.

2022.10.17
ETCD : key-value 타입의 설정 저장소

2022.10.18
Kube-Controller Manager
  Node-Controller : 노드는 5초마다 Api Server를 통해 통신하고, 컨트롤러는 통신이 끊긴 노드를 체크, 40초 이상 통신이 안 될 경우, 복제 셋에서 새로 갖고 온다.
  Replication-Controller : 복제셋의 상태를 모니터링
Kube-Scheduler
  쿠버네티스 파드 : Linux 컨테이너를 하나 이상 모아 놓은 것으로, 쿠버네티스 애플리케이션의 최소 단위
  스케쥴러는 오직 파드가 어디로 갈지만 결정함, 노드에 파드를 배치하는 것은 오직 큐브렛의 역할
  2 단계에 거쳐 알맞은 노드를 결정하는데, 1번째로 파드가 요구하는 성능에 충분한 노드만 선택함. 2번째로 여유가 더 남는 노드를 선택하는데, 
  이는 설정에서 변경 가능함.
  taints와 tolerations : 테인트를 설정한 노드에는 파드가 스케쥴링 되지 않고, 톨러레이션을 이용한 특정 파드들만 실행 가능하게 함.
Kubelet
  마스터 노드와 단독 통신
  컨테이너 배치
  노드와 컨테이너의 상태를 주기적으로 통신
  워커노드에 항상 수동으로 설치해줘야 함
Kube Proxy
  쿠버네티스에서 모든 파드가 서로 통신 가능하게 함.
  네트워크 규칙 관리

2022.10.19
Recap - PODs
  컨테이너를 캡슐화한 것
  1 파드, 1 컨테이너
  하나의 파드에 헬퍼 컨테이너도 포함될 수 있다
  kubectl run nginx --image nginx
  kubectl get pods
PODs with YAML
  apiVersion:
  kind:
  metadata:
  spec:



2022.10.24

pod.yaml
apiVersion: v1
kind: Pod
metadata:
	name: nginx
	labels:
		app: nginx
		tier: frontend
spec:
	containers:

	- name: nginx
	  image: nginx

kubectl apply -f pod.yaml : 파드 생성
(apply = create)

kubectl get pods : 파드 확인

kubectl describe pod nginx : 파드 상세정보



2022.10.25

Create a new pod

- kubectl run `name` --image=`image-name` 
- kubectl run `name` --image=`image-name`  --dry-run=client -o yaml 



describe pods : kubectl get pods -o wide



Replication Controller

- high availability

- load balancing & scaling

- yaml 파일 구성

  - apiVersion: v1
  - kind: ReplicationController
  - metadata:
    - name: myapp-rc
    - labels:
      - app: myapp
      - type: front-end
  - spec:
    - template:
      - (apiVersion과 kind를 제외한 POD 설정)
    - replicas: (numbers of replica sets you need)

- 생성 : kubectl create -f (yaml 파일)

- 조회 : kubectl get replicationcontroller

  ​           kubectl get pods에서도 조회 가능



Replication Controller -> ReplicaSet

- yaml 파일

  - apiVersion: apps/v1
  - kind: ReplicaSet
  - metadata:
    - name: myapp-replicaset
    - labels:
      - app: myapp
      - type: front-end
  - spec:
    - template:
      - (apiVersion과 kind를 제외한 POD 설정)
    - replicas: (numbers of replica sets you need)
    - selector: 
      - matchLabels:
        - type: front-end

- 생성 : kubectl create -f (yaml 파일)

- 조회 : kubectl get replicaset

  ​           kubectl get pods에서도 조회 가능



Scale

- 수정 : kubectl replace -f (yaml)

  ​			kubectl scale --replicas=(값) -f (yaml)



2022.10.26

## Practice

kubectl get rs

kubectl describe replicaset `name`

kubectl create -f `yaml file`

kubectl explain replicaset

kubectl delete rs `rs-name`

kubectl edit rs `rs-name`

kubectl scale rs `rs-name` --replicas=`숫자`



Deployments

- yaml 파일 : kind에 Deployments 로 바뀌는 것 빼고 ReplicaSet과 동일

- kubectl get all

