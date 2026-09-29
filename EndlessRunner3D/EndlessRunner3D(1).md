[EndlessRunner3D(1).md](https://github.com/user-attachments/files/32800061/EndlessRunner3D.1.md)
# PlayerMovement
```
using UnityEngine;

public class playerMovement : MonoBehaviour
{
    [Header("Player Setting")]
    public float fowardSpeed = 15.0f;
    public float laneDistance = 3.0f;
    public float laneChangeSpeed = 10.0f;

    private int currentLane = 1;
    private Vector3 targetPosition;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.LeftArrow) || Input.GetKeyDown(KeyCode.A))
        {
            ChangeLane(-1);
        }
        else if (Input.GetKeyDown(KeyCode.RightArrow) || Input.GetKeyDown(KeyCode.D))
        {
            ChangeLane(1);
        }

        float targetX = (currentLane-1)*laneDistance;
        targetPosition = new Vector3(targetX, transform.position.y, transform.position.z);

        Vector3 newPosition = Vector3.Lerp(transform.position, targetPosition, Time.deltaTime*laneChangeSpeed);

        newPosition.z += fowardSpeed*Time.deltaTime;

        transform.position = newPosition;
    }

    void ChangeLane(int direction)
    {
        currentLane = Mathf.Clamp(currentLane+direction, 0, 2);
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Obstacle"))
        {
            Debug.Log("Crush Obstacle!");
            fowardSpeed = 0f;
        }
    }
}
```
# void Update()
### ``` Input.GetKeyDown(KeyCode.____);```

**Input** -> 입력 받겠다. / **GetKeyDown** -> 입력(눌려진) 키를 / **KeyCode.__** -> 키가 __ 인 걸


여기서 **ChangeLane**은 메서드, **1**이 오른쪽, **-1**이 왼쪽

---
### ``` float targetX = (currentLane - 1)*laneDistance;```

**currentLane**의 값에 따라 변하는 것

- 0 (왼쪽 라인) / 1 (중앙 라인) / -1 (오른쪽 라인)

**laneDistance**는?

- 라인 사이의 간격 (지금은 설정값이 3.0f)

**TargetX**의 값은 어떻게 계산할까?
> *(**current의 값** - 1 ) * **laneDistance(=3.0f)**;*

ex) 중앙 라인 (current==1): (1-1) × 3.0f = 0 --> X축 좌표

핵심은
**(currentLane - 1) 공식을 통해 중앙(1)을 기준점 0으로 만들고, 좌우를 음수/양수 위치 좌표로 변환**하는 것

---
### ```targetPosition = new Vector3(targetX, transform.position.y, transform.position.z); ```

**TargetX** = 위에서 계산한 물체의 **x**좌표

**transform.position.y /z** = 현재 플레이어의 **높이(y)**, **진행 거리(z)**는 그대로 유지

> *targetPosition은 변수명, targetX(첫번째)는 전에 변수값 불러오기, transform.position.y/z(2,3번째)는 고정(유지) 값*

---
### ```Vector3 newPosition = Vector3.Lerp(transform.position, targetPosition, Time.deltaTime*laneChangeSpeed);```

**Vector3.Lerp(A, B, t)** 란?
* **A** (시작점): 출발 위치
* **B** (목표점): 도달할 위치
* **t** (비율): 0~1 사이의 값 (0=A 지점, 1=B 지점, 0.5= A,B의 딱 중간)

> *만약 **A = 0 , B = 10일 때, t = 0.3f 라면, 0과 10사이의 30% 지점인 3이 반환***

여기서 **t**값은 *Time.deltaTime×laneChangeSpeed*

**Time.deltaTime**이란?
* 이전 프레임에서 현재 프레임까지 걸린 시간 **(프레임의 독립적 이동을 위해 사용)**

**LaneChangeSpeed**는 10.0f

**[작동방식]**

**currentLane**이 바뀌면 **targetPosition**이 즉시 이동

**Lerp**는 바로 그 위치로 순간이동 시키지 않고, 현재 위치인 **transform.position**에서 **targetPosition**으로 매 프레임(60fps)씩 조금씩 따라붙게 만들어
**부드럽게 이동하는 것처럼** 보이게 됨

---
### ```  newPosition.z += fowardSpeed*Time.deltaTime; ```

**forwardSpeed** : 플레이어가 앞으로 나아가는 속도 (현재 설정값 15.0f)

**Time.deltaTime** : 각 PC의 성능이 달라도 초당 동일한 거리를 이동하게 해줌

이 코드는 위의 코드의 라인 변경이 끝난 **newPosition**위치에 앞으로 가는 거리를 더해줌

