# 1. get all statuses, not repeating, alphabetically ordered

```sql
SELECT DISTINCT status FROM tasks
ORDER BY status ASC
```

# 2. get the count of all tasks in each project, order by tasks count descending

```sql
SELECT project_id, COUNT(*) FROM tasks
GROUP BY project_id
ORDER BY count DESC;
```

# 3. get the count of all tasks in each project, order by projects names

```sql
SELECT *
FROM projects
LEFT JOIN (
	SELECT project_id, count(*) as task_count
	FROM tasks
	GROUP BY project_id
) grouped_tasks ON (projects.id = grouped_tasks.project_id)
ORDER BY name ASC;
```

# 4. get the tasks for all projects having the name beginning with "N" letter

```sql
SELECT tasks.*
FROM tasks
INNER JOIN projects on projects.id = tasks.project_id  
WHERE projects.name LIKE 'n%'
```

# 5. get the list of all projects containing the 'a' letter in the middle of 
the name, and show the tasks count near each project. Mention that there can exist projects without tasks and tasks with 
project_id = NULL

```sql
SELECT projects.*, COALESCE(task_count, 0)  as task_count
FROM projects
LEFT JOIN (
	SELECT project_id, count(*) as task_count
	FROM tasks
	GROUP BY project_id
) grouped_tasks ON (projects.id = grouped_tasks.project_id)
WHERE name LIKE '%a%'
```

# 6. get the list of tasks with duplicate names. Order alphabetically

```sql
SELECT *
FROM tasks
WHERE name IN (
	SELECT name
	FROM tasks
	GROUP BY name
	HAVING count(*) > 1
)
ORDER BY name ASC;
```

# 7. get list of tasks having several exact matches of both name and status, from the project 'Garage'. Order by matches count

```sql
SELECT tasks.*, match_count
FROM tasks
INNER JOIN (
	SELECT project_id, name, status, COUNT(*) as match_count
	FROM tasks
 	WHERE project_id = (SELECT id FROM projects WHERE name = 'Garage')
	GROUP BY project_id, name, status
	HAVING count(*) > 1
) grouped_tasks ON (tasks.project_id = grouped_tasks.project_id AND 
					tasks.name = grouped_tasks.name AND 
					tasks.status = grouped_tasks.status)
ORDER BY match_count DESC;
```

# 8. get the list of project names having more than 10 tasks in status 'completed'. Order by project_id 

```sql
SELECT name
FROM projects
WHERE ID IN (
	SELECT project_id
	FROM tasks
 	WHERE status = 'completed'
	GROUP BY project_id
	HAVING count(*) > 10
)
ORDER BY id ASC;
```
