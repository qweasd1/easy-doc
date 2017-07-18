# easy-fs
easy-fs module provide you a well defined interface to do CRUD operations and monitor file changes on your file system.

## Initialization
to install it:
```bash
// yarn
yarn add easy-fs

// npm
npm install --save easy-fs

```

to use it, you need to first initialize the root of your project and then call the methods on it
```javascript
const EasyFs = require('easy-fs');
// initialize your project root first, here we use the current working directory
let project = EasyFs.create({
  cwd:process.cwd()
})

// get or create a directory
// here, we create the src folder if it's not exists 
let directory = project.dir("src/");

// get or create a file
// here, we create the src/sample.js file and write its content as 'console.log('hello world!'
let file = project.file("src/sample.js").write("console.log('hello world!')");
```


## Core Model

### Node
In easy-fs, ```Node``` represents either a File or Directory. 
Actually, there is a ```Node``` class which is the base class for File and Directory object.
However, we usually don't use this class directly.

> from now on, **Node** will refer to either File or Directory, **Directory** will refer to Directory, **File** will refer to File 

### Directory
The ```Directory``` represents a directory in file system. Actually, when you call the ```EasyFs.create(...)```, the return value is a Directory object

#### Attributes
Here are all attributes on Directory object:
* ```name:string```: the name of current directory
* ```absolutePath:string```: absolute path of current directory
* ```relativePath:string```: relative path of current directory to the root of project
* ```type:string``` : type of the node, will be 'dir' for Directory node
* ```parent:Directory```: parent directory, if the current directory is the root, parent will be null


```javascript
...
let directory = project.dir("src/");
// here <root> represents the absolutePath for the root of project
directory.name           // 'src'
directory.absolutePath   // '<root>/src'
directory.relativePath   // 'src' 
directory.type           // 'dir' 
directory.parent         // Directory object for <root>
```

#### Methods

* ```dir(...paths)``` : get or create the Directory object for given paths. If the Directory and its parent Directory not existing on file system, they will be created
    * ```...paths:string```: paths for the directory 
    * ```<return>:Directory```: return the chosen directory object
     
> Here are some samples:

```javascript
// you can use only one path
let subDirectory = directory.dir("path/to/folder")
// or you can join several paths
subDirectory = directory.dir("path","to","folder")
 
```
________

* ```file(...paths)```: get or create the File object for given paths. If the File and its parent Directory not existing on file system, they will be created
    * ```...paths:string```: paths for the file
    * ```<return>:File```: return the chosen file object
    
> Here are some samples:

```javascript
// you can use only one path
let file = directory.file("path/to/file.js")
// or you can join several paths
file = directory.file("path","to","file.js")
 
```
_________

