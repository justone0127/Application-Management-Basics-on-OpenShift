## Application Management Basics on OpenShift

### 1. Application Management Basics

이 모듈에서는 `oc` 명령 도구를 통해 샘플 애플리케이션을 배포하고, 핵심 개념, 기본 오브젝트, 오픈시프트에서 애플리케이션을 관리하는 기본에 대해 배울 것 입니다.



### 1.1 Core OpenShift Concepts

오픈시프트 관리자로서, 애플리케이션과 관련된 여러 핵심 빌딩 블록을 이해하는 것은 중요하다. 이러한 빌딩 블록을 이해하는 것은 플랫폼 환경에서 애플리케이션 관리에 대한 큰 그림을 보는데 도움이 될 것 입니다.



### 1.2 Project

**Project**는 버킷의 종류입니다. 이것은 사용자의 모든 리소스가 있는 메타 구조입니다. 관리 관점에서의 각 **Project**는 테넌트입니다. **Project**는 이것에 접근 할 수 있는 여러 사용자가 있을 수 있으며, 사용자는 여러 **Project**에 접근 할 수 있습니다. 기술적으로 말하면 사용자는 리소스를 소유하지 않고, **Project**가 소유합니다. 사용자를 삭제해도 생성된 리소스에 대해서는 영향을 미치지 않습니다.

- 프로젝트 생성

  ```bash
  oc new-project app-management
  ```



### 1.3 Deploy a Sample Application

`new-app` 명령은 OpenShift가 작업을 실행하도록 하는 매우 간단한 방법을 제공합니다. 다양한 입력 배열 중 하나를 제공하기만 하면 무엇을 해야 하는지 파악합니다. 사용자는 일반적으로 이 명령을 사용하여 OpenShift가 기존 이미지를 시작하고, 소스 코드 빌드를 생성하고 궁극적으로 이를 배포하고, 템플릿을 인스턴스화 하도록 합니다.

아래 명령어를 수행하여 Quay에 있는 특정 이미지를 시작할 수 있습니다.

```bash
oc new-app quay.io/openshiftroadshow/mapit
```



다음과 내용이 표시됩니다.

```bash
$ oc new-app quay.io/openshiftroadshow/mapit
--> Found container image 7ce7ade (5 years old) from quay.io for "quay.io/openshiftroadshow/mapit"

    * An image stream tag will be created as "mapit:latest" that will track this image

--> Creating resources ...
    imagestream.image.openshift.io "mapit" created
    deployment.apps "mapit" created
    service "mapit" created
--> Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/mapit'
    Run 'oc status' to view your app.
```

OpenShift가 이 명령을 통해 자동적으로 생성한 여러 리소스들을 볼 수 있습니다.  생성된 리소스를 확인 하는 데에는 약간의 시간이 걸릴 수 있습니다.



`new-app`의 자세한 기능은 `oc new-app -h` 명령으로 확인해 보십시오.



### 1.4 Pods

