# Load DB
```
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/tsherburne/neo4j/master/ArduPilot_Files.csv" as row 
CREATE (n:CodeFile) SET n = row

CREATE INDEX ON :CodeFile(File)

USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/tsherburne/neo4j/master/ArduPilot_FileDependencies.csv" AS row 
MATCH (fromFile:CodeFile {File: row.FromFile})
MATCH (toFile:CodeFile {File: row.ToFile})
MERGE (fromFile)-[fd:FileDependency]->(toFile)
ON CREATE SET fd.ReferenceCount = toInteger(row.References), 
fd.FromEntities = toInteger(row.FromEntities),
fd.ToEntities = toInteger(row.ToEntities);
```
# Clear DB
```
MATCH (n)
DETACH DELETE n
```
# Sample Queries
```
MATCH p = (f:CodeFile {File: "ArduCopter/mode_circle.cpp"})-[*1..1]->(df:CodeFile)
RETURN p
```
