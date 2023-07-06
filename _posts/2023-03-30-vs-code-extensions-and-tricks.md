---
layout: post
title:  "VS Code extensions and tricks"
date:   2023-03-30 13:00:00 +0100
categories: ["vs-code"]
---

This page collects usefull VS code extensions and configurations to get productive quickly after installing VS code on another machine.

## Important notes
- **VS Code on WSL**: when using VS Code on WSL store your project files/repo in WSL, not in mounted windows folder for better performance ([link](https://learn.microsoft.com/en-us/windows/wsl/setup/environment#file-storage))
  - when using repo in mounted windows floder (like /mnt/c/...) there are some issues with Git:
    - slower git
    - issues with symlinks and opening files from source control pane (although this is possibly not related to windows-mounting)
   
- **Configure python debuging**
  - Accept debug console sugestions via Enter: ctrl+P -> settings json: add `"debug.console.acceptSuggestionOnEnter": "on"`
  - Debuging into library code in python: ctrl+shift+P -> Debug: Add configuration: set "justMyCode" to false
  - Add current workspace folder to PYTHONPATH for debuging:  ctrl+shift+P -> Debug: Add configuration:
    ```
    "env": {
                "PYTHONPATH": "${workspaceFolder}"
           }
    ```


## Extensions
- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) English spelling checker, suggestions on hower or via ctrl+.

- [Codeium](https://marketplace.visualstudio.com/items?itemName=Codeium.codeium): AI Coding Autocomplete and Chat for Python, Javascript, Typescript, Java, Go, and more

- [Databricks](https://marketplace.visualstudio.com/items?itemName=databricks.databricks) Run code on Databricks cluster. **Warning: at least on WSL it uses too much CPU now. Consider disabling and enabling only when needed**

- [Databricks Driver for SQLTools](https://marketplace.visualstudio.com/items?itemName=databricks.sqltools-databricks-driver) run SQL queries against Databricks. **Warning: at least on WSL it uses too much CPU now. Consider disabling and enabling only when needed.**

- [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph): Visualizes your git history.

- [Git Extension Pack](https://marketplace.visualstudio.com/items?itemName=donjayamanne.git-extension-pack): package of good git extensions

- [Markdown+Math](https://marketplace.visualstudio.com/items?itemName=goessner.mdmath): The only extension I found which renders nicely Markdown+Katex into HTML. Unfortunatelly not into PDF, the best way to convert to PDF is to open the reulting PDF in browser and print to PDF (as stated [here](https://github.com/yzane/vscode-markdown-pdf/issues/259))
  
- [WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl): Work in VS code under WSL. Use native linux terminal, git, python/conda installed under WSL etc.

