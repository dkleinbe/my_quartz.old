---
created: 18.02.2025 - 16:01
modified: 18.02.2025 - 16:01
---
## keywords

starts|due

before|on|after|next|in

or

yesterday|today|tomorrow

# Tasks
No cache
## toutes les taches prioritaires

```otasks
not done 
root includes Reno Lafontaine
filter by function !task.file.property('tags').includes('#no-task')
(priority is above none) OR (description includes ⏫) OR (description includes 🔼)
sort by path
group by function task.file.folder
group by function task.file.filename
group by function task.heading
show tree
<script>
print("coucou");
</script>
```



```otasks showLinenumbers=false
filter by function task.file.property('tags')
```




```markdown
<script>
task.file.property('tags').includes('#no-task');
</script>
if (i == 0) then 
	printf("");
/* sdfdsf */
```


