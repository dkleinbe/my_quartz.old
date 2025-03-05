---
created: 18.02.2025 - 16:01
modified: 18.02.2025 - 16:01
---

# Tasks

## toutes les taches prioritaires

```otasks title="toutes les taches prioritaires" frame="none"
# only not completed tasks
not done 
# define root folder
root includes Reno Lafontaine
# do not scan files with #no-task tag
filter by function !task.file.property('tags').includes('#no-task')
(priority is above none) OR (description includes ⏫) OR (description includes 🔼)
sort by path
group by function task.file.folder
group by function task.file.filename
group by function task.heading
show tree
```



```bash title="JAVA" frame="terminal"
# comment
if (i == 0) then 
	printf("");
/* sdfdsf */
```


