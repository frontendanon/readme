# Описание файлов и папок

**`build`**
> В папку build компилируются минифицированные файлы css, html, картинки, которые будут лежать на сервере

**`dev`** 
>  В папку dev копируются картинки, компилируются спрайты, файлы pug, stylus во время разработки

**`src`**
> В папке src лежат исходники pug, stylus, спрайтов, картинок
>> **`blocks`**
>
>> В папке blocks содержатся блоки сайта, например header, gallery, post, footer со всеми файлами принадлежащими этому блоку js, pug, styl, png, jpg
>
>>> Папка common содержит файлы блоков, которые могут применяться многократно на странице, например, title, description, image, grid, sprite
>
>> **`index.pug`**
>
>>> Основной pug файл только в него можно `include` другие pug файлы
>>>Пример
>>>
>>>```
>>><!DOCTYPE html>
>>>html
>>>  head
>>>    link(rel="stylesheet", href="css/main.css")
>>>    meta(charset="UTF-8")
>>>    meta(name="viewport" content="width=device-width,initial-scale=1")
>>>    title Title page
>>>  body.page
>>>    include blocks/header/header.pug
>>>    ...
>>>    include blocks/text/text.pug
>>>    script(src="js/main.js")
>>>```
>
>> **`index.styl`**
>
>>> Основной stylus файл только в него можно `@import` другие styl файлы
>>>Пример
>>>```
>>>@import "blocks/common/var.styl"
>>>@import "blocks/common/sprite.styl"
>>>@import "blocks/header/header.styl"
>>>```

**`.gitignore`**
> Файл для игнорирования гитом

**`gulpfile.js`**
> Файл разработки, содержит таски для разработки и билда

**`package.json`**
>  npm зависимости проекта

**`README.md`**
> Описание проекта