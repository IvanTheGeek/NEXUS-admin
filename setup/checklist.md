# New NEXUS Project Checklist

Replace `{{PROJECT-NAME}}` with the actual project name throughout.

---

## 1. Create the directory and git repo

```bash
mkdir /home/ivan/nexus/{{PROJECT-NAME}}
cd /home/ivan/nexus/{{PROJECT-NAME}}
git init
git checkout -b main
```

## 2. Create the graveyard branch

```bash
git commit --allow-empty -m "chore: init"
git checkout -b graveyard
git checkout main
```

## 3. Copy template files

```bash
cp -r /home/ivan/nexus/NEXUS-admin/templates/new-project/. /home/ivan/nexus/{{PROJECT-NAME}}/
```

Update placeholders in the copied files:
- `CLAUDE.md` — replace `{{PROJECT-NAME}}` and fill in the purpose
- `.claude/settings.json` — replace `{{PROJECT-NAME}}` in the hook command

## 4. Set up remote origin

```bash
cd /home/ivan/nexus/{{PROJECT-NAME}}
git remote add origin <remote-url>
git push -u origin main
git push origin graveyard
```

## 5. Create the F# solution (if applicable)

```bash
cd /home/ivan/nexus/{{PROJECT-NAME}}
dotnet new sln -n {{PROJECT-NAME}}
dotnet new console -lang F# -n {{PROJECT-NAME}} -o src/{{PROJECT-NAME}}
dotnet sln add src/{{PROJECT-NAME}}/{{PROJECT-NAME}}.fsproj
```

Adjust project structure as needed (library vs console, test project, etc.).

## 6. Initial commit

```bash
git add -A
git commit -m "chore: scaffold {{PROJECT-NAME}} project"
git push
```

## 7. Update NEXUS-admin/CLAUDE.md (if adding a new standard)

If this project introduced a new pattern or setup that should be the standard for future projects, update the templates here in NEXUS-admin.
