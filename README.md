# Neo4j Graph Database

## Neo4j Setup

After installation, you'll need to create a new database within Neo4j:

1. Open the Neo4j Desktop application.
2. Click on `New` to create a new project. Give your project a meaningful name.
3. Inside your project, click on `Add` and then choose `Local DBMS`.
4. Assign a name and password to your new DBMS, then click on `Create`.
5. Once created, click `Start` to run the database.

Within the database management system, you'll notice two databases: `System` (which is not activated and is colored in gray) and your newly created one (default).

To interact with your database:

1. Go to the left side of the window and click on `Graph Apps`, which is an icon with four squares.
2. Then click on `Neo4j Browser`.
3. In the Neo4j Browser, you can start interacting with your database. For example, to return all nodes, type:

```cypher
MATCH (n)
RETURN n
```

## Overview

This repository contains the schema and example data for a graph database built using Neo4j. The database models a scenario involving trainees and tasks, capturing relationships based on skills, task requirements, and performance evaluations.

The graph database has two main categories of nodes:

- **Trainees**: Individuals who have attributes such as age, sex, grade acquired so far, a background vector, and a representative vector. Trainees are interconnected based on the cosine similarity of their representative vectors.
  
- **Tasks**: Defined activities with various levels of complexity, urgency, and skill requirements. Tasks are interconnected based on the cosine similarity of their representative vectors.

Each trainee is assigned to exactly one task, with details about their performance and preferences captured in the relationship properties.

## Schema

### Trainees

- **Attributes**:
  - `Name`: The name of the trainee (randomly chosen).
  - `Age`: The age of the trainee (randomly chosen).
  - `Sex`: The sex of the trainee.
  - `Grade Acquired So Far`: A numerical grade between 1 and 10.
  - `Background Vector`: A vector indicating the level of experience in bimanual tasks, performing delicate tasks, and agility.
  - `Representative Vector`: A randomly chosen vector of size 8, representing various skill levels or attributes.
- **Relationships**:
  - Interconnected with other trainees through a `SIMILARITY` relationship that includes the cosine similarity value between their representative vectors.

### Tasks

- **Attributes**:
  - `Name`: Specific name of the task (e.g., `task1_level_1`, `task1_level_2`, etc.).
  - `Representative Vector`: A vector with values (1-5) indicating the task level, difficulty level, time sensitivity, bimanual level, and precision level.
- **Relationships**:
  - Interconnected with other tasks through a `SIMILARITY` relationship that includes the cosine similarity value between their representative vectors.

### Trainee to Task Assignments

- **Relationship Label**: `TRIAL`
- **Properties**:
  - `Grade`: A value between 1 and 10 indicating the performance grade.
  - `Is_Passed`: Indicates whether the trainee passed the task (`0` for no, `1` for yes).
  - `Is_Liked`: Indicates whether the trainee liked the task (`0` for no, `1` for yes).



```cypher
// creating trainees
CREATE (t1:Trainee {name: "Alice", age: 24, sex: "Female", grade: 8, background_vector: [3, 5, 4], representative_vector: [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8]})
CREATE (t2:Trainee {name: "Bob", age: 29, sex: "Male", grade: 6, background_vector: [4, 2, 5], representative_vector: [0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2, 0.1]})
CREATE (t3:Trainee {name: "Charlie", age: 22, sex: "Male", grade: 9, background_vector: [5, 3, 2], representative_vector: [0.4, 0.4, 0.4, 0.4, 0.4, 0.4, 0.4, 0.4]})
```

```cypher
// creating tasks
CREATE (task1:Task {name: "task1_level_1", representative_vector: [1, 3, 5, 2, 4]})
CREATE (task2:Task {name: "task1_level_2", representative_vector: [2, 4, 1, 3, 5]})
CREATE (task3:Task {name: "task2_level_1", representative_vector: [5, 3, 1, 4, 2]})
CREATE (task4:Task {name: "task2_level_2", representative_vector: [4, 2, 5, 1, 3]})
```

```cypher
// creating cosine similarity relationships between trainees
// Between Trainee Alice and Bob
MATCH (t1:Trainee {name: "Alice"}), (t2:Trainee {name: "Bob"})
CREATE (t1)-[:SIMILARITY {cosine_similarity: 0.85}]->(t2)

// Between Trainee Alice and Charlie
MATCH (t1:Trainee {name: "Alice"}), (t2:Trainee {name: "Charlie"})
CREATE (t1)-[:SIMILARITY {cosine_similarity: 0.78}]->(t2)

// Between Trainee Bob and Charlie
MATCH (t1:Trainee {name: "Bob"}), (t2:Trainee {name: "Charlie"})
CREATE (t1)-[:SIMILARITY {cosine_similarity: 0.92}]->(t2)
```

