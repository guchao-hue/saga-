---
name: muffin-svn-table-flow
description: Use when Codex needs to update, edit, compare, or upload Muffin local Excel configuration tables through SVN, especially for "拉表", "改表", "提表", "传表", SVN update/commit, conflict recovery, Chinese commit remarks, or Muffin table working-copy operations.
---

# Muffin SVN Table Flow

## Scope

Use this skill for Muffin Excel configuration table operations that involve local SVN working copies.

Working-copy roots:

- Main/trunk: `C:\Users\A-AAA-202501-16\muffin\trunk-tw`
- TW: `C:\Users\A-AAA-202501-16\muffin\test-tw`
- CN: `C:\Users\A-AAA-202501-16\muffin\test-cn`

Default server-scope rule:

- Always update and edit `trunk-tw` when changing a table, unless the user explicitly says not to.
- Edit `test-tw` and/or `test-cn` only when the user asks for those servers, says "全服", "国台服", "台服", or "国服", or when prior task context clearly requires them.
- If the user says `testcn不用改` or equivalent, do not edit or commit `test-cn`.

## Core Workflow

1. Run SVN `update` on each target working copy before editing.
2. Edit only the intended local Excel table(s).
3. If the user asks for comparison, report the concrete changed cells or row/id field changes before commit.
4. Commit the target working copy with the user's remark.
5. After upload, run SVN `status` and report the final submitted revisions and comparison summary.
6. Ignore unrelated unversioned Excel temp files such as `~$*.xlsx` unless the user asks to clean them.

## Lock Rule

- Do not run `get lock` before editing or uploading.
- Reason: command-line locking is unreliable for the Chinese-path workbook nodes, and GUI locking creates disruptive popups.
- Conflict-control strategy is `update -> edit -> commit`; if conflict occurs, use the conflict reset rule below.

## Conflict Reset Rule

If SVN upload hits a conflict on a table file:

1. Stop editing that conflicted local copy in place.
2. Delete the conflicted local table file.
3. Run SVN `update` again from the working-copy root.
4. Reapply the intended change from the beginning.
5. Commit again.
6. After successful commit, report the final comparison and revision.

## SVN Tooling

Preferred command-line SVN client:

`C:\Users\A-AAA-202501-16\Desktop\ai工作\tools\sliksvn_extracted\PFiles\bin\svn.exe`

Do not rely on `TortoiseProc.exe /command:commit` as the primary upload path.

Chinese path workaround:

- Some Muffin paths contain Chinese characters and may fail when passed directly to `svn.exe`.
- Use `subst` to map working-copy roots and run SVN commands against the mapped root.

Verified mappings:

- `X:` -> `C:\Users\A-AAA-202501-16\muffin\trunk-tw`
- `Y:` -> `C:\Users\A-AAA-202501-16\muffin\test-tw`
- `Z:` -> `C:\Users\A-AAA-202501-16\muffin\test-cn`

## Commit Remarks

- Every upload must include a remark.
- If the user provides a remark, use it exactly.
- If the user asks to upload but has not provided a remark, ask whether to proceed directly; do not invent a final remark silently.
- Do not pass Chinese remarks with `svn commit -m`.
- Write the full remark to a UTF-8 file without BOM and commit with `-F <file> --encoding utf-8`.
- Create the remark file directly on disk with controlled encoding, then read it back and verify Chinese text is intact before committing.
- Prefer a non-Chinese path for the remark file, for example `C:\Users\A-AAA-202501-16\Desktop\SVN_MSG_<ticket>.txt`.

If a committed log message is garbled:

- Fix it with `svn propset --revprop svn:log -F <utf8-file> --encoding utf-8 -r <revision> <working-copy-root>`.
- Do not recommit the file just to fix the message.
- Verify with `svn log --xml`; plain terminal `svn log` may display Chinese as `?` even when the repository log is correct UTF-8.

## Validation

After commit:

- Run `svn status` on each target working copy.
- Report submitted revision numbers.
- Report the changed table rows/cells in user-facing terms.
- Mention unrelated unversioned temp files only if they remain visible and could confuse the user.
