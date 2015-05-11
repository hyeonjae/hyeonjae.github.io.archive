---
title:  "배열 shuffling 하기"
date:   2015-05-08 09:32:40
categories: algorithm
---


크기 N짜리 배열을 섞는 방법에 대해 생각해보았다.
 
 
### 방법1.
 
우선 배열의 두 수를 무작위로 뽑아 서로 자리를 바꾼다.
여러번 충분히 섞일 때 까지 반복한다.

{% highlight c %}
while(++i < NUM_ITERATION ){
    swap( &arr[rand()%N], &arr[rand()%N] );
}
{% endhighlight %}
 
### 방법2.
{% endhighlight %}
int comp(const void* n1, const void* n2){
    return rand()%2;
}
 
srand(time(NULL));
qsort(arr, N, sizeof(int), comp);
{% endhighlight %}

위의 코드에서 qsort의 비교 연산을 랜덤하게 바꾼 것이다. 50%확률로 두 변수를 swap한다.
실제로 테스트해보면 값이 어지럽게 나온다.
그럼 귀찮게 shuffle 코드를 구현하지 않고 위처럼 qsort를 이용해도 되는 것일까?


### 방법3.
 
이것도 random하게 섞인 것일까?

{% highlight c %}
int n = N;
for(i=0; i<N; i++){
    int r = rand()%n;
    swap( &arr[rand()%N], &arr[--n] );
}
{% endhighlight %}
딱 N번만에 섞었다.
  
---
  
  
방법 2에 대해선 stackoverflow에서 어떤 아저씨가 답해주었다. unpredictable 하지만 random한 것은 아니라고...
 
자세히 보면 shuffle하기 전의 값들이 남아있는 걸 볼 수 있다.


방법 3은 이전글 배열에서 중복하지 않고 무작위로 숫자 뽑기와 관련이 있다. 중복하지 않고 무작위로 배열의 크기만큼 숫자를 뽑으면 shuffling 된 것과 같다. 역시 배열의 크기 N번 만큼만 루프를 돌고 shuffling하였다. 이 방법은 [Fisher-Yates-Shuffle](http://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle) 이라고 한다.