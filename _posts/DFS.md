---
title : "Distributed File Systems(01)"
excerpt : "MapReduce"
categories :	
 - Data Mining
 - MapReduce
tags :
 - Big Data
 - Distributed File Systems
 - Commodity Linux Node
---

[원본 강의링크](https://www.youtube.com/watch?v=xoA5v9AO7S0&list=PLLssT5z_DsK9JDLcT8T62VtzwyW9LNepV&index=2&t=1s)



이 포스팅의 개요는 다음과 같습니다. 

우선 고전적인 파일 처리 방식에 대해 언급하고

그 문제점에 대해 언급합니다. 

이를 해결하기 위한 방식으로 

Clustering computing 를 제시합니다.

clustering computing 기법에서의 발생하는 이슈 3가지에 대해

말한 후 

 이를 해결하기 위한 방식으로 

MapReduce 에 대해 언급합니다. 

즉, 이번 포스팅은 MapReduce 의 등장배경과 필요성에 대한 내용입니다.



---



프로그램은 메모리에 올라가고 CPU에서 처리합니다.

파일 작업을 하기 위해서는 파일이 메모리 안에 있어야 합니다.

데이터의 크기가 커서 메모리에 다 들어가 못하는 경우가 있습니다.

메모리 용량을 높이는 것은 한계가 있습니다.

때문에 disk를 사용합니다. 

disk 에 있는 메모리를 필요할 때 메모리에 가져와 처리합니다. 



하지만 이러한 방식에는 한계가 있습니다. 



예를 들어 

구글 검색 엔진을 봅신다. 

10Billion 개의 웹페이지가 있고

웹페이지 하나 당 20kB 라면

200TB 를 처리해야합니다. 

CPU 가 데이터를 처리하기 위해서는

disk 에서 읽어와야 합니다.

읽어 오는데에는 시간이 소요되고

시간 당 데이터를 읽어 오는 것을 Data bandwidth 라 합니다 .

이 bandwidth 를 50m/s 라 가정한다면 (1초당 50mb) 

웹페이지를 읽는데 46일 이상이 소요됩니다. 

때문에 이러한 방식은 실사용이 어렵습니다. 



이를 위한 대안으로 clustering computing 이 제시됩니다. 

작업을 병렬적으로 처리할 수 있도록 여러개의 컴퓨터를 연결하는 것으로 보시면 됩니다. 

하나의 데이터를 여러개의 chunk 로 나누고

여러개의 disk와 cpu 를 사용합니다. 

각각의 cpu들이 disk에 있는 데이터들을 동시에 읽으면서(병렬적으로) 처리합니다. 

이런 방식으로 이어붙여 속도를 향상시킬 수 있습니다. 

1000개의 CPU,disk 가 있다면 46일이 4000초로 단축될 수 있습니다. 



클러스터링 컴퓨팅을 어떻게 구성하는지 살펴봅시다. 

commodity linux node를 사용합니다. 이는 가격이 쌉니다.

그리고 rack이 필요합니다. 

rack 은 말 그대로 다음과 같은 선반입니다. 



이 rack 에는 16~64 개의 commodity linux node 를 설치할 수 있습니다. 

rack 안의 node 들은 switch 로 연결이 되어 있습니다. 

일반적으로 node 간 switch의 bandwidth 는 1Gbps 정도가 됩니다.



하나의 rack으로 최대 64개의 node 로 병렬처리, 즉, 64배의 속도를 향상이 가능합니다. 

이렇게해도 부족한 경우

여러개의 rack 을 switch 로 붙여 사용합니다.

rack의 switch 들을 backbone switch 로 연결합니다. 

이 backbone switch 는 rack의 swich 보다 더 높은 bandwidth를 갖는 것을 사용합니다.

만약 64개의 node 를 갖는 rack들 64개를 연결한다면

최대 2^12 배의 속도를 향상시킬 수 있습니다. 



이렇게 tree 구조로 병렬화 하는 구조도 

대규모 데이터를 처리하는데 완전한 해결책을 주진 못합니다. 

다음과 같은 문제들이 발생합니다.

1. Node failure
2. Network bottleneck
3. Programming



노드 패일은 [여기](https://galeracluster.com/library/documentation/recovery.html) 

노드패일은 말 그대로 동작에 실패하는 것을 말합니다. 

이는 여러가지 이유로 발생할 수 있습니다.@@

노드가 하나일 때는 node failure 가 자주 발생하지 않습니다. 

연결된 노드가 많을수록 많이 발생하게 됩니다. 

전체가 연결되있을 때 하나의 노드만 failure 가 되더라도 

전체가 fail 이 됩니다. 



데이터는 다음과 같은 특성을 보장해야합니다. 

- Persistence  데이터를 저장하면 그것을 다시 read 하는 것을 보장해야한다. 
- availability : 작업은 계속 수행되어야 한다.

데이터를 저장하는데 failure 가 발생하는 경우 데이터를 읽게 되고 

node failure 가 발생하면 해당 노드안에 있는 데이터에 대해 수행할 수 없으므로 

작업이 중단되게 된다.



Network bottleneck 

데이터를 move 해야하는 경우 발생합니다. 

switch 의 bandwidth 가 1Gbps 인 경우 

아무리 병렬로 처리를 할 수 있더라도 

데이터를 옮기는데에는 1초에 1GB 가 최대입니다. 

때문에 이동시 bottleneck 이 발생하게 됩니다. 



Programming hard 

이런 식으로 분산시스템을 구현하는 것은 매우 어렵습니다. 

race condition 이나 병렬로 구현시 발생할 수 있는 다양한 상황에 대해 

처리를 해야하기 때문에 구현이 어렵습니다.  



이러한 문제들은 MapReduce 를 통해 해결할 수 있습니다.

1. 여러개의 노드에 중복하여 데이터를 저장합니다. 
   동일한 데이터가 다른 노드에 저장되므로 특정 노드에서 node failure 가 발생하더라도
   다른 노드에서 수행할 수 있습니다.
2. bottleneck
   데이터 이동에는 제약이 있으므로 데이터를 이동시키지 않고 
   데이터가 있는 곳에서 컴퓨팅을 합니다. 
3. 단순하게 구현할 수 있습니다. 



1.Redundunt storage infrastructure 





---



