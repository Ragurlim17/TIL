# PlayerMovement
```
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
```
## void Update()
```
if (Input.GetKeyDown(KeyCode.LeftArrow) || Input.GetKeyDown(KeyCode.A))
        {
            ChangeLane(-1);
        }
        else if (Input.GetKeyDown(KeyCode.RightArrow) || Input.GetKeyDown(KeyCode.D))
        {
            ChangeLane(1);
        }
```

만약 지금 입력 받은 키 입력이 **왼쪽 화살표** 이거나 **키보드에서 A키**라면 메서드인 **ChangeLane**에 *-1* 값을 넣는다.

만약에 지금 입력 받은 키 입력이 **오른쪽 화살표** 이거나 **키보드에서 D키**라면 메서드인 **ChangeLane**에 *1*값을 넣는다.
