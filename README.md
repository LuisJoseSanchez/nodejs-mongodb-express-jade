# Acceso a una BBDD MongoDB desde NodeJS con Express y Jade (Pug)

Si tienes poca o nula experiencia con **MongoDB** y/o **NodeJS**, te recomiendo echarle un vistazo a los siguientes repositorios antes de seguir:

* [MongoDB - Instalación y primeros pasos](https://github.com/LuisJoseSanchez/mongodb)
* [Iniciación a NodeJS con ejemplos básicos](https://github.com/LuisJoseSanchez/nodejs-iniciacion)
* [NodeJS, Express y Jade (Pug). Ejemplos básicos.](https://github.com/LuisJoseSanchez/nodejs-express-jade)

Acceso a una base de datos **MongoDB** desde **NodeJS** usando el framework **Express** y el motor de plantillas **Jade (Pug)**. Se muestra cómo hacer una lectura de datos - un listado - y una escritura - inserción de datos recogidos en un formulario.

La base de datos que se emplea en los ejemplos de este repositorio es la utilizada en el repositorio [MongoDB - Instalación y primeros pasos](https://github.com/LuisJoseSanchez/mongodb).

Para importar a **MongoDB** la base de datos, primero clona este repositorio:

```console
git clone https://github.com/LuisJoseSanchez/nodejs-mongodb-express-jade.git
```

Entra dentro del repositorio:

```console
cd nodejs-mongodb-express-jade
```

Y por último, restaura la base de datos de MongoDB:

```console
mongorestore -d gestion dump/gestion
```

## Creación del proyecto

```console
mkdir mongodb-minicrud
cd mongodb-minicrud/
npm init
npm install express --save
npm install jade --save
npm install mongodb --save
```

## Listado

Cre el fichero `index.js`:

```javascript
var express = require('express');
var mongodb = require('mongodb')

var app = express();

app.set('view engine', 'jade');

var dbUrl = 'mongodb://localhost:27017/gestion';

app.get('/listado', function (req, res) {
  mongodb.connect(dbUrl, function(err, db) {
    db.collection('usuarios').find().toArray(function(err, docs) {
      datos.usuarios = docs;
      res.render('listado', datos);
    });
  });
});

app.listen(8080);
```

Crea el fichero `base.jade` dentro de la carpeta `views`. Este fichero será la plantilla base.

Hemos utilizado **MaterializeCSS** para los estilos.

```jade
<!DOCTYPE html>
html(lang="es")
head
  meta(charset="UTF-8")
  title Mi página en Node + Express + Jade

  // Compiled and minified CSS
  link(rel="stylesheet", href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.98.0/css/materialize.min.css")

  // Compiled and minified JavaScript
  script(src="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.98.0/js/materialize.min.js")
body
  block contenido
  hr
  footer(class="center") &copy; Luis José Sánchez
```

Crea el fichero `listado.jade` que será el que muestre los datos que le envía `index.js`.

```jade
extends ./base.jade
block contenido
  div(class="container")
    h1(class="center blue-text") Listado de usuarios
    table(class="striped highlight")
      tr
        th Nombre
        th Apellido
        th Edad
        th Pais
      for u in usuarios
        tr
          td #{u.nombre}
          td #{u.apellido}
          td #{u.edad}
          td #{u.pais}
```

Si te ha resultado útil este repositorio :wrench: recuerda que puedes ponerle una estrellita :star:
