
# Scripting

It is possible to use NodeJS scripts. All scripts before the request line are executed before the request is called. All scripts after the request line are executed as soon as the response is received. All exports of the script are stored as variables.


<<< ./examples/script/script.http

## Access to Variables

All Variables already defined can be accessed via the global scope.

::: warning
Since all variables are placed on the global scope of the script, they may overwrite other variables. Please use unique variable names
:::

In addition to the defined variables, the following values are also set on global scope

| name | description |
| - | - |
| request | request of the next [http request](https://github.com/AnWeber/httpyac/blob/main/src/models/httpRequest.ts) |
| response | [http response](https://github.com/AnWeber/httpyac/blob/main/src/models/httpResponse.ts) of the last request |
| httpRegion | current [httpRegion](https://github.com/AnWeber/httpyac/blob/main/src/models/httpRegion.ts) |
| httpFile | current [httpFile](https://github.com/AnWeber/httpyac/blob/main/src/models/httpFile.ts) |
| test | method to simplify [tests](https://github.com/AnWeber/httpyac/blob/main/src/actions/jsAction.ts#L40) ([assert](https://github.com/AnWeber/httpyac/blob/main/examples/script/assert.http) or [chai](https://github.com/AnWeber/httpyac/blob/main/examples/script/chai.http)) |

## Require

External scripts can be imported using require, but you need to install dependencies yourself.

<<< ./examples/script/scriptRequire.http{3}


::: tip
External dependencies must be installed independently, exceptions are [vscode](https://www.npmjs.com/package/@types/vscode), [got](https://www.npmjs.com/package/got) and [httpYac](https://www.npmjs.com/package/httpyac) Dependency, which are provided from the extension.
:::

::: warning
[NodeJS](https://nodejs.org/api/modules.html#modules_require_cache) caches all loaded scripts. Since in VS Code the script is executed in the context of the extension, the content of the script is not reloaded. Therefore, the script must be manually removed from the cache. A helper method requireUncached is provided.
:::

<<< ./examples/script/scriptRequireUncached.http{3}

## Console

The console cannot be accessed in VS Code. Therefore, a separate Console object is provided in the context of the script that redirects the output to the OutputChannel.

```http
@foo = https://httpbin.org

{{
  console.info(foo);
}}
```

## Test
You can write easily test scripts in JavaScript. Tests allow you to ensure that your API is working as expected, to establish that integrations between services are functioning reliably, and to verify that new developments haven't broken any existing functionality. You can also use test code to aid the debugging process when something goes wrong with your API project.


<<< ./examples/script/assert.http

<<< ./examples/script/chai.http


::: tip
[Auxiliary methods](https://github.com/AnWeber/httpyac/blob/790a1b0409bd9eed6ef0ff26a2fc017952d58231/src/models/testFunction.ts#L6-L14) are provided for standard tests such as Status and Content-Type
:::

<<< ./examples/script/test.http

## Global Scripts

Scripts with no request in the same region are always executed (Global Scripts).

<<< ./examples/script/globalScripts.http

Global Scripts initialized with <code v-pre>{{+</code> are executed before every region.

<<< ./examples/script/globalScriptOnEveryHttpRegion.http{1}

Global Scripts initialized with <code v-pre>{{+after</code> are executed after every region.

<<< ./examples/script/globalScriptOnEveryHttpRegionAfter.http{1}




## Intellij Script

Intellij Scripts are supported. An [Http client](https://www.jetbrains.com/help/idea/http-client-reference.html) and [response](https://www.jetbrains.com/help/idea/http-response-reference.html) object corresponding to the interface is created and are available in the script.

::: warning
The execution environment differs between NodeJS and Intellij (uses Nashorn). Possibly the behavior is not completely identical, to Intellij Execution. If there are problems, please let me know.
:::

<<< ./examples/script/intellij.http

::: warning
Intellij scripts are always executed after request. Scripts before Request Line are ignored
:::


## How to debug scripts

1. Install httpYac cli with `npm install httpyac -g`
1. open Http File in VS Code
3. add [debugger;](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/debugger) statement in script
4. open [Javascript Debug Terminal](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_javascript-debug-terminal) in VS Code
5. execute command `httpyac <file> -l <line>`


<<< ./examples/script/debugger.http{7}