* ```find(glob)```: find the first descendant match the given [glob](https://github.com/isaacs/node-glob) pattern
    * ```glob:string```: glob pattern
    * ```<return>:File|Directory```: return the match Node
    
> Here are some samples:

```javascript
// you can use only one path
let node
// find specific file
node = directory.find("path/to/file.js")
// find the direct child js file
node = directory.find("*.js")
// find descendant js file
node = directory.find("**/*.js")
// find any child directory
node = directory.find("*/") 
// find descendant direcotry which has a 'Test' suffix
node = directory.find("**/*Test/") 
```
_________

* ```findAll(glob)```: find the all descendant nodes match the given [glob](https://github.com/isaacs/node-glob) pattern
    * ```glob:string```: glob pattern
    * ```<return>:Array<File|Directory>```: return all the match Nodes
    
_________

* ```remove()```: remove the current directory and all content in it in the file system
_________

* ```bundle(bundleObj)```: create/update file specify in bundleObj
    * ```bundleObj:Object```: a plain JSON object represent the structure of sub directory. For the object, each key represent the file/directory's name, each value, if it's string, represents the content of the file, if it's an object, it represents an directory and the object represents the inner structure of the directory

> Here are some samples:

```javascript

let bundleObj = {
  "item.js":"consolo.log('content of item.js')",
  "item.test.js":"consolo.log('content of item.test.js')",
  "emptyFolder":{}, // this is an empty sub folder
  // this a sub folder with some files
  "subFolder":{
    "inner.js": "consolo.log('content of inner.js')"
  }
}

// the bundleObj represent the following folder structure
// item.js
// item.test.js
// - emptyFolder/
// - subFolder/
//     inner.js

// when you call bundle method, it will create the specify files and directories if they does not exist. 
// It will update the content of files if the files already exists
directory.bundle(bundleObj)
```
We have a separate module called easy-bundle to help you create bundle object more easily. 
    
_________


### File

#### Attributes
Here are all attributes on Directory object:
* ```name:string```: the name of current directory
* ```absolutePath:string```: absolute path of current directory
* ```relativePath:string```: relative path of current directory to the root of project
* ```type:string``` : type of the node, will be 'file' for Directory node
* ```parent:Directory```: parent directory, if the current directory is the root, parent will be null
* ```baseName:string```: filename without extension (e.g. for "a.js", the baseName is "a")
* ```ext:string```: file extension (e.g. for "a.js", ext is "js"")
* ```content:string```: content of the file, this is a property, it will read content of file from disk lazily and cache it. If the file is marked as dirty, the content will be read again
* ```isDirty:boolean```: indicate whether the current file's content cache is out of date, you can set this value to false manully and access the ```content``` to get the newest file's content. The isDirty is managed automatically by a inner file watcher, so usually, you don't need to manually update it. see [here](#file-change-event-and-file-dirty-check) for more detaisls 



```javascript
...
let file = project.dir("src/test.js");
// here <root> represents the absolutePath for the root of project
file.name           // 'test.js'
file.absolutePath   // '<root>/src/test.js'
file.relativePath   // 'src/test.js' 
file.type           // 'file' 
file.parent         // Directory object for <root>/src
file.baseName       // 'test'
file.ext            // 'js'
file.content        // content of '<root>/src/test.js' file
```

#### Methods
* ```remove()```: remove the current file in the file system
* ```write(content)``` : write content to the file
    * ```content:string``` : content you want to write to the file
_________


## File Watcher

### basic usage
you can register file watcher on any ```Node``` by calling its ```.on(eventName [,filters], callback)```
```javascript

const EasyFs = require('easy-fs');

let project = EasyFs.create({
  cwd:process.cwd()
})

// register watcher on a directory
project.dir("src").on("child.add",(path)=>{
  console.log(path);
})

// register watcher on a file
pdescendantse("src/index.js").on("change",(path)=>{
  console.log(path);
})
```
Here the path in the ```callback``` parameter is the relative path from the changed file to the ```Node``` you register the event. You might also notice there is an optional ```filters``` parameter.
We will discuss it in [event filter](#event-filter) section

### event types
easy-fs provide 2 different kind of events, the ```child``` events and ```node```(file/directory) event.
 
The ```child``` event is defined on all ```Directory``` object and will fired when any of **descendants**(not only direct child) of the Directory changes.

Here are all the ```child``` events:
* ```child.addFile```    : new descendant file added
* ```child.removeFile``` : descendant file removed
* ```child.addDir```     : new descendant directory removed
* ```child.removeDir```  : descendant directory removed
* ```child.chage```      : descendant file's content changed

 
The ```node``` event is defiend on all ```File``` and ```Directory``` object and will fired when the node itself changed

Here are all the ```node``` events:
* ```child.remove```    : when the node(both file / directory) removed
* ```child.change```    : when the file's content changed

### event filter
There are two kinds of event filter in easy-fs: 

* pre event filter
* post event filter

#### pre event filter
 when you call ```EasyFs.create(options)```, you can pass two options ```include``` and ```ignored``` to restrict the files you want to watch:
```javascript
// by default, file watcher will watch all files change under root directory
let project = EasyFs.create({
  cwd:process.cwd()
})

// if you give the 'include' option, the file watcher will only apply to "src" and "node_modules" and their children 
let project = EasyFs.create({
  cwd:process.cwd(),
  include:["src","node_modules"]
})

// you can use any glob pattern inside 'include' like '**/*.js'

// however, 'include' option will watch all descendants if the glob pattern match to a directory
// sometimes, this is not the desired effect
// e.g. you might want to monitor the change of 'node_modules' directory, but you only care about whether a direct child of 'node_modules' add or remove and don't care about other descendant files' change
// you can use 'ignored' option to filter out all other descendants event
// again ignored support all glob pattern

let project = EasyFs.create({
  cwd:process.cwd(),
  include:["src","node_modules"],
  ignored:["node_modules/*/**/*"]
})

```

#### post event filter
when you call ```.on(eventName [,filters], callback)```, you can also use the optional ```filters``` parameter to restrict to files you want watch. This the post event filter

Here is a sample:
```javascript
let directory = project.dir("src")
// this filter will only watch all added descendant js file
let dispose = directory.on("child.addFile","**/*.js",(path)=>{
  
})

// you can then call dispose() to unregister the event
```
the ```filters``` parameters can be ```null```, ```glob``` pattern or any [anymatch](https://github.com/es128/anymatch) compatible matchers (add more examples here)

when you use **post event filter** to filter directory related events like ```child.addDir```, ```child.removeDir```, if you use glob, the trailing '/' at the end of glob is optional:
```javascript
// this will work
directory.on("child.addDir","**/*/",(path)=>{
  
})

// this will also work
directory.on("child.addDir","**/*",(path)=>{
  
})

```
TODO: We will enhance this part in the future

Event filter is only supported on ```Directory``` object, ```File``` don't support and will ignore it

#### file change event and file dirty check
On ```File``` object, there is an ```isDirty``` attribute to indicate whether this File's content changed from the last read of its content. 
The File object use this attribute in the ```content``` property to lazy load the content of file only when isDirty is true and mark it false after the reading.
Also, all files you try to watch after apply the [pre event filter](#pre-event-filter) will automatically update the ```isDirty``` attribute when file content change.
You can also manually mark this attribute if you want

#### event filter best practice
* specify pre event filter if possible to watch only necessary file change.
* use child event to monitor "change" or "remove" of ```Node``` will have better performance.
* use node event to monitor limit number of specific files (e.g. the package.json file), you will get better performance if doing so.

    

## create options (Move to API Refernce in the future)
* ```cwd:string``` (reqruied): the root of your project.
* ```include:Array<string>``` (optional): the paths you want to watch. Support all glob pattern
* ```ignored:Array<string>```(optional): the files you don't want to watch
 

## Caution
* anytime you specify a path, please use a linux style even you are working on a windows system (use '/' instead of '\\')