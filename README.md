# Introduction

### Why another scaffolding tool framework? (summarize the main bad aspects of current scaffolding tool)
There are already many scaffolding tools for current web development, why bother write another?
The reason is simple, the existing one is not good enough. 
For general one like yeoman, you will notice it has too many dependencies which make it slow to download from npm. 
The programming model is also not user friendly.
 
For web framework specific ones like angular-cli, you will find it's not easy to extend and customize it.

both yeoman and angular-cli like command-line tool doesn't provide very good code-completion especially when you call them from command-line. 
They are also very slow because each time you run them from command-line, they need to setup the same environment again which is time consuming.


### What does a scaffolding framework really needs?(show some concrete samples here)
* easy CRUD operations over files
    * 
* support file change monitor
* support modular development
* easy to extend, customize, tune and integrated(with other ide or framework)
* contains highly used utilities
* has less dependencies, fast to download
* transparent, easy to learn
* [optional] has an command-line interface support rich code-completion

_______





