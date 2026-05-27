# saga-

This repository stores reusable Codex skills for Saga/Muffin workflows.

## Skills

### `muffin-svn-table-flow`

Use this skill for Muffin Excel configuration table workflows involving SVN:

- 拉表: update target SVN working copies before editing.
- 改表: edit only the intended local Excel config tables.
- 对比: report concrete changed cells or row/id field changes when requested.
- 提表: commit the target working copy with the provided remark.
- 冲突处理: if a conflict occurs, delete the conflicted local table, update again, reapply, and commit.
- 中文备注: write commit remarks to a UTF-8 file and commit with `-F`.

Skill path:

`muffin-svn-table-flow/SKILL.md`

## Notes

The skill intentionally focuses only on the table update/edit/upload process. Domain-specific numeric tuning rules should live in separate skills or knowledge-base files.
