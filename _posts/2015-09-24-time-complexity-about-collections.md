---
layout: post
title: "Java Collections의 시간복잡도"
categories: java map
---

#Java Packages for Data Structure
LinkedList, HashMap 등 Java는 수 많은 자료구조를 구현한 클래스가 존재한다. 각 자료구조가 특징을 가지고 있듯이 각 컬랙션 클래스는 모두 수행시간이 다르다. 이번에는 자주 사용하는(저자 기준) 몇몇 컬래션 클래스들의 수행시간을 시간복잡도을 표현하는 방법 중 하나인 빅 O 표기법으로 정리한다. 
정리를 위해 **Information Technology Gems**이라는 블로그를 참고하였다. 아래의 내용은 그 블로그의 내용을 요약 정리한 것이다. 그런데 몇 개 **납득이 안가는 부분**이 있는데 이부분은 좀 더 생각해봐야겠다.

#List

{: .table .table-striped .table-bordered}
Class Name	|Add	|Remove		|Get	|Contains
---|---|---|---|---
ArrayList	|O(1)	|O(n)		|O(1)	|O(n)
LinkedList	|O(1)	|O(1)		|O(n)	|O(n)

#Set

{: .table .table-striped .table-bordered}
Class Name		|Add		|Contains	|Next
---|---|---|---
HashSet			|O(1)		|O(1)		|O(h/n)
LinkedHashSet	|O(1)		|O(1)		|O(1)
EnumSet			|O(1)		|O(1)		|O(1)
TreeSet			|O(log n)	|O(log n)	|O(log n)

#Queue

{: .table .table-striped .table-bordered}
Class Name		|Offer		|Peak	|Poll		|Size
---|---|---|---|---
PriorityQueue	|O(log n)	|O(1)	|O(log n)	|O(1)
LinkedList		|O(1)		|O(1)	|O(1)		|O(1)
ArrayDequeue	|O(1)		|O(1)	|O(1)		|O(1)
DelayQueue		|O(log n)	|O(1)	|O(log n)	|O(1)

#Map

{: .table .table-striped .table-bordered}
Class Name		|Get		|ContainsKey	|Next
---|---|---|---
HashMap			|O(1)		|O(1)			|O(h/n)
LinkedHashMap	|O(1)		|O(1)			|O(1)
WeakHashMap		|O(1)		|O(1)			|O(h/n)
EnumMap			|O(1)		|O(1)			|O(1)
TreeMap			|O(log n)	|O(log n)		|O(log n)

---

#Reference
- **[Information Technology Gems: Java Collections – Performance (Time Complexity)](http://infotechgems.blogspot.kr/2011/11/java-collections-performance-time.html)**