# ec.codeStyle
> A definitive guide on how we style our code.

We are using [prettier](https://prettier.io/) with [eslint](https://eslint.org/). It's that simple.

1. Get your [node](https://confluence.entrecode.de/display/KB/Node.js+setup) environment up and running.    
    a) Make sure NVM is installed, current version can be found at https://github.com/creationix/nvm#install-script

        curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
    b) Install/Update desired versions

        nvm install 10
        nvm install 12
        nvm alias default 12
        cd ~/.nvm/versions/node
        ln -s v12.x.x default # to make a symlink into default, if you follow 3. your IDE settings will be valid with the new version

2. Install [Visual Studio Code](https://confluence.entrecode.de/display/KB/VSCode+Setup).
3. Install dependencies – run the following on a non-sudo command line:

    ```sh
    npm i -g \
      babel-eslint \
      eslint@^5.16.0 \
      eslint-config-airbnb \
      eslint-config-prettier \
      eslint-plugin-import \
      eslint-plugin-jsx-a11y \
      eslint-plugin-react \
      eslint-plugin-react-hooks@^1.7.0 \
      eslint-plugin-prettier \
      tslint \
      tslint-config-airbnb \
      tslint-config-prettier \
      typescript \
      prettier@~1.15.2 # note the pinned version
    ```
4. ESLint looks for .eslintrc configuration files up to the root folder. This means that you don't need a file in each project repository, but just in your root projects folder (e.g. your entrecode folder). Download the `files/x.eslintrc` file, put it into your projects folder and rename it to .eslintrc (without the x). 
5. Repeat step 4 with the .prettierrc file `files/x.prettierrc`.
6. Repeat step 4 with the tslint config file `files/tslint.json`. No need to rename here.
7. Open Visual Studio Code and install [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint), [TSLint](https://marketplace.visualstudio.com/items?itemName=eg2.tslint), [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode), [Nunjucks](https://marketplace.visualstudio.com/items?itemName=ronnidc.nunjucks), and [Nunjucks Template Converter](https://marketplace.visualstudio.com/items?itemName=okitavera.vscode-nunjucks-formatter). The latter is for Nunjucks files.
8. Open User Settings (cmd + , then navigate "…" + Open settings.json) and add the following settings to your existing settings.
    
    ```json
    {
      "editor.formatOnSave": false,
      "eslint.enable": true,
      "eslint.provideLintTask": true,
      "eslint.run": "onType",
      "editor.detectIndentation": false,
      "editor.tabSize": 2,
      "emmet.includeLanguages": {
        "nunjucks": "html"
      },
      "files.associations": {
        "*.html": "html",
        "*.njk": "nunjucks"
      }
    }
    ```

## Sublime Text

1. Install [JSPrettier](https://packagecontrol.io/packages/JsPrettier) with PackageControl
2. Add the following settings to JsPrettier User Settings (open with cmd + shift + p | JsPrettier Settings - User).

    ```json
    {
      "prettier_cli_path": "/Users/<yourUserName>/.nvm/versions/node/default/bin/prettier",
      "node_path": "/Users/<yourUserName>/.nvm/versions/node/default/bin/node",
      "allow_inline_formatting": true,
      "prettier_options": {
        "printWidth": 120,
        "useTabs": false,
        "tabWidth": 2,
        "semi": true,
        "singleQuote": true,
        "jsxSingleQuote": false,
        "trailingComma": "all",
        "bracketSpacing": true,
        "jsxBracketSameLine": false,
        "arrowParens": "always",
        "proseWrap": "preserve",
        "htmlWhitespaceSensitivity": "css",
        "endOfLine": "lf"
      }
    }
    ```
3. Add the following setting to the Key Bindings arry (open with cmd + shift + p | Preference: Key Bindings).

    ```json
    { "keys": ["super+alt+l"], "command": "js_prettier" }
    ```

## Webstorm

You have two options:

1. *preferred* Switch to Visual Studio Code
2. Apply ESLint Rules with help of the deprecated setup, find IntelliJ prettier integration on prettiers homepage.
