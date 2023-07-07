---
layout: post
title:  "VS Code extensions and tricks"
date:   2023-03-30 13:00:00 +0100
categories: ["vs-code"]
---

This page collects usefull VS code extensions and configurations to get productive quickly after installing VS code on another machine.

## Important notes
Where VS Code is installed: see [code.visualstudio.com/docs/setup/uninstall](https://code.visualstudio.com/docs/setup/uninstall) (+ C:\Users\name\AppData\Local on windows user installation, resp. unpacked ZIP folder in zip installation)

### VS Code on WSL 
When using VS Code on WSL store your project files/repo in WSL, not in mounted windows folder for better performance ([link](https://learn.microsoft.com/en-us/windows/wsl/setup/environment#file-storage)) If the repo is in mounted windows folder (like /mnt/c/...) there are some issues with Git:
- git is slower
- issues with symlinks and opening files from source control pane (although this is possibly not related to windows-mounting)
   
### General Structure of VS Code configurations
See [code.visualstudio.com/docs/getstarted/settings](https://code.visualstudio.com/docs/getstarted/settings).
- Basically, there is a global "user" setting.json (ctrl+shift+P: user settings) valid across workspaces and for each workspace a speciffic workspace settings.json (ctrl+shift+P: workspace settings) in .vscode folder in the current workspace.
- The workspace settings overwrite the (global) user settings
- Beside the workspace settings in .vscode folder can be also extensions.json holding recommended extensions (see [code.visualstudio.com/docs/editor/extension-marketplace](https://code.visualstudio.com/docs/editor/extension-marketplace#_workspace-recommended-extensions)) and **launch.json**
- launch.json holds configurations for running/debuging with F5, ctrl+F5, or left pane. However, these options are valid only in workspace. To save these options in global way, the configurations should be specified in user settings.json in configurations:
    ```
  // Global debug launch configuration. Should be used as an alternative to 'launch.json' that is shared across workspaces.
  "launch": {
    "configurations": [{configuration 1 gouse here}, {configuration2 goes here},...],
    "compounds": []
  },
    ```
    How to create the launch configuration:
    - left pane -> Run and Debug -> dropdown on top: Add Configuration
    - this creates configuration in workspace launch.json. However, this can be copied into user launch configurations specified in user settings.json (and of course edited)
    
### Python specific configurations
- Accept debug console sugestions via Enter: ctrl+P -> settings json (workspace or user): add `"debug.console.acceptSuggestionOnEnter": "on"`
- Debuging into library code in python: open launch configuration (or ctrl+shift+P -> Debug: Add configuration): set "justMyCode" to false
- Add current workspace folder to PYTHONPATH for debuging: open launch configuration (or ctrl+shift+P -> Debug: Add configuration) and add:
    ```
    "env": {
                "PYTHONPATH": "${workspaceFolder}"
           }
    ```
- To add current workspace folder for running python with top-right button: to settings.json (user or workspace) add:
    ```
    "terminal.integrated.env.linux": {
        "PYTHONPATH": "${workspaceFolder}"
    }
    ```
- To add current workspace folder for running in python interactive window (top-right button, jupyter extension needed):
    - make sure to have `"python.envFile": "${workspaceFolder}/.env"` in settings.json (workspace, or user but not overwritten)
    - create .env file in workspace with the following entry: `PYTHONPATH="/PATH/TO/YOUR/WORKSPACE"` (this file defines enviromental variables)
 
### Bugs
- Visual studio keeps downgrading and upgrading back (windows user installation): install zip version of VS Code (this needs to be update manually, probably via redownlaoding newer version)

## Extensions
- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) English spelling checker, suggestions on hower or via ctrl+.

- [Codeium](https://marketplace.visualstudio.com/items?itemName=Codeium.codeium): AI Coding Autocomplete and Chat for Python, Javascript, Typescript, Java, Go, and more

- [Databricks](https://marketplace.visualstudio.com/items?itemName=databricks.databricks) Run code on Databricks cluster. **Warning: at least on WSL it uses too much CPU now. Consider disabling and enabling only when needed**

- [Databricks Driver for SQLTools](https://marketplace.visualstudio.com/items?itemName=databricks.sqltools-databricks-driver) run SQL queries against Databricks. **Warning: at least on WSL it uses too much CPU now. Consider disabling and enabling only when needed.**

- [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph): Visualizes your git history.

- [Git Extension Pack](https://marketplace.visualstudio.com/items?itemName=donjayamanne.git-extension-pack): package of good git extensions

- [Markdown+Math](https://marketplace.visualstudio.com/items?itemName=goessner.mdmath): The only extension I found which renders nicely Markdown+Katex into HTML. Unfortunatelly not into PDF, the best way to convert to PDF is to open the reulting PDF in browser and print to PDF (as stated [here](https://github.com/yzane/vscode-markdown-pdf/issues/259))
  
- [WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl): Work in VS code under WSL. Use native linux terminal, git, python/conda installed under WSL etc.

