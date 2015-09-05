---
layout: post
title: "삽입정렬을 통해 본 구현 코드에 따른 성능 차이"
categories: java sort insertion_sort
---

#정렬과 성능
잠시 정렬 알고리즘을 살펴보다 재밌는 현상을 보게되어 이를 정리하고자 한다.

#정렬 알고리즘
정렬(Sort)를 모르는 사람은 없을 것이다. 버블정렬, 삽입정렬, 선택정렬, 병합정렬, 퀵정렬, 카운팅정렬 등 수 많은 정렬 알고리즘이 존재한다. Java같은 경우 내부적으로 정렬과 괕련된 기능을 제공하기도 한다. Collections.sort나 Arrays.sort 같은 것들 말이다. 각 알고리즘들은 다시 여러가지의 구현방법이 존재한다. 같은 삽입정렬이라도 어떻게 구현하느냐에 따라서 성능에 차이를 보인다. 이번에는 한번 삽입정렬 구현에 대해서 이야기해 보고자 한다. 삽입정렬을 구현하는 몇 가지 방법을 소개하고 그 안에 숨어있는 **함정**을 볼 것이다.

#삽입정렬
사실 삽입정렬은 가장 기본에 되는 정렬 중 하나로 모르는 사람은 드물 것이다. 최소한 정보처리기사 문제도 등장하곤 하니 누구나 한번 쯤은 이 알고리즘을 접했을 것이다. 그래도 간단히 다시 한번 집고 넘어가자면, 삽입정렬은 자신(정렬한 데이터)을 이미 정렬된 앞부분의 데이터들과 비교해서 자신이 들어갈 위치를 찾아 삽입하는 정렬 알고리즘이다. 선택 정렬이나 거품 정렬 처럼 O(n^2) 알고리즘이지만 이 둘보다는 빠른 성능을 부여준다. 그리고 구현에 따라 안전 정렬이 될 수도 있고 비 안전 정렬이 될 수도 있다.

#삽입정렬 구현

	public void sort(T[] dataset) {
		int size = dataset.length;
		
		for(int i=1 ; i<size ; i++){
			T data = dataset[i];
			int j = i-1;
			while(j>0){
				if(data.compareTo(dataset[j]) < 0){		
					dataset[j+1] = dataset[j];			
				}else
					break;
				j--;
			}
			dataset[j] = data;
		}
	}

위 코드는 삽입정렬를 구현한 코드의 한 예이다. 전달 받은 배열을 삽입정렬 알고리즘을 통해 정렬해준다. sortOld메서드를 가지고 있는 클래스는 제네릭으로 선언되어 있으며 T는 아래와 같이 정의되었다.

	<T extends Comparable<? super T>>
	
제네릭 타입 T는 Comparable인터페이스를 구현하고 있어야한다. 그래야지 대소 구분을 해서 정렬을 할테니 말이다. 그래서 대소를 구분하는 부분은 Comparable인터페이스의 compareTo메서드를 사용하고 있다. 

그런데 다른 방식으로 구현한 코드를 보게 되었다. 아래의 코드를 봐보자.

	public void sort(T[] dataset){
		List<T> sortedList = new LinkedList<>();
		
		datasetFor:for(T data : dataset){
			for(int i=0 ; i<sortedList.size() ; i++){
				if(sortedList.get(i).compareTo(data) > 0){
					sortedList.add(i, data);
					continue datasetFor;
				}
			}
			sortedList.add(sortedList.size(), data);
		}
		
		for(int i=0 ; i<dataset.length ; i++){
			dataset[i] = sortedList.get(i);
		}
	}

처음 이 코드를 봤을때 꽤 창의적이라고 생각했었다. 정렬된 데이터들을 보관하는 리스트를 따로 만들어 이를 통해서 검사를 하고 삽입이 이루어지고 있다. if문의 비교문을 만족하는 데이터가 없는 경우에는 정렬된 리스트의 마지막 노드로 삽입하고 있다. 마지막에는 for문을 통해 원본배열에 데이터를 옮기고 있다.

그런데 이 코드(두번째 코드)에는 매우 큰 문제가 존재한다. 이 코드를 통해 테스트를 해보았다. 수 만개의 데이터를 가지고 정렬을 시도했을때 첫 번째 코드는 빠르게 정렬이 완료되었지만, 두 번째 코드는 끝날 기미를 보이지 않았다. 코드만 봐도 두 번째 코드가 더 느리게 보이긴 한다. 근데 너무 차이가 난다. 정렬 작업이 끝나지가 않는다. 무엇이 문제일까? 새로운 정렬 리스틀 생성해서 그럴까? 그러면 사용하는 공간이 2배로 늘어날테니까? 마지막에 정렬된 수만개의 데이터를 다시 원본 배열로 옮기는 과정 때문일까? 둘다 아니다. 문제는 바로 대소를 구분하는 부분의 'sortedList.get(i)' 때문이다! 이 부분 때문에 O(n^2)의 시간복잡도를 가지는 삽입정렬이 O(n^3)으로 늘어나 버린다. 왜 일까?

그건 sortedList가 LinkedList 객체라는 점 때문이다! LinkedList는 안타깝게도 임의접근이 안된다. i번째 노드에 접근하기 위해서는 i번 반복해서 노드를 이동해야한다. 최악의 경우에는 맨 마지막까지 이동해서 리스트의 길이 만큼 이동해야한다. 이 때문에 'sortedList.get(i)'부분은 O(n)복잡도를 가지며, 결국 전체적으로보면 O(n^3)인 알고리즘이 되어버린다. add메서드도 문제다. add(i, data)의 경우 i번째에 data를 삽입하라는 의미인데, 이도 결국 i번 반복해서 해당 노드로 이동해야한다. 이때문에 기하급수적으로 늘어나버린 수행시간 때문에 정렬 작업이 끝날 기미를 보이지 않던 것이다.

이러한 문제를 고치기 위해서 아래와 같이 코드를 고쳐야할 것이다. get(i)통해 매번 i씩 반복하는 것이 아니라, Iterable 특성을 사용하는 것이다. 이렇게 고쳐도 첫 번째 코드보다는 느리긴 하지만 그래도 정렬이 끝나긴 끝난다.

	public void sort(T[] dataset) {
		List<T> sortedList = new LinkedList<>();
		
		datasetFor:for(T data : dataset){
			int i=0;
			for(T sortedData : sortedList){
				if(sortedData.compareTo(data) > 0){
					sortedList.add(i, data);
					continue datasetFor;
				}
				i++;
			}
			sortedList.add(sortedList.size(), data);
		}
		
		//하는 김에 이것도 깔끔하게 수정
		sortedList.toArray(dataset);
	}
