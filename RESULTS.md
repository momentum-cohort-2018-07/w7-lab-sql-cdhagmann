# SQL Queries!

## Description

For each of the questions below, add the following information to a Markdown file in your homework repo:

- The original question text
- Your final SQL query (which you must have run and validated on the included database)
- The number of results returned (if more than one)
- The specific result returned (if a single record is returned)

## The queries

- Find all time entries.

```SQL
SELECT * 
FROM   time_entries;
```

- Find the developer who joined most recently.
  - Dr. Danielle McLaughlin

```SQL
SELECT * 
FROM   developers 
ORDER  BY joined_on DESC 
LIMIT  1;
```

- Find the number of projects for each client.

```SQL
SELECT clients.id,
       clients.name,
       COUNT(*) 
FROM   projects 
       INNER JOIN clients
               ON projects.client_id = clients.id
GROUP  BY client_id;
```

- Find all time entries, and show each one's client name next to it.

```SQL
SELECT clients.name, 
       time_entries.*
FROM   time_entries
       INNER JOIN projects
               ON time_entries.project_id = projects.id
       INNER JOIN clients
               ON projects.client_id = clients.id
```

- Find all developers in the "Ohio sheep" group.

```SQL
SELECT  developers.*
FROM    developers
        INNER JOIN group_assignments
                ON developers.id = group_assignments.developer_id
                   AND group_assignments.group_id = 3; -- Ohio sheep's group ID 
```

- Find the total number of hours worked for each client.

```SQL
SELECT clients.name, 
       SUM(time_entries.duration)
FROM   time_entries
       INNER JOIN projects
               ON time_entries.project_id = projects.id
       INNER JOIN clients
               ON projects.client_id = clients.id
GROUP  BY clients.id 
```

- Find the client for whom Mrs. Lupe Schowalter (the developer) has worked the greatest number of hours.
  - Kuhic-Bartoletti

```SQL
SELECT clients.*
FROM   time_entries
       INNER JOIN projects
               ON time_entries.project_id = projects.id
                  AND time_entries.developer_id = 28 -- Mrs. Lup Schowatler's ID
       INNER JOIN clients
               ON projects.client_id = clients.id
GROUP  BY clients.id
ORDER  BY SUM(time_entries.duration) desc
LIMIT  1;
```

- List all client names with their project names (multiple rows for one client is fine).  Make sure that clients still show up even if they have no projects.

```SQL
SELECT clients.name, 
       projects.name
FROM   clients
       LEFT JOIN projects
              ON clients.id = projects.client_id
```

- Find all developers who have written no comments.

```SQL
SELECT *
FROM   developers
       LEFT JOIN comments
              ON comments.developer_id = developers.id
where  comments.id IS NULL;		
```

Unless otherwise specified, return all columns in the requested table (e.g. developers).

## Challenges

Try these queries if you have time:

- Find all developers with at least five comments.

```SQL
SELECT developers.*
FROM   developers
       INNER JOIN comments
               ON comments.developer_id = developers.id
GROUP  by developers.id
HAVING count(*)  >= 5
```

- Find the developer who worked the fewest hours in January of 2015.
  - Ms. Tremayne Kuhn

```SQL
SELECT developers.name,
       SUM(time_entries.duration) as hours_worked
FROM   developers
       INNER JOIN time_entries
                 ON time_entries.developer_id = developers.id
WHERE  time_entries.worked_on BETWEEN '2015-01-01' AND '2015-01-31'
GROUP  BY developers.id
ORDER  BY SUM(time_entries.duration)
LIMIT  1;
```

- Find all time entries which were created by developers who were not assigned to that time entry's project.

```SQL
SELECT time_entries.*
FROM   time_entries
       LEFT JOIN project_assignments
                 ON time_entries.developer_id = project_assignments.developer_id
                    AND time_entries.project_id = project_assignments.project_id
WHERE  project_assignments.id IS NULL
```

- Find all developers with no time put towards at least one of their assigned projects.

```SQL
SELECT *
FROM   developers
WHERE  developers.id NOT IN (SELECT DISTINCT time_entries.developer_id
                             FROM   time_entries
                                    LEFT JOIN project_assignments
                                           ON time_entries.developer_id = project_assignments.developer_id
				                                      AND time_entries.project_id = project_assignments.project_id
                             WHERE  project_assignments.id IS  NOT NULL)
```

- Find all pairs of developers who are in two or more different groups together. 
  - 41 AND 44

```SQL
SELECT GROUP_A.developer_ID, GROUP_B.developer_ID
FROM   group_assignments GROUP_A
       INNER JOIN group_assignments GROUP_B
	             ON GROUP_A.GROUP_ID = GROUP_B.group_ID
		              AND GROUP_A.developer_ID < GROUP_B.developer_ID
GROUP  BY GROUP_A.developer_ID, GROUP_B.developer_ID
HAVING COUNT(*) >= 2
```

### Resources

- [DB Browser for SQLite](https://sqlitebrowser.org/)
- [Markdown](https://guides.github.com/features/mastering-markdown/)