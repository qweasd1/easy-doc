# easy-bundle

easy-bundle help you create and manage your bundle object easily


## Installation
```bash
// yarn
yarn add easy-bundle

// npm
npm install --save easy-bundle
```
you can then create a bundle like:
```javascript
const Bundle =  require('easy-bundle');
let bundle = Bundle({
  "src":{
    "index.js":"console.log('hello world!')"
  }
})

// use bundle with your easy-fs system
// suppose you already have a Directory object...
d.bundle(bundle.value)
```

## The design of Bundle
If you already read the easy-fs document, you should already know the ```bundle object```. 
It's simply an JSON object which represents a set of files in memory:
 
```javascript
// sample bundle object
let bundleObj = {
  "src":{
    "index.js":"console.log('hello world!')"
  }
}
```
Since bundle object is just a plain JSON object, you can modify it before passing it to ```Directory.bundle(bundleObj)``` method. 
You may create more files dynamically, compose/render the content of file from template, change the file's name dynamically easily in your code.

The concept of Bundle also boost the modular development. We provide helper methods to help you merge many bundles together to construct new bundles. 
```javascript
let bundle1 = Bundle({
  "src":{
    "index.js":"//index.js file"
  }
})

let bundle2 = Bundle({
  "src":{
    "test.js":"//test.js file"
  }
})

let bundleMerged = bundle1.mergeWith(bundle2)

// the value of bundleMerged.value is: 
{
  "src":{
    "index.js":"//index.js file",
    "test.js":"//test.js file"
  }
}


```

This is a very good feature, since you can then divide your scaffolding logic into separate reusable modules and compose them together
E.g. most projects require git support and usually a scaffolding tool is supposed to create an .gitignore file. You can create a module to create bundle for ".gitignore" file and then merged into any other project's bundle.
 
In one word, bundle help you create modular and reusable scaffolding tools easily


## API of Bundle

### create Bundle
You have 2 ways to create Bundle object:
* from JSON object
* from file system

```javascript

// from JSON object
let bundleFromObj = Bundle({
  "index.js":"//content"
})

// from file system
// this will load directories/files from './bundleDir' recursively and create the JSON object for the folder structure 
let bundleFromFile = Bundle.fromFile("./bundleDir")

// e.g. if there is only 1 file in "./bundleDir" called: "./bundlrDir/src/index.js"
// the result bunle object will be 
{
  src:{
    "index.js":"..."
  }
}


```

### modify bundle
You have 3 ways to modify Bundle object:
* directly modify ```Bundle.value``` attribute
* use ```Bundle.pipe(...transforms)```
* use ```Bundle.mergeWith(...otherBundles)```

#### directly modify ```Bundle.value``` attribute
The simplest way to modify the bundle.value attribute, this attribute is just the internal reference to the bundle object
```javascript

let b = Bundle({
  src:{}
})

b.value.src["test.js"] = "// ..."
```
This method is very straight and easy to understand, but it modify the reference of the bundle object which is not immutable. 
It's hard for you to reuse the bundle object.

#### use ```Bundle.pipe(...transforms)```
The second method is you can apply many transform functions to your bundle
```javascript
let b = Bundle({
  src:{}
})
let newBundle = b.pipe((bundleObj)=>{
  bundleObj.src["test.js"] = "//..."
  return bundleObj
}, (bundleObj)=>{
  bundleObj.src["test2.js"] = "//..."
  return bundleObj
})
```
Each transform function has the following signature: ```(bundleObj:object):object```. 
The value returned from transform function will be new bundle object paassed to the next transform function. 
The transform functions will apply from left to right.
The input for the first transform function will be the deep copy of the value of the bundle which calls the ```.pipe(...)``` method. 
So the result of ```.pipe(...)``` will definitely contains a new bundle object.
In this way, we can reuse the origin bundle in our code


#### use ```Bundle.mergeWith(...otherBundles)```
The final way to modify bundle is using ```.mergeWith(...otherBundles)```
```javascript
let projectBundle = Bundle({
  src:{
    "index.js":"// ..."
  }  
})

let gitBundle = Bundle({
  ".gitignore":"node_modules"
})

// .mergeWith also support merge with direct JSON object
let finalBundle = projectBundle.mergeWith(gitBundle,{
  src:{
    "index.test.js":"//..."
  }
})

```
The ```.mergeWith(...otherBundles)``` will first create a copy of current bundleObj.
It then do a deep copy from each otherBundles into this copy bundle object.
So the result of ```.mergeWith(...)``` will definitely contains a new bundle object.
In this way, we can reuse the origin bundle in our code


### Helper Utilities

#### Visitor
visitor is an utility class to help you visit nodes and change nodes in a bundle object.
Here is a short sample to render and rename all ".ejs" template in bundle.

```javascript
const Bundle =  require('easy-bundle');
let context = {name:"tony"}
let visitor = Bundle.Visitor({
  // when visit all descendant js file, invoke the callback
  "**/*.ejs":(parent, key, value)=>{
    delete parent[key]
    
    // change file name to "*.js"
    let newName = key.substring(0,key.length - 3) + "js"
    // suppose we have a renderEjsTemplate method to render ejs template
    parent[newName] = renderEjsTemplate(value,context)
  }
})
Bundle.visit(visitor, {
  "a.ejs":"<%= name%>"
})
```

you can also visit directory node, by using pattern like "**/test/", the trailing '/' indicate this pattern match directories.

By using visitor, it's very easy to implement complex Bundle transform functions


#### Utility methods
We also provide some utility methods to help you work easier with Bundle:
* Bundle.contentOf(path) : get content of file for given path
    * ```path:string```: path to a file 




