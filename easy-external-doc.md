# easy-external
install npm package and spawn process easily from code

## Installation
```bash
// yarn
yarn add easy-external

// npm
npm install --save easy-external
```

## Usage
To use easy-external, you need to first create a runner with the current working directory as following:
```javascript
const createRunner =  require('easy-external');

// create a Runner
// 'cwd'(required) is the current working directory for your runner
let runner = createRunner({
  cwd:process.cwd()
})

```
Then you can call the following methods on the runner:
* installPakcages(packageNames,isSaveDev=false): sync install npm packages, if yarn installed, will prefer use yarn instead of npm.
  * ```packageNames:Array<string>```: packages to install
  * ```isSaveDev:boolean```:  whether save packages to devDependencies, default is false which save packages as dependencies
  * ```<return>:null|Object```: if everything run successfully, return null, else return an object with 2 attributes: the ```error``` attribute show the error message, the ```raw``` attribute show the raw result return from child_process.spawnSync which let you get more information
* installAll() : sync install all dependencies from package.json file
* spawn(cmd,args,options): exactly the same as child_process.spawnSync


```javascript
```javascript
const createRunner =  require('easy-external');

// create a Runner
// 'cwd'(required) is the current working directory for your runner
let runner = createRunner({
  cwd:process.cwd()
})

// use .installPackages(packages,isSaveDev) to install npm package
// if yarn is installed, it will use yarn to install the package, otherwise npm is used
runner.installPackages(["react","redux@3.7.2"])
runner.installPackages(["gulp"],true)

// use .removePackages(packages,isSaveDev) to remove npm package
// if yarn is used, you don't need to specify the second parameter, yarn will choose it for you
runner.removePackages(["react","redux@3.7.2"])
runner.removePackages(["gulp"],true)

// use.spawn(cmd,args,options) to run a child process, this is just a wrapper for child_precess.spawnSync, we just set the default cwd to your runner's cwd, you can override it on options
runner.spawn("/bin/bash","list")

// There is a result from .installPackages(...), .removePackages(...)
let result = runner.installPackages(["react"])
if(result){
   console.log(result.error)
}

```
```