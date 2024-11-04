# Mon setup taskwarrior (par Hezirel)

## Associer des notes a une tâche

- Un script bash

```bash
if [ $# -eq 1 ]; then
    task_id=$1
    uuid=$(task _get $task_id.uuid)
    notes_file=~/dev/notes/tasknotes/$uuid.md
    if [ -f $TASKN/$uuid.md ]; then
        nvim $TASKN/$uuid.md
    else
        desc=$(task $task_id info | awk '/^Description/ {print substr($0, index($0,$2))}')
        annotation="Notes"
        task $task_id annotate -- $annotation
        {
            echo "p:$(task _get $task_id.project)"
            echo "d:$(task _get $task_id.description)"
            echo "#uuid:$uuid"
            echo "---"
            echo ""
        } >> $notes_file
        nvim $notes_file
    fi
fi
```
Insertion de meta data format custom en header du fichier à l'aide des helper commands pour taskwarrior
Facilement personalisable avec du FZF et du scripting pour avoir un picker multi notes éventuellement.
J'avais pensé à en faire un mais j'en ai jamais eu besoin au final

Le setup dans la config .taskrc

```
uda.taskwarrior-tui.shortcuts.8=$HOME/.config/.task/taskopen/scripts/addnote
```

## Les contextes

Les reports sont super bien en cli, mais sur tw-tui les contextes sonts rois

Voici une comparaison de syntaxe

```
	## Report: Path ##
report.path.labels=ID,Project,scheduled.countdown,Due,until.remaining,Mission
report.path.sort=start+,id+,due+,priority-,totalactivetime-
report.path.filter=(+BLOCKING -BLOCKED) or (project:dojo -COMPLETED -wanderlust -reading -BLOCKED -BLOCKING)
report.path.project=indented
report.path.columns=id,project,scheduled.countdown,due.relative,until.remaining,description.count
report.path.description=Main Quest - Storyline blocking tasks

```

```
context.next.read=+NEXT or +ACTIVE
context.next.write=+NEXT
context.work.read=+work or +DUE
context.work.write=+work
context.AI.read=+llm or +ai or +ml or project:dojo.AI
context.AI.write=+ai
context.hX.read=project:dojo.hX.cs50 or +hX
context.hX.write=project:dojo.hX.cs50 or +hX
context.dojo.read=(project:dojo or +wanderlust) and -NEXT or +ACTIVE
context.dojo.write=+wanderlust +dojo
context.study.read=project:study or +study
context.study.write=project:study or +study
context.stash.read=+stash or project:workflow and -vim
context.stash.write=+stash
context.reading.read=+reading or project:reading
context.stash.write=+reading
context.vim.read=+vim or project:workflow.vim or project:workflow.nvim
context.vim.write=+vim
```

![Contexte](./ctx.png)
![Couleurs](./colors.png)

## Le time management

- un UDA

```
## Task active time ##
uda.totalactivetime.type=duration
uda.totalactivetime.label=Total active time
uda.totalactivetime.values=

```
Ÿ
Un hook timewarrior pour avoir des jolis rapports

![timew](./timew.png)

### Le timer

Pour mesurer facilement des durées de tâchs j'ai plusieurs alias de ce type

```fish
abbr -a wrkt "timew track workout;termdown -T WRKT;timew stop"
```

- [termdown](https://github.com/trehn/termdown)

![Termdown](./td.png)

Faut que je recode ce timer moi même pour le beau jeu
