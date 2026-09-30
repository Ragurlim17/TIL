# Camera
```
using UnityEngine;

public class Camera : MonoBehaviour
{
    public Transform target;
    public Vector3 offset = new Vector3(0, 5, -10);
    public float followSpeed = 10.0f;

    void LateUpdate()
    {
        if(target == null) return;

        Vector3 targetPosition = target.position+offset;

        transform.position = Vector3.Lerp(transform.position, targetPosition, Time.deltaTime*followSpeed);
    }
}
```
# 기본 설정
### ``` public Vector3 offset = new Vector3(0, 5, -10);```
**offset**이 뭔데?
* 카메라가 추적 대상으로부터 얼마만큼 떨어진 위치에 있을지 정해주는 상대적인 거리/위치값

**offset**을 사용하지 않고 무작정 *target.position* 으로 카메라 위치를 정하게 된다면,
카메라가 추적 대상(오브젝트) 안으로 들어가버리는 사태가 발생

각각의 축들이 하는 역할
* X축(0): 오브젝트의 좌우 중앙에 위치
* Y축(5): 오브젝트보다 위로 (5)만큼 높은 곳에 위치 *(=위에서 내려다 보는 시점)*
* Z축(-10): 오브젝트보다 뒤로 (-10)만큼 떨어진 곳에 위치 *(=3인칭 시점 확보)*

---
# Void LateUpdate()
**LateUpdate()** 가 뭔데?

일반적인 *Update()* 구문이 다 마무리된 뒤에 **제일 마지막**에 호출되는 유니티 이벤트 함수
### ```if(target == null) return;```
* target 변수가 비어있는지 확인하는 **예외 처리 구문**

**예외 처리 구문**이 왜 필요한데?

만약 저 구문을 넣지 않는다면 target 변수가 할당되지 않았거나, 게임 도중에 파괴되었을 때
*NullReferenceException* 오류가 발생하면서 멈추는 걸 방지

### ``` transform.position = Vector3.Lerp(transform.position, targetPosition, Time.deltaTime * followSpeed);```

**Lerp(A, B, t)** 가 뭔데? (복습)
* A에서 B까지 가는데 텔포해서 가지말고 t만큼 끊어서 이동하라는 뜻

우리가 정한 A,B,t는?
* **A**(출발점): *transform.position* 지금 현재 이 프레임에서의 **카메라의 위치**
* **B**(도착점): *targetPosition* 위애서 ```Vector3 targetPosition = target.position+offset;``` 로 계산했던 **카메라의 목적지**
* **t**(이동할 비율): *Time.deltaTime * followSpeed* 이번 프레임에서 **얼마나 가깝게 접근할지 정하는 비율**

**Lerp(A,B,t)** 를 쓰면서 생기는 문제?
* **t**의 값이 **0**이다? => **A**는 출발도 안함
* **t**의 값이 **1.0**이다? => **B**까지 순간이동 함
* **t**의 값이 **0.1**이다? => **A**에서 **B**까지 **10%** 만큼 움직임