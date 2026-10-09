# skills
Software engineering skills for agentic workflows

## Install

The skills ship as a Claude Code plugin. Install it from this repository, which adds the `manuelcattelan` marketplace on the way:

```sh
claude plugin install skills --marketplace manuelcattelan/skills
```

Then turn on auto-update: run `/plugin`, open **Marketplaces**, select `manuelcattelan`, and select **Enable auto-update**.

To develop and test skills, install from a local clone instead. Edits then load at the next session start:

```sh
claude plugin install skills --marketplace /path/to/skills
```
