# 미니 랩: AKS를 사용하여 Kubernetes 배포

이 미니 랩에서는 다음을 수행합니다.

* 새 리소스 그룹을 만듭니다.

* 클러스터 네트워킹을 구성합니다.

* Azure Kubernetes Service 클러스터를 만듭니다.

* kubectl을 사용하여 Kubernetes 클러스터에 연결합니다.

* Kubernetes 네임스페이스를 만듭니다.

## 새 리소스 그룹 만들기

먼저 리소스가 배포할 리소스 그룹을 만들어야 합니다.

1. Azure 계정으로 Azure Cloud Shell에 로그인합니다. Cloud Shell의 Bash 버전을 선택합니다.

[Azure Cloud Shell](https://shell.azure.com/)

2. 예를 들어, **미국 동부**와 같은 리소스 그룹을 만들 지역을 선택해야 합니다. 다른 값을 선택하는 경우 이 모듈의 나머지 연습에 대해 기억하세요. Cloud Shell 세션 간의 값을 재정의해야 할 수 있습니다. 다음 명령을 실행하여 Bash 변수에서 이러한 값을 기록합니다.

```Azure CLI
REGION_NAME=eastus
RESOURCE_GROUP=aksworkshop
SUBNET_NAME=aks-subnet
VNET_NAME=aks-vnet
 ```

에코 명령(예: echo ```$REGION_NAME```)을 사용하여 각 값을 확인할 수 있습니다.

3. *aksworkshop* 이라는 새 리소스 그룹을 만듭니다. 이러한 연습에서 만든 모든 리소스를 이 리소스 그룹에 배포합니다. 단일 리소스 그룹을 사용하면 모듈을 완료한 후 리소스를 쉽게 정리할 수 있습니다.

```Azure CLI
az group create \
    --name $RESOURCE_GROUP \
    --location $REGION_NAME
```

## 네트워킹 구성

AKS 클러스터를 배포할 때 선택할 수 있는 두 가지 네트워크 모델이 있습니다. 첫 번째 모델은 *Kubenet 네트워킹* 이고 두 번째 모델은 *Azure CNI(컨테이너 네트워킹 인터페이스) 네트워킹*입니다.

## Kubenet 네트워킹

Kubenet 네트워킹은 Kubernetes의 기본 네트워킹 모델입니다. Kubenet 네트워킹을 사용하면 노드에서 Azure Virtual Network 서브넷의 IP 주소가 할당됩니다. Pod는 논리적으로 다른 주소 공간에서 노드의 Azure Virtual Network 서브넷에 대한 IP 주소를 받습니다.

그런 다음 (NAT) Network Address Translation 가 구성되어 Pod가 Azure Virtual Network의 리소스에 도달할 수 있도록 합니다. 트래픽의 소스 IP 주소는 노드의 기본 IP 주소로 변환된 다음 노드에서 구성됩니다. Pod는 노드 IP 뒤에 "숨겨진" IP 주소를 받습니다.

## Azure (CNI) Container Networking Interface 네트워킹

Azure (CNI) Container Networking Interface 를 사용하여 AKS 클러스터가 기존 가상 네트워크 리소스 및 구성에 연결됩니다. 이 네트워킹 모델에서는 모든 Pod가 서브넷에서 IP 주소를 받고 직접 액세스할 수 있습니다. 이러한 IP 주소는 네트워크 공간 전반에 걸쳐 고유해야 하며 사전에 계산해야 합니다.

사용할 기능 중 일부는 *Azure Container Networking Interface 네트워킹* 구성을 사용하여 AKS 클러스터를 배포해야 합니다.

아래에서 AKS 클러스터에 대한 가상 네트워크를 만듭니다. 클러스터를 배포할 때 이 가상 네트워크를 사용하고 네트워킹 모델을 지정합니다.

1. 먼저, 가상 네트워크 및 서브넷을 만듭니다. 클러스터에 배포된 Pod에 이 서브넷의 IP가 할당됩니다. 다음 명령을 실행하여 새 가상 네트워크를 만듭니다.

```Azure CLI
az network vnet create \
    --resource-group $RESOURCE_GROUP \
    --location $REGION_NAME \
    --name $VNET_NAME \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name $SUBNET_NAME \
    --subnet-prefix 10.240.0.0/16
```

2. 다음으로 아래 명령을 실행하여 Bash 변수에서 서브넷 ID를 검색하고 저장합니다.

```Azure CLI
SUBNET_ID=$(az network vnet subnet show \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --query id -o tsv)
```

## AKS 클러스터 만들기

새 가상 네트워크가 배치되면 새 클러스터를 만들 수 있습니다. ```az aks create``` 명령을 실행하기 전에 알아야 할 두 가지 값이 있습니다. 첫 번째는 선택한 지역에서 사용할 수 있는 최신의 비 미리 보기 Kubernetes 버전이며, 두 번째는 클러스터의 고유한 이름입니다.

1. 최신의 비 미리 보기 Kubernetes 버전을 얻으려면 ```az aks get-versions``` 명령을 사용합니다. 명령에서 반환되는 값을 ```VERSION```이라는 Bash 변수에 저장합니다. 검색 아래 명령을 실행하고 버전 번호를 저장합니다.

```Azure CLI
SUBNET_ID=$(az network vnet subnet show \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --query id -o tsv)
```

2. AKS 클러스터 이름은 고유해야 합니다. 다음 명령을 실행하여 고유한 이름을 포함하는 Bash 변수를 만듭니다.

```Bash
AKS_CLUSTER_NAME=aksworkshop-$RANDOM
```

3. 다음 명령을 실행하여 ```$AKS_CLUSTER_NAME```에 저장된 값을 출력합니다. 이 값은 나중에 사용하기 위한 겁니다. 필요한 경우 나중에 변수를 다시 구성해야 합니다.

```Bash
echo $AKS_CLUSTER_NAME
```

4. 다음 ```az aks create``` 명령을 실행하여 최신 Kubernetes 버전을 실행하는 AKS 클러스터를 만듭니다. 이 명령을 완료하려면 몇 분 정도 걸립니다.

```Azure CLI
az aks create \
--resource-group $RESOURCE_GROUP \
--name $AKS_CLUSTER_NAME \
--vm-set-type VirtualMachineScaleSets \
--load-balancer-sku standard \
--location $REGION_NAME \
--kubernetes-version $VERSION \
--network-plugin azure \
--vnet-subnet-id $SUBNET_ID \
--service-cidr 10.2.0.0/24 \
--dns-service-ip 10.2.0.10 \
--docker-bridge-address 172.17.0.1/16 \
--generate-ssh-keys
```

## kubectl을 사용하여 클러스터 연결 테스트

```kubectl```은 클러스터와 상호 작용하는 데 사용하는 주요 Kubernetes 명령줄 클라이언트로서 Cloud Shell에서 사용할 수 있습니다. 클러스터 컨텍스트는 클러스터에 ```kubectl```을 연결할 수 있어야 합니다. 컨텍스트에는 클러스터의 주소, 사용자 및 네임스페이스가 포함됩니다. az aks get-credentials를 사용하여 인스턴스 ```kubectl``` 을 구성합니다.

1. 아래 명령을 실행하여 클러스터 자격 증명을 검색합니다.

```Azure CLI
az aks get-credentials \
    --resource-group $RESOURCE_GROUP \
    --name $AKS_CLUSTER_NAME
```

2. 클러스터의 모든 노드를 나열하여 배포된 내용을 모두 확인할 수 있습니다. ```kubectl``` get nodes 명령어를 사용하여 모든 노드를 나열합니다.

```Bash
kubectl get nodes
```

클러스터의 노드 목록이 표시됩니다. 예로 다음이 있습니다.

```Ouput
이름                                상태   역할   연령  버전
aks-nodepool1-24503160-vmss000000   Ready    agent   1m   v1.15.7
aks-nodepool1-24503160-vmss000001   Ready    agent   1m   v1.15.7
aks-nodepool1-24503160-vmss000002   Ready    agent   1m   v1.15.7
```

## 애플리케이션에 대한 Kubernetes 네임스페이스 만들기

Kubernetes의 네임스페이스는 논리적 격리 경계를 만듭니다. 리소스 이름은 네임스페이스 내에서 고유해야 하지만 네임스페이스 간에는 고유하지 않아도 됩니다. Kubernetes 리소스로 작업할 때 네임스페이스를 지정하지 않아도 네임스페이스가 기본으로 포함되어 있습니다.

등급 애플리케이션에 대한 네임스페이스를 만듭니다.

1. 클러스터에 현재 네임스페이스를 나열합니다.

```Bash
kubectl은 네임스페이스를 얻습니다.
```

이 출력과 유사한 네임스페이스 목록을 확인할 수 있습니다.

```Bash
이름               상태   연령
기본               활성   1시간
kube-node-lease   활성   1시간
kube-public       활성   1시간
kube-system       활성   1시간
```

2. ```kubectl```을 사용하여 네임스페이스 명령을 만들어 **ratingsapp**이라는 애플리케이션에 대한 네임스페이스를 만듭니다.

```Bash
kubectl create namespace ratingsapp
```

네임스페이스가 만들어졌다는 확인이 표시됩니다.

```Output
namespace/ratingsapp created
```


 
