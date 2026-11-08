# benny

benny gives you two automations for slack issue reports. one triages each report. the other reproduces confirmed bugs and may prepare a small draft fix.

benny needs an automation backend that starts an agent run on each new top-level slack report, in a checkout of the target repository, with slack, repository, and tracker access. cursor automations is one supported backend. any other triggered automation feature or a ci job works when it meets the same needs.

the files in this directory are dormant setup and automation sources. they do not appear as slash skills.

## set it up

1. point your agent at [`FOR_AGENTS.md`](./FOR_AGENTS.md) and name the target repository.
2. let setup merge this whole directory into a committed pack directory in the target, for example `automations/benny/`, or `.cursor/automations/benny/` with cursor automations. it must preserve destination-only files and review conflicts instead of overwriting local edits.
3. let setup enable pstack in project scope for shared dependencies, through the committed project plugin or skill config your harness reads. with cursor, that is the target repository's `.cursor/settings.json`:

```json
{
	"plugins": {
		"pstack": { "enabled": true }
	}
}
```

4. keep user-owned configuration outside the copied pack, for example in `.benny/`. adapt [`configuration.example.yaml`](./templates/configuration.example.yaml) and [`feature-map.example.md`](./skills/reproduce-and-fix-issues/references/feature-map.example.md).
5. commit the project pstack config, the pack directory, and any secret-free configuration before enabling either automation.
6. review each new automation draft or update existing automations in their editors. then send a harmless test report and verify every source-channel post stays in the original thread.