![01_pods](https://github.com/justone0127/Application-Management-Basics-on-OpenShift/blob/main/images/01_pods.png)

Pods는 호스트에 함께 배포되는 하나 이상의 컨테이너입니다. Pod는 OpenShift에서 배포되고 관리되는 가장 작은 컴퓨트 유닛입니다. 각각의 Pod는 SDN에서 자체 내부 IP 주소가 할당되고 전체 포트 범위를 소유합니다.  Pod 내의 컨테이너는 로컬 스토리지 공간과 네트워킹 리소스를 공유할 수 있습니다.



Pod는 OpenShift 내의 **정적(static)** 객체로 취급됩니다. 즉, 실행중에 정의를 변경 할 수 없습니다.

 

Pod의 리스트를 확인 할 수 있습니다.

```bash
oc get pods
```



다음과 같은 내용을 확인 할 수 있습니다.

```bash
NAME                     READY   STATUS    RESTARTS   AGE
mapit-7f5b86b6bc-tl249   1/1     Running   0          11m
```

> Pod의 이름은 배포 프로세스의 부분으로 동적으로 생성됩니다. 



`decribe` 명령을 통해 Pod에 대한 더 상세한 정보를 확인 할 수 있습니다.

위 Pod 이름의 경우:

```bash
oc describe pod -l deployment=mapit
```

> `-l deployment=mapit`은 나중에 설명할 배치와 관련된 Pod을 선택합니다.



다음과 같은 내용이 표시됩니다.

```bash
Name:         mapit-7f5b86b6bc-tl249
Namespace:    app-management
Priority:     0
Node:         ip-10-0-199-52.ap-northeast-2.compute.internal/10.0.199.52
Start Time:   Thu, 15 Dec 2022 01:49:53 +0000
Labels:       deployment=mapit
              pod-template-hash=7f5b86b6bc
Annotations:  k8s.v1.cni.cncf.io/network-status:
                [{
                    "name": "openshift-sdn",
                    "interface": "eth0",
                    "ips": [
                        "10.131.0.191"
                    ],
                    "default": true,
                    "dns": {}
                }]
              k8s.v1.cni.cncf.io/networks-status:
                [{
                    "name": "openshift-sdn",
                    "interface": "eth0",
                    "ips": [
                        "10.131.0.191"
                    ],
                    "default": true,
                    "dns": {}
                }]
              openshift.io/generated-by: OpenShiftNewApp
              openshift.io/scc: restricted-v2
              seccomp.security.alpha.kubernetes.io/pod: runtime/default
Status:       Running
IP:           10.131.0.191
IPs:
  IP:           10.131.0.191
Controlled By:  ReplicaSet/mapit-7f5b86b6bc
Containers:
  mapit:
    Container ID:   cri-o://a47f8400fcec62bfa3fd04f0e7093341a0c42d990922ec46c5aed090b2ead517
    Image:          quay.io/openshiftroadshow/mapit@sha256:983bc150d53a370c7825dbc42bf1d00adc951bb2e367c149fd7041114ba5da4f
    Image ID:       quay.io/openshiftroadshow/mapit@sha256:983bc150d53a370c7825dbc42bf1d00adc951bb2e367c149fd7041114ba5da4f
    Ports:          8080/TCP, 8778/TCP, 9779/TCP
    Host Ports:     0/TCP, 0/TCP, 0/TCP
    State:          Running
      Started:      Thu, 15 Dec 2022 01:50:11 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9r7f6 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-9r7f6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
    ConfigMapName:           openshift-service-ca.crt
    ConfigMapOptional:       <nil>
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason          Age   From               Message
  ----    ------          ----  ----               -------
  Normal  Scheduled       15m   default-scheduler  Successfully assigned app-management/mapit-7f5b86b6bc-tl249 to ip-10-0-199-52.ap-northeast-2.compute.internal by ip-10-0-193-147
  Normal  AddedInterface  15m   multus             Add eth0 [10.131.0.191/23] from openshift-sdn
  Normal  Pulling         15m   kubelet            Pulling image "quay.io/openshiftroadshow/mapit@sha256:983bc150d53a370c7825dbc42bf1d00adc951bb2e367c149fd7041114ba5da4f"
  Normal  Pulled          15m   kubelet            Successfully pulled image "quay.io/openshiftroadshow/mapit@sha256:983bc150d53a370c7825dbc42bf1d00adc951bb2e367c149fd7041114ba5da4f" in 16.519716441s
  Normal  Created         15m   kubelet            Created container mapit
  Normal  Started         15m   kubelet            Started container mapit
```

> 실행중인 Pod에 대한 상세한 정보를 확인할 수 있습니다. pod가 어떤 노드에서 실행중인지, Pod의 내부 IP, labels, 다른 정보 등을 확인 할 수 있습니다.



### 1.5 Service

![02_services](https://github.com/justone0127/Application-Management-Basics-on-OpenShift/blob/main/images/02_services.png)

**Services**는 유사한 Pod 그룹을 찾기 위해 OpenShift 내에서 편리한 추상화 계층을 제공합니다. 또한 이러한 Pod와 OpenShift 환경 내부에서 Pod에 접근해야 하는 모든 것 사이에서 내부 프록시/로드 밸런서 역할을 합니다. 예를 들어 로드를 처리하기 위해 더 많은 mapit 인스턴스가 필요한 경우 더 많은 Pod를 가동할 수 있습니다. OpenShift는 자동으로 이를 서비스에 대한 엔트포인트로 매핑하며, 들어오는 요청은 서비스가 이제 요청을 더 잘 처리하고 있다는 점을 제외하고 다른 점을 인식하지 못합니다. 



OpenShift에 이미지 실행을 요청하면 `new-app` 명령이 자동으로 서비스를 생성합니다. 서비스는 내부 구조라는 것을 기억합니다. "외부 세계" 또는 Openshift 환경 외부에 있는 어떤 것도 사용할 수 없습니다. 



**Services**가 Pod 집합에 매핑되는 방식은 Label 및 Selector 시스템을 통해 이루어집니다. **Services**에는 고정 IP 주소가 할당되며 많은 포트와 프로토콜을 매핑할 수 있습니다. 



직접 만드는 YAML 형식을 포함하여 [Services](https://docs.openshift.com/container-platform/4.11/architecture/understanding-development.html#understanding-kubernetes-pods)에 대한 자세한 정보는 공식 문서에 있습니다.



다음을 사용하여 프로젝트의 현재 서비스 목록을 볼 수 있습니다.

```bash
oc get services
```



다음과 같은 내용이 표시됩니다.

```bash
NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
mapit   ClusterIP   172.30.219.152   <none>        8080/TCP,8778/TCP,9779/TCP   28m
```

> Service IP 주소는 생성 시 동적으로 할당되며 변경 할 수 없습니다. Service IP는 절대 변경되지 않으며 Service가 삭제될 때까지 IP가 예약됩니다.

Pod와 마찬가지로 `describe` 명령을 통해 services를 확인 할 수 있습니다. `describe` 명령으로 OpenShift의 대부분의 objects를 확인 할 수 있습니다.

```bash
oc describe service mapit
```



다음과 같은 내용이 표시됩니다.

```bash
Name:              mapit
Namespace:         app-management
Labels:            app=mapit
                   app.kubernetes.io/component=mapit
                   app.kubernetes.io/instance=mapit
Annotations:       openshift.io/generated-by: OpenShiftNewApp
Selector:          deployment=mapit
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                172.30.219.152
IPs:               172.30.219.152
Port:              8080-tcp  8080/TCP
TargetPort:        8080/TCP
Endpoints:         10.131.0.191:8080
Port:              8778-tcp  8778/TCP
TargetPort:        8778/TCP
Endpoints:         10.131.0.191:8778
Port:              9779-tcp  9779/TCP
TargetPort:        9779/TCP
Endpoints:         10.131.0.191:9779
Session Affinity:  None
Events:            <none>
```

> 모든 objects에 대한 정보(해당 정의, 해당 상태 등)는 etcd 데이터 저장소에 저장됩니다. etcd는 데이터를 Key/Value 형태로 저장하며 오든 데이터는 직렬화 가능한 데이터 objects(JSON, YAML)로 나타낼 수 있습니다.



서비스에 대한 YAML 출력을 살펴봅니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    openshift.io/generated-by: OpenShiftNewApp
  creationTimestamp: "2022-12-15T01:49:49Z"
  labels:
    app: mapit
    app.kubernetes.io/component: mapit
    app.kubernetes.io/instance: mapit
  name: mapit
  namespace: app-management
  resourceVersion: "12063613"
  uid: 6a80f514-6ac4-4294-96f3-342911b6c6f3
spec:
  clusterIP: 172.30.219.152
  clusterIPs:
  - 172.30.219.152
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: 8080-tcp
    port: 8080
    protocol: TCP
    targetPort: 8080
  - name: 8778-tcp
    port: 8778
    protocol: TCP
    targetPort: 8778
  - name: 9779-tcp
    port: 9779
    protocol: TCP
    targetPort: 9779
  selector:
    deployment: mapit
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
```

Pod의 `selector`를 기억해주십시오.  OpenShift가 구성 요소를 함께 연결하는 방법을 이해하기 위해 Pod의 YAML을 보는 것도 중요합니다. 돌아가서 mapit Pod의 이름을 찾은 후 다음을 실행합니다.

```bash
oc get pod -l deployment=mapit -o jsonpath='{.items[*].metadata.labels}' | jq -r
```

> `o -jsonpath` 는 특정 필드를 선택합니다. 이 경우 매니페스트의 `labels` 섹션을 요청합니다.



다음과 같은 내용이 표시됩니다.

```bash
{
  "deployment": "mapit",
  "pod-template-hash": "7f5b86b6bc"
}
```

- **Services**는 `deployment: mapit`을 참조하는 `selector` 스탠자가 있습니다.
- **Pod**는 다음과 같은 여러 **Labels**가 있습니다.
  - `deployment: mapit`
  - `pod-template-hash": "7f5b86b6bc`

**Labels**는 key/values 형태의 값 입니다. **Selector**와 일치하는 **Label**이 있는 이 프로젝트의 모든 **Pod**는 서비스와 연결됩니다. `describe` 출력을 다시 service에 대한 하나의 엔드포인트(기존  `mapit` **Pod**)가 있음을 알 수 있습니다.



`new-app`의 기본 동작은 요청된 항목의 인스턴스를 하나만 생성하는 것 입니다. 잠시 후에 이를 수정/조정하는 방법을 살펴보겠지만 먼저 배워야 할 몇 가지 개념이 더 있습니다.



### 2. Background: Deployment Configurations and Replica Sets

**Services**는 **Pods**에 대한 라우팅 및 로드 밸런싱을 제공하며 존재하거나 소멸할 수 있지만 **ReplicaSet(RS)**는 존재하는 **Pods**(replicas)의 원하는 수를 지정하고 확인하는 데 사용됩니다. 예를 들어, 애플리케이션을 항상 3개의 **Pod** (instances)로 확장하려면 **ReplicaSet**가 필요합니다. RS가 없으면 종료되거나 죽거나/종료되는 **Pods**가 자동으로 다시 시작되지 않습니다. **ReplicaSet**은 OpenShift가 "self-heals"하는 방법입니다.



**Deployment**(deploy)는 OpenShift에서 무언가 배포하는 방법을 정의합니다. 

자세한 내용은 공식문서를 참조 : [Deployments documentation](https://docs.openshift.com/container-platform/4.11/applications/deployments/what-deployments-are.html#deployments-kube-deployments_what-deployments-are)

```bash
Deployments describe the desired state of a particular component of an
application as a Pod template. Deployments create ReplicaSets, which
orchestrate Pod lifecycles.
```

거의 모든 경우에 **Pod**, **Service**, **Replicaset** 및 **Deployment**를 함께 사용하게 됩니다. 그리고 거의 모든 경우에 OpenShift가 모든 것을 생성합니다.



**Deployment**나 **Service**가 없는 일부 **Pods**와 **RS**가 필요한 일부 극단적인 경우가 있지만 이러한 사례는 이 연습에서 다루지 않는 고급 주제입니다.



> 이전 버전의 OpenShift는 **DeploymentConfig**라는 것을 사용했습니다. 여전히 유효한 배포 매커니즘이지만 앞으로 배포는 `oc new-app`으로 생성될 것 입니다. 자세한 내용은 [공식 문서](https://docs.openshift.com/container-platform/4.11/applications/deployments/what-deployments-are.html#deployments-comparing-deploymentconfigs_what-deployments-are)를 참조하십시오.



### 2.1 Exploring Deployment-related Objects

이제 **Replicaset** 및 **Deployment**가 무엇인지에 대한 배경을 알았으므로 이들이 어떻게 작동하고 관련되어 있는지 살펴볼 수 있습니다.  OpenShift에 `mapit` 이미지를 설정하도록 지시했을 때 생성된 **Deployment** (deploy)를 살펴보십시오.

```bash
oc get deploy
```



다음과 같은 내용이 표시됩니다.

```bash
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
mapit   1/1     1            1           3h15m
```

더 자세한 정보를 얻기 위해 **Replicaset (RS)**를 살펴볼 수 있습니다.



OpenShift에 `mapit` 이미지를 설정하라고 지시했을 때 생성된 **Replicaset(RS)**를 살펴보십시오.

```bash
oc get rs
```



다음과 같은 내용이 표시됩니다.

```bash
NAME               DESIRED   CURRENT   READY   AGE
mapit-68ddc9cfdc   0         0         0       3h17m
mapit-7f5b86b6bc   1         1         1       3h17m
```

이를 통해 현재 하나의 **Pod**가 배포될 것이라고 예상(`Desired`)하고 하나의 **Pod**가 실제로 배포되었음을 알 수 있습니다.



### 2.2 Scaling the Application

`mapit` 애플리케이션을 최대 2개의 인스턴스로 확장해보겠습니다. 

```bash
oc scale --replicas=2 deploy/mapit
```



복제본 수를 변경했는지 확인하려면 다음 명령을 실행하십시오

```bash
oc get rs
```



다음과 같은 내용이 표시됩니다.

```bash
NAME               DESIRED   CURRENT   READY   AGE
mapit-68ddc9cfdc   0         0         0       3h23m
mapit-7f5b86b6bc   2         2         2       3h23m
```

> "old" 버전이 유지되었습니다. 이는 애플리케이션의 이전 버전으로 "rollback" 할 수 있도록 하기 위한 것 입니다.



이제 2개의 복제본이 있음을 알 수 있습니다. `oc get pods` 명령을 사용하여 Pods 수를 확인합니다.

```bash
oc get pods
```



다음과 같은 내용이 표시됩니다.

```bash
NAME                     READY   STATUS    RESTARTS   AGE
mapit-7f5b86b6bc-lltqn   1/1     Running   0          2m23s
mapit-7f5b86b6bc-tl249   1/1     Running   0          3h25m
```



마지막으로, 이전 실습에서 배운 **Service**가 두 엔드포인트를 정확하게 반영하는지 확인하겠습니다.

```bash
oc describe svc mapit
```



다음과 같은 내용이 표시됩니다.

```bash
Name:              mapit
Namespace:         app-management
Labels:            app=mapit
                   app.kubernetes.io/component=mapit
                   app.kubernetes.io/instance=mapit
Annotations:       openshift.io/generated-by: OpenShiftNewApp
Selector:          deployment=mapit
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                172.30.219.152
IPs:               172.30.219.152
Port:              8080-tcp  8080/TCP
TargetPort:        8080/TCP
Endpoints:         10.131.0.191:8080,10.131.2.103:8080
Port:              8778-tcp  8778/TCP
TargetPort:        8778/TCP
Endpoints:         10.131.0.191:8778,10.131.2.103:8778
Port:              9779-tcp  9779/TCP
TargetPort:        9779/TCP
Endpoints:         10.131.0.191:9779,10.131.2.103:9779
Session Affinity:  None
Events:            <none>
```

**Service**의 엔드포인드를 보는 또 다른 방법은 다음과 같습니다.

```bash
oc get endpoints mapit
```

다음과 같은 내용이 표시됩니다.

```bash
NAME    ENDPOINTS                                                           AGE
mapit   10.131.0.191:8080,10.131.2.103:8080,10.131.0.191:9779 + 3 more...   3h27m
```

각 Pod는 OpenShift 환경 내에서 고유한 IP를 수신하므로 IP 주소는 다를 수 있습니다. 엔드포인트 목록은 service 뒤에 있는 Pod 수를 빠르게 확인할 수 있는 방법입니다.



전반적으로 애플리케이션(**Service**의 **Pod**)을 확장하는 것은 간단합니다. 특히 해당 이미지가 노드에 이미 캐시되어 있는 경우 OpenShift가 기존 이미지의 새 인스턴스를 시작하기 때문에 애플리케이션의 확장이 매우 빠르게 발생할 수 있습니다.



마지막으로 주목해야 할 사항은 실제로 이 서비스에 여러 포트가 정의되어 있다는 것 입니다. 앞에서 우리는 Pod가 단일 IP를 얻고 해당 IP의 전체 포트 공간을 제어한다고 말했습니다. **Pod** 내부에서 실행 중인 무언가가 여러 포트(여러 포트를 사용하는 컨테이너, 개별 포트를 사용하는 개별 컨테이너, 혼합)에서 수신 대기할 수 있지만 **Service**는 실제로 포트를 다른 위치로 프록시/매핑할 수 있습니다.



예를 들어, **Service**는 레거시 이유로 포트 80에서 수신 대기 할 수 있지만 포트는 포트 8080, 8888 또느 ㄴ기타 모든 위치에서 수신 대기 할 수 있습니다.



이 `mapit` 사례에서 우리가 실행한 이미지는 Dockerfile에 여러 `EXPOSE` 구문이 있으므로 OpenShift는 자동으로 서비스에 포트를 생성하고 포드에 매핑했습니다.



### 2.3 Application "Self Healing"

OpenShift의 **RSs**는 원하는 수의 Pod가 실제로 실행 중인지 확인하기 위해 지속적으로 모니터링하므로 상황이 올바르지 않은 경우 OpenShift가 상황을 "수정"할 것이라고 기대할 수도 있습니다.



이제 두 개의 **Pods**가 실행 중이므로 삭제하면 어떻게 되는지 살펴보겠습니다. 먼저, `oc get pods` 명령어를 수행하고, **Pod**의 이름을 기록해 둡니다.



```bash
oc get pods
```



다음과 같은 내용이 표시됩니다.

```bash
NAME                     READY   STATUS    RESTARTS   AGE
mapit-7f5b86b6bc-lltqn   1/1     Running   0          14m
mapit-7f5b86b6bc-tl249   1/1     Running   0          3h37m
```



이제 **Deployment** `mapit`에 속한 Pod를 삭제합니다.

```bash
oc delete pods -l deployment=mapit --wait=false
```



다시 `oc get pods` 명령을 수행합니다.

```bash
oc get pods
```

이미 실행중인 컨테이너가 있습니다.



**Pods**의 이름은 다릅니다. 이는 OpenShift가 현재 상태 (**Pods**가 삭제되었기 때문에 0개)가 원하는 상태(2개 **Pods**)와 일치하지 않음을 거의 즉시 감지하고 **Pods**를 예약하여 수정했기 때문입니다.



### 2.4 Background : Routes

![03_routes](https://github.com/justone0127/Application-Management-Basics-on-OpenShift/blob/main/images/03_routes.png)

**Services**는 OpenShift 환경 내에서 내부 추상화 및 로드 밸런싱을 제공하지만 때로는 OpenShift 외부의 클라어인트(사용자, 시스템, 장치 등)가 애플리케이션에 접근해야 합니다. 외부 클라이언트가 OpenShift에서 실행 중인 애플리케이션에 접근할 수 있는 방법은 OpenShift 라우팅 계층을 통하는 것 입니다. 그리고 그 뒤의 데이터 Object는 **Route**입니다.



기본 OpenShift router (HAProxy)는 수신 요청의 HTTP 헤더를 사용하여 연결을 프록시할 위치를 결정합니다. **Route**에 대한 TLS와 같은 보안을 선택적으로 정의할 수 있습니다. **Services** (및 확장하여 **Pods**)를 외부에서 접근할 수 있게 하려면 **Route**를 생성해야 합니다.



라우팅 설정을 기억하십니까? 아마 아직 하지 않았습니다. 그 이유는 설치 시 router Operator가 배포되었고 Operator가 당신을 위해 router를 생성했기 때문입니다. router는 `openshift-ingress` **Project**에 있으며 다음 명령을 사용하여 이에 대한 정보를 볼 수 있습니다.

```bash
oc describe deployment router-default -n openshift-ingress
```

다음 실습에서 라우터용 Operator를 자세히 살펴보겠습니다.



### 2.5 Creating a Route

**Route** 생성은 매우 간단한 프로세스입니다. 명령어를 통해 **Service**를 `expose` 하기만 하면 됩니다. 앞에서 기억한다면 이 서비스 이름은 `mapit`입니다. **Service** 이름을 사용하여 **Route**를 만드는 것은 간단한 단일 명령 작업입니다.

```bash
oc expose service mapit
```

다음을 볼 수 있습니다.

```bash
route.route.openshift.io/mapit exposed
```

다음 명령을 사용하여 **Route**가 생성되었는지 확인합니다.

```bash
oc get route
```

다음과 같은 내용이 표시됩니다.

```bash
NAME    HOST/PORT                                               PATH   SERVICES   PORT       TERMINATION   WILDCARD
mapit   mapit-app-management.apps.ocp4.sandbox298.opentlc.com          mapit      8080-tcp                 None
```

`HOST/PORT`를 열을 살펴보면 익숙한 모양의 FQDN을 볼 수 있습니다. OpenShift의 기본 동작은 공식 호스트 이름에 서비스를 노출하는 것 입니다.

`{SERVICENAME}-{PROJECTNAME}-{ROUTINGSUBDOMAIN}`

후속 router Operator 실습에서는 이 옵션과 기타 구성 옵션을 살펴보겠습니다.

router 구성은 router가 수신 대기해야 하는 도메인을 지정하지만 여전히 먼저 해당 도메인에 대한 요청을 router로 가져와야 합니다. `*.apps...` router가 있는 호스트를 가리키는 와일드카드 DNS 항목이 있습니다. OpenShift는 **Service** 이름, **Project** 이름 및 라우팅 하위 도메인을 연결하여 이 FQDN/URL을 생성합니다.



브라우저를 사용하거나 `curl` 또는 기타 도구를 사용하여 이 URL을 방문할 수 있습니다.



**Route**는 **Service**와 연결되면 router는 자동으로 **Pod**에 대한 연결을 직접 프록시합니다. router 자체는 **Pod**로 실행됩니다. "실제" 인터넷을 SDN에 연결합니다.



한 걸음 물러서서 지금까지 수행한 모든 작업을 검토하면 세 가지 명령으로 애플리케이션을 배포하고 확장하고 외부에서 접근할 수 있도록 했습니다.

```bash
oc new-app quay.io/openshiftroadshow/mapit
oc scale --replicas=2 deploy/mapit
oc expose service mapit
```



### 2.6 Scale Down

계속하기 전에 애플리케이션을 단일 인스턴스로 축소하십시오.

```bash
oc scale --replicas=1 deploy/mapit
```



### 2.7 Application Probes

OpenShift는 애플리케이션 인스턴스의 활성 및 또는 준비 상태를 확인하는 기본적인 기능을 제공합니다. 기본 검사가 불 충분한 경우 OpenShift는 검사를 수행하기 위해 **Pod**/container 내에서 명령을 실행할 수도 있습니다. 해당 명령은 컨테이너 이미지 내에 이미 설치된 언어를 사용하는 복잡한 스크립트일 수 있습니다.

정의할 수 있는 두 가지 형의 애플리케이션 Probes가 있습니다.



- **Liveness Probe**

  liveness probe는 구성된 container가 여전히 실행 중인지 확인합니다. liveness probe가 실패하면 container가 종료되고 재시작 정책이 적용됩니다.

- **Readiness Probe**

  readiness probe는 container가 요청을 처리할 준비가 되었는지 확인합니다. readiness probe가 실패하면 엔드포인트의 컨트롤러는 일치해야 하는 모든 서비스의 엔드포인트에서 container의 IP 주소가 제거되었는지 확인합니다. readiness probe를 사용하여 container가 실행 중이더라도 트래픽을 수신해서는 안 된다는 엔드포인트의 컨트롤러에 신호를 보낼 수 있습니다.



​		더 자세한 설명은 [애플리케이션 상태](https://docs.openshift.com/container-platform/4.11/applications/application-health.html) 섹션에서 확인할 수 있습니다.



### 2.8 Add Probes to the Application

`oc set` 명령을 사용하여 여러 가지 기능을 수행할 수 있으며 그 중 하나는 probe 생성 및 또는 수정입니다. `mapit` 애플리케이션은 살아 있고 응답할 준비가 되었는지 확인할 수 있는 엔드포인트를 노출합니다. `curl`을 사용하여 테스트할 수 있습니다.

```bash
curl mapit-app-management.apps.ocp4.sandbox298.opentlc.com/health
```

응답으로 일부 JSON을 받게 됩니다.

```bash
{"status":"UP","diskSpace":{"status":"UP","total":128300593152,"free":104485945344,"threshold":10485760}}
```

다음 명령을 사용하여 이 엔드포인트의 활성 여부를 조사하도록 OpenShift에 요청할 수 있습니다.

```bash
oc set probe deploy/mapit --liveness --get-url=http://:8080/health --initial-delay-seconds=30
```

그러면 이 probe가 `oc describe` 출력에 정의되어 있음을 확인할 수 있습니다.

```bash
--- 생략 ---
Pod Template:
  Labels:       deployment=mapit
  Annotations:  openshift.io/generated-by: OpenShiftNewApp
  Containers:
   mapit:
    Image:        quay.io/openshiftroadshow/mapit@sha256:983bc150d53a370c7825dbc42bf1d00adc951bb2e367c149fd7041114ba5da4f
    Ports:        8080/TCP, 8778/TCP, 9779/TCP
    Host Ports:   0/TCP, 0/TCP, 0/TCP
    Liveness:     http-get http://:8080/health delay=30s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
--- 생략 ---
```

마찬가지로 동일한 방식으로 readiness probe를 설정할 수 있습니다.

```bash
oc set probe deploy/mapit --readiness --get-url=http://:8080/health --initial-delay-seconds=30
```



### 3. Examining Deployments and ReplicaSets

**Deployment**에 대한 각 변경은 새 *deployment*를 *triggers*하는 구성 변경으로 계산됩니다. **Deployment**는 배포할 **Replicaset**을 담당합니다. 항상 *최신* 버전이 배포됩니다.



다음을 실행합니다:

```bash
oc get deployments
```

다음과 같은 내용이 표시됩니다.

```bash
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
mapit   1/1     1            1           6h25m
```

초기 배포이후 두 가지 구성 설정 변경(및 확장)을 수행했으므로 이제 **Deployment**의 네 번째 개정에 있습니다.

다음을 실행합니다:

```bash
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
mapit   1/1     1            1           6h25m
```

다음과 같은 내용이 표시됩니다.

```bash
NAME               DESIRED   CURRENT   READY   AGE
mapit-668d959f88   0         0         0       8m18s
mapit-68ddc9cfdc   0         0         0       6h26m
mapit-78bb898c9f   1         1         1       6m24s
mapit-7f5b86b6bc   0         0         0       6h26m
```

새 배포가 trigger될 때마다 배포자 Pod는 Pod가 존재하는지 확인하는 역할을 하는 새 **Replicaset**을 생성합니다. 이전 RS의 원하는 스케일은 0이고 가장 최근의 RS가 원하는 스케일은 1입니다.



이러한 각 RS를 설명하면 이전 버전에는 probes가 없고 최신 실행 RS에는 새 probe가 있음을 알 수 있습니다.



