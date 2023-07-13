# git submodule
init add submodule in your current project
```
cd SOMEWHREE_IN_MAIN_FOLDER
git submodule add SUBMODLE_REPO_LINK
cd MAIN_FOLDER
git add -u && git commit -m 'Add sub module'
```

```
cd MAIN_FOLDER
git submodule update --remote
git add -u && git commit -m 'update sub module and auto checkout'
```