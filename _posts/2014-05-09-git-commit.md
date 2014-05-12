---
layout: post
title: git commit
date: 2014-05-09 15:11:12
---

1. stage all modified and deleted paths

  ```
  --all
  ```

1. allow recording an empty commit

  ```
  --allow-empty
  ```

1. allow recording a commit with an empty message

  ```
  --allow-empty-message
  ```

1. amend the tip of the current branch

  ```
  --amend
  ```

1. override the author name used in the commit

  ```
  --author
  ```

1. specify how the commit message should be cleaned up

  ```
  --cleanup
  ```

1. override the author date used in the commit

  ```
  --date
  ```

1. only show list of paths that are to be commited or not, and any untracked

  ```
  --dry-run
  ```

1. edit the commit message before committing

  ```
  --edit
  ```

1. read commit message from given file

  ```
  --file
  ```

1. update the given files and commit the whole index

  ```
  --include
  ```

1. interactively update paths in the index file

  ```
  --interactive
  ```

1. use the given message as the commit message

  ```
  --message
  ```

1. do not include the output of git status in the commit message template

  ```
  --no-status
  ```

1. do not look for suspicious lines the commit introduces

  ```
  --no-verify
  ```

1. separate dry run entry output with NUL

  ```
  --null
  ```

1. commit only the given files

  ```
  --only
  ```

1. output dry run in porcelain-ready format

  ```
  --porcelain
  ```

1. suppress commit summary message

  ```
  --quiet
  ```

1. use existing commit object and edit log message

  ```
  --reedit-message
  ```

1. use existing commit object with same log message

  ```
  --reuse-message
  ```

1. output dry run in short format

  ```
  --short
  ```

1. add Signed-off-by line at the end of the commit message

  ```
  --signoff
  ```

1. include the output of git status in the commit message template

  ```
  --status
  ```

1. use file as a template commit message

  ```
  --template
  ```

1. show files in untracked directories

  ```
  --untracked-files
  ```

1. show unified diff of all file changes

  ```
  --verbose
  ```