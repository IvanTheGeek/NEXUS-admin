# New NEXUS Project Checklist

Replace `{{PROJECT-NAME}}` with the actual project name throughout.

---

## 1. Create the directory and git repo

```bash
mkdir /home/ivan/nexus/{{PROJECT-NAME}}
cd /home/ivan/nexus/{{PROJECT-NAME}}
git init
git branch -m main
```

## 2. Copy template files

```bash
cp -r /home/ivan/nexus/NEXUS-admin/templates/new-project/. /home/ivan/nexus/{{PROJECT-NAME}}/
```

Update placeholders in the copied files:
- `CLAUDE.md` — replace `{{PROJECT-NAME}}` and fill in the purpose
- `.claude/settings.json` — replace `{{PROJECT-NAME}}` in the hook command

## 3. Create initial commit

```bash
cd /home/ivan/nexus/{{PROJECT-NAME}}
git add -A
git commit -m "init: scaffold {{PROJECT-NAME}}"
```

## 4. Create GitHub repo and push

```bash
gh repo create {{PROJECT-NAME}} --public --description "{{description}}"
git remote add origin git@github.com:IvanTheGeek/{{PROJECT-NAME}}.git
git push -u origin main
```

## 5. Create graveyard branch

```bash
git checkout -b graveyard
git push -u origin graveyard
git checkout main
```

## 6. Add to NEXUS-Ecosystem .gitignore

In `/home/ivan/nexus/.gitignore`, add a line:
```
{{PROJECT-NAME}}/
```

Then commit and push NEXUS-Ecosystem:
```bash
cd /home/ivan/nexus
git add .gitignore
git commit -m "chore: exclude {{PROJECT-NAME}} from workspace repo"
git push origin main
```

## 7. Set up F# solution (if applicable)

```bash
cd /home/ivan/nexus/{{PROJECT-NAME}}
dotnet new gitignore
dotnet new sln -n {{PROJECT-NAME}}
dotnet new classlib -lang F# -n {{PROJECT-NAME}} -o src/{{PROJECT-NAME}}
dotnet sln add src/{{PROJECT-NAME}}/{{PROJECT-NAME}}.fsproj
```

Adjust as needed: `console` vs `classlib`, test project, etc.

## 8. Set up LOGOS (if this is an app project)

```bash
mkdir /home/ivan/nexus/{{PROJECT-NAME}}/logos
cp /home/ivan/nexus/NEXUS-admin/templates/logos-area.md \
   /home/ivan/nexus/{{PROJECT-NAME}}/logos/{{FIRST-AREA}}.md
```

Fill in the area name, workflows, flows, and GWT example rows.
Set `status: finalized` when the area is ready to feed FORGE.

## 9. Commit and push

```bash
cd /home/ivan/nexus/{{PROJECT-NAME}}
git add -A
git commit -m "init: scaffold {{PROJECT-NAME}} project"
git push
```
