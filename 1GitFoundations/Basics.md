## DataStorage

### HOW DOES GIT STORE INFORMATION?

    ➤ At its core, git is like a key value store.
    ➤ The Value = Data
    ➤ The Key = Hash of the Data
    ➤ You can use the key to retrieve the content.

THE KEY - SHA1
       
         ➤ Is a cryptographic hash function.
         ➤ Given a piece of data, it produces a 40-digit hexadecimal
           number. 
         ➤ This value should always be the same if the given input is the
         same.-So when u run git log,you see a lot of 40 digit hexa numbers-->those are Sha1
 
 Such systems are also called content addressable systems,thats becoz u use content to generate the key
 
 ## Git blobs and Trees
 
 The way git stores info is in form of git objects
 The very basic such object is a blob
 
 ### Git blob
 THE VALUE - BLOB
 
 
     ➤ git stores the compressed data in a blob, along with metadata in
        a header:
     ➤ the identifier blob
     ➤ the size of the content
     ➤ \0 delimiter
     ➤ content



|blob | 14     | 
|-----|------- |
| \0           |
| Hello World! |



### UNDER THE HOOD - GIT HASH-OBJECT

Asking Git for the SHA1 of contents:

```
❯ echo 'Hello, World!' | git hash-object --stdin //--stdin is used to tell the command to read from input otherwise a file as input is expected

8ab686eafeb1f44702738c8b0f24f2567c36da6d
```
Generating the SHA1 of the contents, with metadata:
```
❯ echo 'blob 14\0Hello, World!' | openssl sha1
8ab686eafeb1f44702738c8b0f24f2567c36da6d
```
It’s a Match! 

### Where does git store these objects?

```
❯ git init
Initialized empty Git repository in /Users/nina/projects/sample/.git/

```
.git directory contains data about our repository
 In the .git directory, git stores project information and its metadata into blob, tree, and commits objects, 
 with each object storing different parts of the project like its content and folder structure.
 Deleting the .git directory,the information and history of the project are gone, but not the files of the project.

### Where are the blobs stored
 
```
echo 'Hello, World!' | git hash-object -w --stdin // -w flag means write
tree .git
.git
├── HEAD
├── config
├── description
├── info
│ └── exclude
├── objects //our blob is stored in objects
│ ├── 8a  //the directory starts with the first 2 chars of the hash.
│ │ └── b686eafeb1f44702738c8b0f24f2567c36da6d
│ ├── info
│ └── pack
└── refs
├── heads

```

### WE NEED SOMETHING ELSE AS WELL IN THE BLOB
The blob is missing some information!
    
    ➤ filenames
    ➤ directory structures
This information is critical especially when we are working with files and not just some strngs inputted by user(as in above examples).
So if we save a file as a blob ,how do we know what that file was called and the directory it was stored in.
Git stores this information in a tree.

### TREE
a tree contains pointers (using SHA1):

    ➤ to blobs
    ➤ to other trees //subdirectories can be nested
       and metadata:  
    ➤ type of pointer (blob or tree)
    ➤ filename or directory name
    ➤ mode (executable file, symbolic link, …)
    
  |Tree|      |<size-14>| 
  |----|------|---------|
  | \0                  |
  |blob|8ab68 |hello.txt|
  |tree|a14ca |copies   |
    
```
├── copies
│ └── hello-copy.txt
└── hello.txt
1 directory, 2 files
    
    ```
***  Git doesnt let u store empty directory---->limitation with the staging area,as it only keeps track of files

    ///Git deltassssssssssssssssssssss
    
## Commits
    
###    COMMIT OBJECT
    
a commit points to:
                            
    ➤ a tree
       and contains metadata:
    ➤ author and committer
    ➤ date
➤ message
➤ parent commit (one or more)
the SHA1 of the commit is the hash of all this information
    
