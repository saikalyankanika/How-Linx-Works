## Bourne shell - /bin/sh

- it is a program that runs commands like users run them on terminal, like an programming environment 
- Bash is a bourne again shell which is an extension or added features of bourne shell , now every linux is coming by default with bash.



# Linux Notes: Archiving and Compressing Files

## Overview

In Linux, **archiving** and **compression** are two different operations:

1. **Archiving** = Combine multiple files/directories into a single file.
2. **Compression** = Reduce the size of that file.

```text
Files/Directories
       │
       ▼
     tar
       │
       ▼
 archive.tar
       │
       ▼
    gzip
       │
       ▼
archive.tar.gz
```

---

# 1. gzip

`gzip` (GNU Zip) is a compression utility.

A file ending in `.gz` is a gzip-compressed file.

## Compress a File

```bash
gzip file
```

Result:

```text
file.gz
```

## Decompress a File

```bash
gunzip file.gz
```

Result:

```text
file
```

## Important

`gzip` does **not** create archives.

It can only compress a single file.

```text
report.txt
     │
     ▼
   gzip
     │
     ▼
report.txt.gz
```

---

# 2. tar

`tar` (Tape Archive) is used for archiving files and directories.

Unlike `gzip`, `tar` can package multiple files and directories into a single archive.

## Create an Archive

```bash
tar cvf archive.tar file1 file2 dir1
```

Result:

```text
archive.tar
```

---

# Understanding tar Options

## c = Create

Create a new archive.

```bash
tar cvf archive.tar files
```

---

## x = Extract

Extract files from an archive.

```bash
tar xvf archive.tar
```

---

## t = Table of Contents

List files inside an archive without extracting.

```bash
tar tvf archive.tar
```

---

## v = Verbose

Show files being processed.

```bash
tar xvf archive.tar
```

Example output:

```text
file1
file2
dir1/file3
```

### Double Verbose

Some tar versions support:

```bash
tar xvvf archive.tar
```

Shows additional details such as:

- Permissions
- Ownership
- File size

---

## f = File

Specifies the archive file name.

```bash
tar cvf archive.tar files
```

The argument immediately after `f` must be the archive filename.

---

# Creating Archives

Suppose we have:

```text
project/
├── app.py
├── config.yaml
└── README.md
```

Create archive:

```bash
tar cvf project.tar project/
```

Result:

```text
project.tar
```

---

# Extracting Archives

Extract all files:

```bash
tar xvf project.tar
```

Result:

```text
project/
├── app.py
├── config.yaml
└── README.md
```

---

# Extracting Specific Files

You can extract specific items if you know their exact names.

```bash
tar xvf project.tar project/config.yaml
```

---

# Important Note

Extracting does **not** remove the archive itself.

Before:

```text
project.tar
```

After extraction:

```text
project.tar
project/
```

The archive remains.

---

# Table of Contents Mode

Before extracting, it is a good practice to inspect the archive.

```bash
tar tvf project.tar
```

Example:

```text
project/
project/app.py
project/config.yaml
project/README.md
```

---

# Why Check Contents First?

A badly structured archive may dump files directly into the current directory.

Good archive:

```text
project/
project/file1
project/file2
```

Bad archive:

```text
file1
file2
file3
```

Extracting the bad archive may clutter your current directory.

### Recommendation

Create a temporary directory first:

```bash
mkdir temp
cd temp
tar xvf ../archive.tar
```

---

# Preserving Permissions

Use the `p` option when extracting:

```bash
tar xvpf archive.tar
```

This preserves:

- File permissions
- Ownership information

Useful for:

- Backups
- System restores
- Configuration archives

---

# Important Root User Note

When extracting as root:

```bash
sudo tar xvpf archive.tar
```

**Do not interrupt tar before it finishes.**

Reason:

- tar checks the archive completely
- Permissions may be applied near the end
- Interrupting can leave files with incorrect permissions or ownership

Always wait until the shell prompt returns:

```bash
$
```

before assuming extraction is complete.

---

# Compressed Archives (.tar.gz)

Most Linux archives are distributed as:

```text
file.tar.gz
```

Meaning:

```text
tar archive
      +
gzip compression
```

---

# Manual Extraction Process

Work from right to left.

## Step 1: Remove .gz

```bash
gunzip file.tar.gz
```

Produces:

```text
file.tar
```

## Step 2: Extract .tar

```bash
tar xvf file.tar
```

---

# Creating a .tar.gz Archive

Reverse the process.

## Step 1: Create Archive

```bash
tar cvf file.tar project/
```

## Step 2: Compress Archive

```bash
gzip file.tar
```

Result:

```text
file.tar.gz
```

---

# zcat

The previous approach creates a temporary `.tar` file.

This:

- Uses extra disk space
- Requires extra I/O operations

A more efficient method is:

```bash
zcat file.tar.gz | tar xvf -
```

---

# What is zcat?

Equivalent to:

```bash
gunzip -dc file.tar.gz
```

Options:

```text
-d = decompress
-c = write output to stdout
```

---

# Understanding the Pipeline

```bash
zcat file.tar.gz | tar xvf -
```

Flow:

```text
file.tar.gz
      │
      ▼
    zcat
      │
      ▼
 decompressed tar stream
      │
      ▼
     tar
      │
      ▼
 extracted files
```

No temporary `.tar` file is created.

---

# Meaning of "-"

In:

```bash
tar xvf -
```

The dash means:

```text
Read archive data from standard input (stdin)
```

instead of reading from a physical file.

---

# tar's Built-in gzip Shortcut

Because `.tar.gz` files are extremely common, tar can call gzip automatically.

Instead of:

```bash
zcat file.tar.gz | tar xvf -
```

Use:

```bash
tar xzvf file.tar.gz
```

---

# The z Option

```text
z = use gzip automatically
```

Works with:

- c (create)
- x (extract)
- t (list)

---

# Common tar.gz Commands

## Create

```bash
tar czvf backup.tar.gz backup/
```

Meaning:

```text
c = create
z = gzip
v = verbose
f = filename
```

---

## Extract

```bash
tar xzvf backup.tar.gz
```

Meaning:

```text
x = extract
z = gunzip automatically
v = verbose
f = filename
```

---

## View Contents

```bash
tar ztvf backup.tar.gz
```

Meaning:

```text
t = table of contents
z = gunzip automatically
v = verbose
f = filename
```

---

# .tgz Files

A:

```text
file.tgz
```

is exactly the same as:

```text
file.tar.gz
```

The `.tgz` extension exists mainly for compatibility with older FAT/MS-DOS filesystems.

---

# Other Compression Utilities

## xz

Compress:

```bash
xz file
```

Result:

```text
file.xz
```

Decompress:

```bash
unxz file.xz
```

---

## bzip2

Compress:

```bash
bzip2 file
```

Result:

```text
file.bz2
```

Decompress:

```bash
bunzip2 file.bz2
```

---

## Comparison

```text
gzip   -> Fast compression/decompression
bzip2  -> Better compression, slower
xz     -> Best compression, usually slowest
```

---

# ZIP Archives

Linux also supports Windows ZIP archives.

## Create ZIP

```bash
zip archive.zip file1 file2
```

## Extract ZIP

```bash
unzip archive.zip
```

Supports:

- `.zip`
- Self-extracting `.exe` archives

---

# Legacy .Z Files

Files ending with:

```text
file.Z
```

were created using the old Unix `compress` utility.

Decompress:

```bash
gunzip file.Z
```

Modern `gzip` can unpack `.Z` files but does not create them.

---

# Commands to Memorize

## gzip

```bash
gzip file
gunzip file.gz
```

## tar

```bash
tar cvf archive.tar dir/
tar xvf archive.tar
tar tvf archive.tar
```

## tar + gzip

```bash
tar czvf archive.tar.gz dir/
tar xzvf archive.tar.gz
tar ztvf archive.tar.gz
```

## Streaming

```bash
zcat archive.tar.gz | tar xvf -
```

---

# Interview One-Liners

### What is gzip?

> gzip compresses a file but does not combine multiple files into an archive.

### What is tar?

> tar combines multiple files/directories into a single archive without compressing them.

### What is .tar.gz?

> A `.tar.gz` file is a tar archive that has been compressed using gzip.

### What does zcat do?

> zcat decompresses a gzip file and writes the result to standard output.

### Difference Between tar and gzip

| tar | gzip |
|------|------|
| Creates archives | Compresses files |
| Combines multiple files | Works on a single file |
| Produces `.tar` | Produces `.gz` |
| No compression by default | Compression only |