이렇게 하면
> ***좌우로 이동하는 와중에 멈추지 않고 앞으로 계속 달리는 움직임이 완성***

---
### ``` transform.position = newPosition;```
계산이 완료된 **newPosition(좌우 이동 + 전진 이동이 모두 반영된 위치)**을
플레이어(오브젝트)의 **실제 위치(transform.position)**에 할당

---
메서드인 **void ChangeLane**에 대해 알아보자
# Void ChangeLane(int direction)

### ```currentLane = Mathf.Clamp(currentLane+direction, 0, 2);```
**currentLane**: 플레이어 오브젝트가 가지는 줄 번호 (0, 1, 2)

**int direction**: 매개 변수이고, Update() 내에서 받는 ChangeLane(int direction)에서 받아옴 (-1, 1 같은 값)

**currentLane + direction**: 말 그대로 '줄 번호(인덱스 값)에 아끼 ChangeLane으로 받았던 값(-1, 1) 더하기'

**Mathf.Clamp(value, min, max)** 가 뭔데?
* 특정 값(value)가 지정한 최소값(min)과 최대값(max) 범위를 벗어나지 않도록 강제로 가뒤두는 함수 *(= 값을 제한하는 유니티 수학 함수)*
* 여기서의 최소값은 **0**(가장 왼쪽 라인), 최대값은 **1**(가장 오른쪽 라인)

**Mathf. 의 다양한 활용**
* **Mathf.Clamp(values, min, max)** : 최소 ~ 최대 범위로 값을 계산
* **Mathf.Lerp(a, b, t)** : 두 숫자(a,b) 사이를 부드럽게 연결 (전에 사용했던 *Vector3.Lerp* 와 원리 같음!!)
* **Mathf.Max(a,b)** : 두 숫자들(a,b)중 더 큰 값을 반환
* **Mathf.Min(a,b)** : 두 숫자들(a,b)중 더 작은 값을 반환
* **Mathf.Abs(value)** : 절댓값 구하기 *(-5 -> 5)*
* **Mathf.Floor(value)** : 소수점 버림 *(3.4f -> 3.0f)*
* **Mathf.Ceil(value)** : 소수점 올림 *(3.4f -> 4.0f)*
* **Mathf.Round(value)** : 반올림 *(3.1f -> 3.0f / 3.5f -> 4.0f)*

**Mathf. 게임 제작용 특수 기능**
* **Mathf.Repeat(t, length)** : 값을 0 ~ length 범위에서 *무한 반복*
* **Mathf. PingPong(t, length)** : 값이 0 ~ length 사이에서 *왔다갔다* 하게 만듦
* **Mathf.Sqrt(value)** : 제곱근(루트) 계산
* **Mathf.Sin , Mathf.Cos** : 삼각함수

---
### ```private void OnTriggerEnter(Collider other)```

**OnTriggerEnter**가 뭔데?
* 유니티 내의 특수 이벤트 메서드
* 개발자가 직접 호출 X, *물체끼리 **'스치거나 겹치는 순간'** 유니티가 알아서 **1회** 자동으로 실행*

어떻게 쓸까?
* 상호작용하게 되는 물체 중 한 개 이상은 **Is Trigger**에 체크를 해야 함
* 상호작용하게 되는 물체 전부 **Collider**을 가지고 있어야 함
* 상호작용하게 되는 물체 중 한 개 이상은 **RigidBody**를 가지고 있어야 함

이렇게 하면 **Is Trigger**가 켜져있는 오브젝트는 다른 오브젝트와 *물리적인* (부딪치기 이런거) 작용이 불가해지고, 단지 자신의 오브젝트 범위에 다른 물체가 들어왔는지만 체크하는 용도로 변하게 됨.

진짜 그저 **유령 블록** 느낌?

**Collider other**이 뭔데?
* 매개 변수(Parameter)
* *Collider* : 유니티 내에서 물체의 *형체(충돌 범위)* 를 나타내는 *컴포넌트 타입*
* *other* : 내 오브젝트(캐릭터, 플레이어)와 *방금 부딪힌 **상대 물체의 Collider** 정보가 들어오는 곳*

---
### ```if (other.CompareTag("Obstacle")) {Debug.Log("Crush Obstacle!"); fowardSpeed = 0f;}```
**other.CompareTag(*Tag_name*)** : other에서 받은 태그 이름이 *Tag_name* 과 같은지 확인 (True, False)

**Debug.Log(*Text*)** : 개발자 콘솔창에 *Text* 표시

여기서 **fowardSpeed = 0f;** 가 뭐하는데?

위에 있는 **Update()** 문에 있었던

**newPosition.z += fowardSpeed * Time.deltaTime**을 멈추게 만듦 *(0f)*
