# Using the File System (FS) Module to Read and Write Files

## Introduction to the FS Module

With a good understanding of the require function, package.json, and npm, we can now explore the FS module in detail. The FS module is one of the most important built-in modules in Node.js.

## Importing the FS Module

### Using the node Namespace
To use the FS module, you first need to import it. There is a relatively newer way to import built-in modules using the node namespace:

```javascript
const fs = require('node:fs');
```

### Traditional Import
You can also use the traditional import without the namespace:

```javascript
const fs = require('fs');
```

Both approaches work, but the newer way using `node:fs` is preferred because:
- It avoids conflicts with third-party modules that might have the same name
- It makes it explicit that this is a built-in module
- It is the recommended approach in newer versions of Node.js

### Import Pattern Recognition
You can identify the type of module by looking at the require statement:
- `require('node:fs')` - starts with `node:` means it's a built-in module
- `require('somepackage')` - just a name means it could be a third-party module
- `require('./math.js')` - starts with a dot means it's a custom module

## Reading Files Synchronously

### Reading File Contents with readFileSync
One of the most basic operations with the FS module is reading a file. You can read a file synchronously using the `readFileSync` function:

```javascript
const fs = require('node:fs');
const contents = fs.readFileSync('notes.txt', 'utf8');
console.log(contents);
```

### Parameters of readFileSync
The `readFileSync` function takes two parameters:
1. The path to the file you want to read (for example, `'notes.txt'`)
2. The encoding you want to read the file in (for example, `'utf8'` for text files)

### Return Value
The `readFileSync` function returns the contents of the file as a string (if you specify an encoding like `'utf8'`).

## Writing Files Synchronously

### Creating and Writing Files with writeFileSync
You can create a new file and write content to it using the `writeFileSync` function:

```javascript
fs.writeFileSync('copy.txt', 'This is the content I want to write', 'utf8');
```

### Parameters of writeFileSync
The `writeFileSync` function takes three parameters:
1. The name/path of the file you want to create
2. The content you want to write to the file
3. The encoding you want to use (typically `'utf8'`)

### Important Behavior: Overwriting Content
One important thing to note about `writeFileSync` is that it always overrides the content. If the file already exists and contains data, that data will be completely replaced. The function does not append to existing content - it overwrites the entire file.

## Appending Content to Files

### Using appendFileSync
If you want to add content to a file without overwriting the existing content, you use the `appendFileSync` function:

```javascript
fs.appendFileSync('file.txt', 'New content to append', 'utf8');
```

### Parameters of appendFileSync
The `appendFileSync` function takes three parameters:
1. The name/path of the file
2. The content you want to append
3. The encoding (typically `'utf8'`)

### Behavior: Adding Content
Unlike `writeFileSync`, the `appendFileSync` function adds new content to the end of an existing file. The previous data stays intact, and the new content is added after it.

### Adding Line Breaks
You can add line breaks when appending content by including the newline character:

```javascript
fs.appendFileSync('file.txt', '\n\nNew content on a new line', 'utf8');
```

Using `'\n\n'` will add two line breaks before your new content.

## Creating Directories

### Using mkdirSync
You can create directories using the `mkdirSync` function:

```javascript
fs.mkdirSync('games');
```

This will create a directory called `games` in your current location.

### Creating Nested Directories
If you want to create nested directories (directories within directories), you need to pass an options object:

```javascript
fs.mkdirSync('games/xyz/abc', { recursive: true });
```

### The recursive Option
By default, if parent directories don't exist, `mkdirSync` will throw an error. For example, if you try to create `games/xyz/abc` but the `games` directory doesn't exist, it will fail. By setting `{ recursive: true }`, Node.js will automatically create all the parent directories that don't exist. This matches the behavior of the `mkdir -p` command in Linux.

## Removing Directories

### Using rmdirSync
You can delete directories using the `rmdirSync` function:

```javascript
fs.rmdirSync('games');
```

### Important Requirement: Empty Directories
There is one important requirement: the directory must be empty. If the directory contains files or subdirectories, `rmdirSync` will throw an error. This is a Linux feature - you cannot delete a directory if it has content.

### Removing Nested Directories
If you want to delete a directory that has subdirectories inside it, you need to delete the subdirectories first, then the parent directory. For example:
1. Delete the file or subdirectory inside
2. Then delete the parent directory

This is consistent with how Linux file systems work.

## Deleting Files

### Using unlinkSync
To delete a file, you use the `unlinkSync` function:

```javascript
fs.unlinkSync('copy.txt');
```

This will delete the file `copy.txt`. After running this command, the file will no longer exist.

## Overview of FS Functions

The FS module provides many functions for working with files and directories:
- `appendFileSync` - append content to a file
- `readFileSync` - read file contents
- `writeFileSync` - write/create a file
- `mkdirSync` - create directories
- `rmdirSync` - remove empty directories
- `unlinkSync` - delete files
- `chmodSync` - change file permissions
- And many other functions

All of these functions are for working with the file system and provide extensive capabilities for file and directory management.

## Synchronous vs Asynchronous

### Every Function Has Both Versions
Each FS operation has two versions:
- A synchronous version (ending with `Sync`) - blocking operation
- An asynchronous version (without `Sync`) - non-blocking operation

For example:
- `readFileSync` vs `readFile`
- `writeFileSync` vs `writeFile`
- `mkdirSync` vs `mkdir`

### When to Use Each
As of now, we are using the synchronous versions. The difference between synchronous and asynchronous versions will be covered in upcoming lectures. For now, understand that these functions do similar things but in different ways.

## Using the node Namespace Going Forward

### Recommended Practice
Going forward, when importing built-in modules, use the `node:` namespace:

```javascript
const fs = require('node:fs');
```

This makes your code clearer and more explicit about what type of module you are using. It also avoids potential conflicts with third-party modules.

## Summary

The FS module provides comprehensive functionality for working with files and directories in Node.js:
- You can read file contents using `readFileSync`
- You can write new files using `writeFileSync`
- You can append to existing files using `appendFileSync`
- You can create directories using `mkdirSync`
- You can remove empty directories using `rmdirSync`
- You can delete files using `unlinkSync`

All these operations are synchronous (blocking) operations. Understanding how to use these functions is fundamental to working with the file system in Node.js. In upcoming sections, we will explore asynchronous versions of these operations and understand when and why to use them.
