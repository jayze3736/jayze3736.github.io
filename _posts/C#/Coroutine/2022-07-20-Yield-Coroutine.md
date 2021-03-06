---
title: 'Yield & Coroutine'
category: C#
tag: Coroutine
layout: post

---
- monobehaviour 과 new의 관계
- yield 
- coroutine

# 1. yield
yield는 yield return 또는 yield break로 사용한다.
yield return은 iterator(enumrator)에서 값을 반환한다.
yield break는 반복을 멈추고 빠져나오는데 사용된다.

yield return의 특징은 반복과정에서 호출할때마다 지연 실행을 구현하는 것이 가능하다.
예를 들어, 10 X 10의 행렬의 값을 각기 다른 값으로 초기화한 후 해당 행렬을 반환하는 경우
100개의 요소값을 한번에 반환하는 것이 아닌, 10개씩 반환하는 것이 가능하다.
이처럼 yield return과 반복문을 사용하면 반환할 값을 전부 반환하지않고 반복문 분기마다 반환하므로써 반환 시점이
딜레이 되는(불연속적이고 떨어지는) 지연 반환이 가능하다.  

## + Enumerator
Enumeration이 가능한 클래스를 Enumerable 클래스라고 하며, Enumeration은 Iteration이 가능한 메소드를 가지고있을때를 반복을 의미한다.
Enumerable 클래스는 IEnumerator GetEnumerator()라는 메소드를 가지고있는데 GetEnumerator()은 yield return을 사용하여 반복기 내에서
값을 순차적으로 반환한다. 

## + yield의 처리 순서
반복문 내에서 yield를 만났을때 값을 호출자에게 반환하고 다시 루프로 돌아가서 yield 아래부분을 실행한다.


# 2. coroutine
1. 보통 함수의 처리는 한 프레임 이내에 모든 처리가 이루어진다. 그러나 함수의 처리가 한 프레임이 아니라 특정 시간내에 처리되도록 제어하기 위해 사용되는 것이 코루틴이다.  
2. 코루틴은 Startcoroutine을 사용하여 코루틴을 실행시킨다.  
3. 코루틴은 IEnumerator를 반환하고 yield return이 존재하는 메소드를 의미한다.  
4. 코루틴이 시작되면 yield return을 만나기까지 함수의 코드를 처리하고 만약 yield return을 만나면 코루틴의 처리를 멈추고(pause) 호출지점으로 돌아갔서 값을 반환한다음 다시 코루틴을 재개하여 yield 아래의
코드를 처리한다.  
5. yield return null: 딜레이 없이 단순히 한 프레임 내에 처리하도록 하는 경우  
yield return new WaitforSeconds(): 딜레이를 줄 경우   
7. 코루틴의 퍼포먼스: 결론부터 얘기하자면 코루틴은 함수 처리 제어에 있어서 유용하고 가독성을 높일 수 있지만 메모리상에서 비교적 꽤 큰 오버헤드가 존재한다.   
이유는 유니티에서 코루틴을 실행하면 실행되는 지점은 한 곳이지만 실제로 실행되는 지점은 두곳을 왔다갔다하면서 코루틴을 실행한다.   
그 지점은 첫번째로 yield이고 두번째는 DelayCallManager라는 함수에의해 실행된다.   
코루틴을 실행할때 함수가 한 번에 처리되지않으므로 코루틴 내에 정의된 지역 변수값을 코루틴이 끝나기전까지 기억해둘 필요가 있는데, 이 지역 변수 정보와 코루틴의 진행 상황을 기록한
인스턴스를 생성한다.   
그리고 그 인스턴스로 코루틴의 현재 정보를 추적하는데 이때 하나의 함수로부터 여러개의 함수가 호출된다.    
결론적으로 yield로 반환 지점으로 돌아가는 오버헤드, Delay Call manager에의해 yield 아래의 코드를 실행될때의 오버헤드, 인스턴스를 통한 코루틴 추적에 사용되는 오버헤드
따라서 코루틴은 비교적 많은 오버헤드가 존재한다는 것을 알 수 있다.    
