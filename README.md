# Cypher

```
// สร้าง graph
MERGE (a1: Node1 { name: "A1" })
MERGE (b1: Node1 { name: "B1" })
MERGE (c1: Node1 { name: "C1" })
MERGE (d1: Node1 { name: "D1" })
MERGE (f1: Node1 { name: "F1" })

MERGE (a1)-[:Link1 { distance: 2 }]->(b1)
MERGE (a1)-[:Link1 { distance: 1 }]->(c1)
MERGE (b1)-[:Link1 { distance: 4 }]->(f1)
MERGE (c1)-[:Link1 { distance: 1 }]->(d1)
MERGE (d1)-[:Link1 { distance: 3 }]->(f1);
```

## Shortes path

https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/shortest-path/

### แบบสนใจ Weight

```
// หา Shortes path from A1->F1
MATCH (start:Node1{name:"A1"}), (end:Node1{name:"F1"})

// ระบุ Weight Attribute ในที่นี้คือ distance
CALL algo.shortestPath.stream(start, end, "distance")

YIELD nodeId, cost

MATCH (other:Node1) WHERE id(other) = nodeId

RETURN other.name AS name, cost;
```

### แบบไม่สนใจ Weight (ทุก relation มี Weight เป็น 1)

```
// หา Shortes path from A1->F1
MATCH (start:Node1{name:"A1"}), (end:Node1{name:"F1"})

// ไม่ต้องระบุ Weight Attribute
CALL algo.shortestPath.stream(start, end)

YIELD nodeId, cost

MATCH (other:Node1) WHERE id(other) = nodeId

RETURN other.name AS name, cost;
```

## MST

https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/minimum-weight-spanning-tree/

```
// สร้าง relation สำหรับ MST โดยใช้ชื่อว่า MINST
// และ return performance ของการทำงาน
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

## Degree centrality

https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/degree-centrality/



```
CALL algo.degree.stream("User", "FOLLOWS", {direction: "incoming"})

YIELD nodeId, score

RETURN algo.asNode(nodeId).id AS name, score AS weightedFollowers

ORDER BY followers DESC
```

โดยสามารถเลือก direction ได้ 3 แบบ

- `'incoming'`: สนใจขาเข้า (เป็น `default` option)

- `'outgoing'`: สนใจขาออก

- `'both'`: ไม่สนทิศทาง (undirected graph)


## Betweeness centrality

https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/betweenness-centrality/

```
CALL algo.betweenness.stream("Node1", "Link1", {direction:"OUT"})

YIELD nodeId, centrality

MATCH (n:Node1) WHERE id(n) = nodeId

RETURN n.name AS n, centrality

ORDER BY centrality DESC;
```

โดยสามารถเลือก direction ได้ 3 แบบ

- `'INCOMING'`,`'IN'`,`'I'`, `'<'`: สนใจขาเข้า (เป็น incoming option)

- `'OUTGOING'`,`'OUT'`,`'O'`, `'>'`: สนใจขาออก (เป็น `default` option)

- `'both'`: ไม่สนทิศทาง (undirected graph)


ดูเพิ่มเติมที่ https://neo4j.com/docs/graph-algorithms/current/labs-algorithms/betweenness-centrality/#labs-algorithms-betweenness-centrality-support
