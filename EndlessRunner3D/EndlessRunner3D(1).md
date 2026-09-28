[EndlessRunner3D(1).md](https://github.com/user-attachments/files/32736907/EndlessRunner3D.1.md)
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
