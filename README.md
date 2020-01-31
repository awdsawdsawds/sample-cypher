# Cypher

```
// สร้าง graph
MERGE (a1: Node1 { name: "A1" })
MERGE (b1: Node1 { name: "B1" })
MERGE (c1: Node1 { name: "C1" })
MERGE (d1: Node1 { name: "D1" })
MERGE (f1: Node1 { name: "F1" })

MERGE (a1)-[:Link1 { distance: 2 }]-(b1)
MERGE (a1)-[:Link1 { distance: 1 }]-(c1)
MERGE (b1)-[:Link1 { distance: 4 }]-(f1)
MERGE (c1)-[:Link1 { distance: 1 }]-(d1)
MERGE (d1)-[:Link1 { distance: 3 }]-(f1);
```

## Shortes path

https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/shortest-path/

### แบบสนใจ Weight

```
// หา Shortes path from A1->F1
MATCH (start:Node1{name:"A1"}), (end:Node1{name:"F1"})

CALL algo.shortestPath.stream(start, end, "distance")

YIELD nodeId, cost

MATCH (other:Node1) WHERE id(other) = nodeId

RETURN other.name AS name, cost;
```

### แบบไม่สนใจ Weight

```
// หา Shortes path from A1->F1
MATCH (start:Node1{name:"A1"}), (end:Node1{name:"F1"})

// ระบุ Weight Attribute ในที่นี้คือ distance
CALL algo.shortestPath.stream(start, end, "distance")

YIELD nodeId, cost

MATCH (other:Node1) WHERE id(other) = nodeId

RETURN other.name AS name, cost;
```

## MST

https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/minimum-weight-spanning-tree/

```
// สร้าง relation สำหรับ MST โดยใช้ชื่อว่า MINST
// return performance ของการทำงาน
MATCH (n:Node1 {name:"A1"})

CALL algo.spanningTree.minimum("Node1", "Link1", "distance", id(n), {write:true, writeProperty:"MINST"})

YIELD loadMillis, computeMillis, writeMillis, effectiveNodeCount

RETURN loadMillis, computeMillis, writeMillis, effectiveNodeCount;
```

```
// แสดง MST จาก relation ที่ชื่อ MINST
MATCH path = (n:Node1 {name:"A1"})-[:MINST*]-()

WITH relationships(path) AS rels

UNWIND rels AS rel

WITH DISTINCT rel AS rel

RETURN startNode(rel).name AS source, endNode(rel).name AS destination, rel.distance AS cost;
```
