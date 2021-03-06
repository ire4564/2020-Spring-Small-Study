오늘은 저번에 reace condition을 해결하는 방법으로 두 가지가 있었던 뮤텍스와 세마포어 방법에 대해 리눅스를 이용하여 직접 실습을 진행해보았다. 두 개의 쓰레드를 생성해서 프로세스들이 어떻게 동작하는지를 보고, 이것을 해결하기 위해 각각 뮤텍스와 세마포어의 방법을 사용해보았다.

 

1. 일반적인 경우 mutual

처음에 코드를 작성한 다음, 컴파일을 시도했는데 계속 'undefined reference' 오류가 떠서 애를 좀 먹었다.

저번에 thread 헤더를 가져오면 -lpthread 옵션을 필수로 입력해야 하는 사실을 까먹고 있다가, 검색 후에 떠올려서 이 옵션으로 컴파일을 진행했더니 정상적으로 컴파일이 되었다.

 


컴파일을 한 파일을 실행시켜보았다. 실행 결과는 In과 out이 프로세스하나가 종료되었는지 여부에 상관없이 그냥 마구 생성되고 마구 순서없이 끝나는 모습을 보았다. 이 문제를 해결하기 위해서 뮤텍스와 세마포어 방법을 사용해보려고 한다. 사용하기 전에, 뮤텍스와 세마포어 방법이 아니라도 key를 체크하는 방식으로 이 문제를 해결할 수 있다.

 


우선, 초기에 작성한 코드는 위와 같다. 이제 이 코드에 mutex와 semaphore 방법을 사용해보겠다.

 

 

2. mutex

뮤텍스 방법은, Critical Section에 대해 한 번에 하나의 쓰레드만을 허용한다. 한 쓰레드가 소유가 가능하려면, 기존에 소유하고 있는 쓰레드가 해제를 해야한다. 그림으로 보면 이해가 될 것이다.

 


기존의 코드에 pthread_mutex_t 변수등을 추가해주고, init과 join 함수를 써서 mutex 방법을 시도해보았다. mutex와 관련된 함수들은 아래와 같은 여러 함수들이 있다. 이중에서 우리는 mutex를 생성하고, 제거하는 방법을 사용하였다.

 


 

Mutex 방법을 적용하고 실행시킨 결과는 아래와 같다. 순서와 상관없이 마구 생성되고 종료되는 이전의 결과와는 달리 생성되고, 종료되고, 생성되고 종료되는 정돈된 실행을 보여주고 있다.


 

3. semaphore

세마포어 방법은 최대 허용치만큼 동시 접근이 가능하게 하는 방법이다. 그래서 특정 프로세스나 쓰레드를 소유하지 않는다. 아래의 실습에서는 key 값을 4로 주면서 최대 허용치를 4로 정하여 실행해보았다.

 


결과는 Mutex와 달리 생성되고, 종료될때까지 기다리는 것이 아니라 최대치를 4로 설정했으므로, 4개를 동시 접근 가능하도록 하고, 한번에 끝내는 것을 볼 수 있다. 그리고 남은 것은 In과 Out을 돌고 작업을 끝낸다.

 

세마포어와 관련된 함수들은 아래와 같다. 우리는 이중에서 세마포어를 할당하고, 반환하고, lock과 unlock을 하는 동작의 함수를 사용해보았다.

 


 사용한 코드는 밑의 화면과 같다.



 

Mutex와 semaphore에 대한 실습을 진행해보았다. 이론상으로는 알고 있었는데, 실습을 진행해보니 어떻게 사용하는 것인지 감이 왔다. 나머지 이론도 정리 후에 다시 코드를 보고 ping pong 게임을 만들어 볼 예정이다.