```cypher
// creating cosine similarity relationships between tasks
// Between task1_level_1 and task1_level_2
MATCH (task1:Task {name: "task1_level_1"}), (task2:Task {name: "task1_level_2"})
CREATE (task1)-[:SIMILARITY {cosine_similarity: 0.88}]->(task2)

// Between task1_level_1 and task2_level_1
MATCH (task1:Task {name: "task1_level_1"}), (task3:Task {name: "task2_level_1"})
CREATE (task1)-[:SIMILARITY {cosine_similarity: 0.75}]->(task3)

// Between task1_level_1 and task2_level_2
MATCH (task1:Task {name: "task1_level_1"}), (task4:Task {name: "task2_level_2"})
CREATE (task1)-[:SIMILARITY {cosine_similarity: 0.67}]->(task4)

// Between task1_level_2 and task2_level_1
MATCH (task2:Task {name: "task1_level_2"}), (task3:Task {name: "task2_level_1"})
CREATE (task2)-[:SIMILARITY {cosine_similarity: 0.82}]->(task3)

// Between task1_level_2 and task2_level_2
MATCH (task2:Task {name: "task1_level_2"}), (task4:Task {name: "task2_level_2"})
CREATE (task2)-[:SIMILARITY {cosine_similarity: 0.90}]->(task4)

// Between task2_level_1 and task2_level_2
MATCH (task3:Task {name: "task2_level_1"}), (task4:Task {name: "task2_level_2"})
CREATE (task3)-[:SIMILARITY {cosine_similarity: 0.84}]->(task4)
```

```cypher
// the whole code:
CREATE (t1:Trainee {name: "Alice", age: 24, sex: "Female", grade: 8, background_vector: [3, 5, 4], representative_vector: [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8]});
CREATE (t2:Trainee {name: "Bob", age: 29, sex: "Male", grade: 6, background_vector: [4, 2, 5], representative_vector: [0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2, 0.1]});
CREATE (t3:Trainee {name: "Charlie", age: 22, sex: "Male", grade: 9, background_vector: [5, 3, 2], representative_vector: [0.4, 0.4, 0.4, 0.4, 0.4, 0.4, 0.4, 0.4]});

CREATE (task1:Task {name: "task1_level_1", representative_vector: [1, 3, 5, 2, 4]});
CREATE (task2:Task {name: "task1_level_2", representative_vector: [2, 4, 1, 3, 5]});
CREATE (task3:Task {name: "task2_level_1", representative_vector: [5, 3, 1, 4, 2]});
CREATE (task4:Task {name: "task2_level_2", representative_vector: [4, 2, 5, 1, 3]});

MATCH (t1:Trainee {name: "Alice"}), (t2:Trainee {name: "Bob"})
CREATE (t1)-[:SIMILARITY {cosine_similarity: 0.85}]->(t2);

MATCH (t1:Trainee {name: "Alice"}), (t2:Trainee {name: "Charlie"})
CREATE (t1)-[:SIMILARITY {cosine_similarity: 0.78}]->(t2);

MATCH (t1:Trainee {name: "Bob"}), (t2:Trainee {name: "Charlie"})
CREATE (t1)-[:SIMILARITY {cosine_similarity: 0.92}]->(t2);

MATCH (task1:Task {name: "task1_level_1"}), (task2:Task {name: "task1_level_2"})
CREATE (task1)-[:SIMILARITY {cosine_similarity: 0.88}]->(task2);

MATCH (task1:Task {name: "task1_level_1"}), (task3:Task {name: "task2_level_1"})
CREATE (task1)-[:SIMILARITY {cosine_similarity: 0.75}]->(task3);

MATCH (task1:Task {name: "task1_level_1"}), (task4:Task {name: "task2_level_2"})
CREATE (task1)-[:SIMILARITY {cosine_similarity: 0.67}]->(task4);

MATCH (task2:Task {name: "task1_level_2"}), (task3:Task {name: "task2_level_1"})
CREATE (task2)-[:SIMILARITY {cosine_similarity: 0.82}]->(task3);

MATCH (task2:Task {name: "task1_level_2"}), (task4:Task {name: "task2_level_2"})
CREATE (task2)-[:SIMILARITY {cosine_similarity: 0.90}]->(task4);

MATCH (task3:Task {name: "task2_level_1"}), (task4:Task {name: "task2_level_2"})
CREATE (task3)-[:SIMILARITY {cosine_similarity: 0.84}]->(task4);

MATCH (t:Trainee {name: "Alice"}), (task:Task {name: "task1_level_1"})
CREATE (t)-[:TRIAL {grade: 7, is_passed: 1, is_liked: 0}]->(task);

MATCH (t:Trainee {name: "Bob"}), (task:Task {name: "task2_level_1"})
CREATE (t)-[:TRIAL {grade: 5, is_passed: 0, is_liked: 1}]->(task);

MATCH (t:Trainee {name: "Charlie"}), (task:Task {name: "task1_level_2"})
CREATE (t)-[:TRIAL {grade: 8, is_passed: 1, is_liked: 1}]->(task);
```

To visualize things, please execute :

```cypher
MATCH (n)
RETURN n
```
