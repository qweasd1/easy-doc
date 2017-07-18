# easy-strings
easy-strings provide string related utility methods to help you do scaffolding work easily

## Installation
```bash
// yarn
yarn add easy-strings

// npm
npm install easy-strings
```

then import the EasyStrings methods
```javascript
const EasyStrings =  require('easy-strings');
```

## Methods

* capitalize(str): turn string into capitalized
  * ```str:string```: text to be capitalized
* basename(filename): get the basename of given filename, e.g. ```EasyStrings.basename("test.js") === "test"```
  * ```filename:string```: filename
* ext(filename): get the extension of given filename
  * ```filename:string```: filename
* snakeToCamel(str,snakeChar="-") : turn snake str into CamelCase, e.g. ```EasyStrings.snakeToCamel("test-component") === "testComponent"```
  * ```str:string```: text to be change
  * ```snakeChar:string```: char for the original snake name, default is '-'
* snakeToCapitalize(str,snakeChar="-"") turn snake str into Capitalized, e.g. ```EasyStrings.snakeToCapitalize("test-component") === "TestComponent"```
  * ```str:string```: text to be change
  * ```snakeChar:string```: char for the original snake name, default is '-'
* camelToSnake(str,snakeChar="-"") turn CamelCase str into SnakeCase, e.g. ```EasyStrings.camelToSnake("testComponent") === "test-component"```
  * ```str:string```: text to be change
  * ```snakeChar:string```: char for the transformed snake name, default is '-'
* camelToCapitalize(str) turn CamelCase str into Capitalized, e.g. ```EasyStrings.camelToCapitalize("testComponent") === "TestComponent"```
  * ```str:string```: text to be change
* capitalizeToCamel(str) turn Capitalized str into CamelCase, e.g. ```EasyStrings.capitalizeToCamel("TestComponent") === "testComponent"```
  * ```str:string```: text to be change
* capitalizeToSnake(str,snakeChar="-"") turn Capitalized str into SnakeCase, e.g. ```EasyStrings.camelToSnake("TestComponent") === "test-component"```
  * ```str:string```: text to be change
  * ```snakeChar:string```: char for the transformed snake name, default is '-'
* toConst(str, type = "camel",snakeChar="-"): turn from other name convention types(Capitalized, CamelCase, SnakeCase) to const name, e.g. ```Easy.Strings.toConst("filePath") === "FILE_PATH""```
  * ```str:string```: text to be change
  * ```type:string```: original name convention, can be "camel", "cap", "snake"
  * ```snakeChar:string```: if the type is "snake", you can provide its snake char
* changeExt(filename,newExt): change the file's extension to new extension
  * ```filename:string```: filename
  * ```newExt:string```: new extension name 