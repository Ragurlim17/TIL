# roadSpawn
```
using Unity.VisualScripting;
using System.Collections.Generic;
using UnityEngine;

public class roadS : MonoBehaviour
{
    public GameObject road;
    public GameObject obstaclePrefab;
    public Transform playerTransform;

    public float roadLength = 200.0f;
    public int numberOfRoad = 5;

    private float spawnZ = 0.0f;
    private List<GameObject> activeRoads = new List<GameObject>();
    private float[] lanePositions = new float[] {-3.0f, 0.0f, 3.0f};
    void Start()
    {
        for(int i = 0; i<numberOfRoad; i++)
        {
            if(i<2) SpawnRoad(false);
            else SpawnRoad(true);
        }
    }

    void Update()
    {
        if(playerTransform.position.z-roadLength > spawnZ - (numberOfRoad * roadLength))
        {
            SpawnRoad(true);
            DeleteRoad();
        }
    }

    void SpawnRoad(bool spawnObstacle = true)
    {
        GameObject go = Instantiate(road, transform.forward*spawnZ, Quaternion.identity);
        activeRoads.Add(go);

        if(spawnObstacle&&obstaclePrefab != null)
        {
            SpawnObstacleOnRoad(spawnZ);
        }

        spawnZ += roadLength;
    }

    void SpawnObstacleOnRoad(float roadZ)
    {
        int obstacleCount = Random.Range(1, 8);

        for(int i =0; i<obstacleCount; i++)
        {
            float randomX = lanePositions[Random.Range(0, lanePositions.Length)];
            float randomZ = roadZ + Random.Range(-roadLength / 8f, roadLength / 8f);

            Vector3 obstaclePos = new Vector3(randomX, 0.5f, randomZ);

            GameObject obs = Instantiate(obstaclePrefab, obstaclePos, Quaternion.identity);
            
            obs.transform.SetParent(activeRoads[activeRoads.Count-1].transform);
        }
    }

    void DeleteRoad()
    {
        Destroy(activeRoads[0]);
        activeRoads.RemoveAt(0);
    }
}
```
# 기본 설정
**road & obstaclePrefab**
* **프리맵(Prefab)**: 유니티 에디터에서 미리 만들어둔 도로 조각과 장애물의 *'원본 틀'*
