# custom-skills
Custom skills that actually work. 

## Skills

| Skill | What it does |
|---|---|
| [plan-build-test](skills/plan-build-test/SKILL.md) | Writes an implementation plan to markdown and waits for your approval. Then it builds the feature, has a separate tester subagent write and run tests against the plan's acceptance criteria, and fixes failures until every test passes or it gets blocked. |

## Installing a skill

Copy the skill folder into a skills directory Claude Code reads:

```sh
# personal (all projects)
cp -r skills/plan-build-test ~/.claude/skills/
# or per project
cp -r skills/plan-build-test <your-repo>/.claude/skills/
```

Then invoke it with `/plan-build-test <feature description>`, or just ask Claude to "plan and build" a feature.
