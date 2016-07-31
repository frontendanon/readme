# Файловая структура

[На основе БЭМ организация файловой системы](https://ru.bem.info/methodology/filesystem/)

```
project
├── build
│   ├── css
│   ├── img
│   ├── js
│   └── index.html
├── dev
│   ├── css
│   ├── img
│   ├── js
│   └── index.html
├── node_modules
├── src
│   ├── blocks
│   │   ├── common
│   │   ├── header
│   │   │   ├── header.pug
│   │   │   ├── header.styl
│   │   │   ├── header.js
│   │   │   └── header_bg.jpg
│   │   ├── ...
│   │   └── footer
│   │       ├── footer.pug
│   │       ├── footer.styl
│   │       └── footer.js
│   ├── index.pug
│   └── index.styl
├── .gitignore
├── gulpfile.js
├── package.json
└── README.md
```