---
title: Git-Like Version Control
---

Dolt implements Git-style version control on tables instead of files. 

Dolt adopts the Git-interface to version control. There are [commits](./commits.md), [branches](./branch.md), [merges](./merge.md), and all the other Git concepts you are familiar with. If you know Git, Dolt will feel very familiar because conceptually, Dolt is modeled on Git.

On the command-line, these concepts are exposed as a replica of the Git command line. Where you would type `git log`, you now type `dolt log`. Where you would type `git add`, you type `dolt add`. The replication extends to the command arguments.

In SQL, Dolt becomes a bit more complicated because no Git-equivalent to SQL exists. Git read operations are modeled as [system tables](../../../reference/sql/version-control/dolt-system-tables.md). Git write operations are modeled as [stored procedures](../../../reference/sql/version-control/dolt-sql-procedures.md). But conceptually, all the Git concepts you are familiar with extend to SQL. 

In this section we explore the following Git concepts and explain how they work in Dolt:

1. [Commits](concepts/dolt/git/commits.md)
2. [Log](concepts/dolt/git/log.md)
3. [Diff](concepts/dolt/git/diff.md)
4. [Branch](concepts/dolt/git/branch.md)
5. [Merge](concepts/dolt/git/merge.md)
6. [Conflicts](concepts/dolt/git/conflicts.md)
7. [Remotes](concepts/dolt/git/remotes.md)
8. [Working Set](concepts/dolt/git/working-set.md)