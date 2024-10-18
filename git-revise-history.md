有時候，我們修正一系統檔案，例如某個commit中，多了一個不該放的檔案，又或者想修改該commit的作者，我們就要追搜到某個commit，然後用rebase隨個改。

例如本次repo，有一個githubAction.md，因為錯誤原因，被加到了main中，也藏了很久。如果我們想連根拔起，我們需要加出它第一次出現的commit。

```
$ git log githubAction.md
commit 60ccd70f6b768138cbe23c93ffcfa32574ce895c
```

那我們就以它前一個commit作為rebase的根據，進行逐個commit修正。

```
$ git rebase -i 60ccd70f6b768138cbe23c93ffcfa32574ce895c^
pick 60ccd70 draft some content
pick e2ee9a3 add some senario.
pick b91afc1 refine submodule;
pick 98cd366 add notes about submodule specific checkout;
pick 064b06f test directly commit in submodule main
pick 7b648d2 update git submodules notes
pick 556f25e add notes about merge timing
pick 5244804 Create git-continuous-integration-strategy.md
pick 107e486 add more pratical nodes about ci;
pick d93cbee add mono repo challenge
pick 1c471b6 add worktree notes
pick 9063ccb notes about different of git flow and github flow;
pick b72e89e Update github-flow.md, add ref more link
pick 0b8f2a9 draft github flow release problem
pick 8b333fc finalize github flow release strategy
```

在rabase選項中，把需要改的commit由pick改為edit。(rebase會以舊到新顯示)。然後儲存。例如
```
edit 60ccd70 draft some content
edit e2ee9a3 add some senario.
edit b91afc1 refine submodule;
pick 98cd366 add notes about submodule specific checkout;
pick 064b06f test directly commit in submodule main
pick 7b648d2 update git submodules notes
pick 556f25e add notes about merge timing
pick 5244804 Create git-continuous-integration-strategy.md
pick 107e486 add more pratical nodes about ci;
pick d93cbee add mono repo challenge
pick 1c471b6 add worktree notes
pick 9063ccb notes about different of git flow and github flow;
pick b72e89e Update github-flow.md, add ref more link
pick 0b8f2a9 draft github flow release problem
pick 8b333fc finalize github flow release strategy
```

我們第一次會在60ccd70，我們作出想要的改動，然後經amend去改掉60ccd70
```
$ rm githubAction.md
$ git add -u
$ git commit --amend --author="newuser <newemail>"
```

確定無誤的話，就可以去下一步，即是到了e2ee9a3
```
$ git rebase --continue
```

因為已經rebase過，你此時看到的不會再是hash不再是e2ee9a3，而是自動rebase完的e2ee9a3。若大家有東西要改，就使用commit --amend。如果沒有東西要改，也沒有conflict，可以繼續rebase --continue下去。