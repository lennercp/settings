## keybinds
```json
[
    {
        "key": "ctrl+o",
        "command": "workbench.action.files.openFolder",
        "when": "openFolderWorkspaceSupport"
    },
    {
        "key": "ctrl+shift+o",
        "command": "workbench.action.files.openFile",
        "when": "true"
    },
    {
        "key": "ctrl+alt+down",
        "command": "editor.action.insertCursorBelow",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "ctrl+alt+up",
        "command": "editor.action.insertCursorAbove",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "shift+alt+down",
        "command": "editor.action.copyLinesDownAction",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "shift+alt+up",
        "command": "editor.action.copyLinesUpAction",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "ctrl+/",
        "command": "editor.action.commentLine",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "ctrl+/",
        "command": "editor.action.commentLine",
        "when": "editorTextFocus && !editorReadonly"
    }
    ,{
        "key": "ctrl+;",
        "command": "editor.action.commentLine",
        "when": "editorTextFocus && !editorReadonly"
    }
]
```

## settings
```json
    {
    "workbench.iconTheme": "material-icon-theme",
    "material-icon-theme.activeIconPack": "nest",
    "[prisma]": {
        "editor.formatOnSave": true
    },
    "explorer.compactFolders": false,
    "tabnine.experimentalAutoImports": true,
    "explorer.confirmDelete": false,
    "editor.bracketPairColorization.enabled": true,
    "editor.guides.bracketPairs": "active",
    "editor.detectIndentation": true,
    "editor.wordBasedSuggestions": false,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
        "source.fixAll": true
    },
    "javascript.preferences.quoteStyle": "single",
    "typescript.preferences.quoteStyle": "single",
    "javascript.validate.enable": false,
    "javascript.updateImportsOnFileMove.enabled": "always",
    "typescript.updateImportsOnFileMove.enabled": "always",
    "javascript.suggest.autoImports": true,
    "typescript.suggest.autoImports": true,
    "[javascript]": {
        "editor.suggestSelection": "recentlyUsed",
        "editor.suggest.showKeywords": false,
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "python.languageServer": "Jedi", // ms-python.vscode-pylance
    "python.testing.unittestEnabled": false, // ms-python.python
    "python.testing.pytestEnabled": true,
    "python.testing.pytestArgs": [],
    "python.linting.mypyEnabled": true,
    "python.linting.pylintArgs": [
        "--load-plugins=pylint_django",
        "--errors-only",
        "--enable=W0611"
    ],
    // "python.formatting.autopep8Args": [
    //     "--indent-size=4"
    // ],
    // "python.formatting.provider": "black",
    // "python.formatting.blackArgs": ["--line-length", "40"],
    // "python.defaultInterpreterPath": "C:\\python311/python.exe",
    // "python.formatting.provider": "none",
    "[python]": {
        "editor.defaultFormatter": "ms-python.python",
        "editor.formatOnSave": true
    },
    "[html]": {
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "vscode.html-language-features",
        "editor.quickSuggestions": {
            "other": true,
            "comments": true,
            "strings": true
        }
    },
    "[django-html]": {
        "editor.formatOnSave": false,
        "editor.defaultFormatter": "vscode.html-language-features",
        "editor.quickSuggestions": {
            "other": true,
            "comments": true,
            "strings": true
        }
    },
    "files.associations": {
        "*.js": "javascript",
        "*.jsx": "javascriptreact",
        "*.xml": "html",
        "*.svg": "html",
        "*.html": "html",
        "django-html": "django-html",
        "**/*.html": "django-html",
        "**/templates/**/*.html": "django-html",
        "**/base_templates/**/*.html": "django-html",
        "**/requirements{/**,*}.{txt,in}": "pip-requirements",
        "*.css": "css"
    },
    "emmet.includeLanguages": {
        "django-html": "html", // batisteo.vscode-django
        "javascript": "javascriptreact",
        "typescript": "typescriptreact"
    },
    "terminal.integrated.enableMultiLinePasteWarning": false,
    "git.confirmSync": false,
    "git.enableSmartCommit": true,
    "django.snippets.exclude": [
        "cms",
        "wagtail"
    ],
    "python.analysis.importFormat": "relative",
    "security.workspace.trust.untrustedFiles": "open",
    "gitlens.advanced.messages": {
        "suppressCommitNotFoundWarning": true
    },
    "[javascriptreact]": {
        "editor.defaultFormatter": "vscode.typescript-language-features"
    },
    "editor.minimap.enabled": false,
    "editor.formatOnSaveMode": "modifications",
    "explorer.confirmDragAndDrop": false,
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "workbench.colorTheme": "OM Theme (Default Dracula Italic)",
    "terminal.integrated.profiles.windows": {
        "Git Bash": {
            "source": "Git Bash"
        }
    },
    "terminal.integrated.defaultProfile.windows": "Windows PowerShell",
    "editor.formatOnSave": true
}
```