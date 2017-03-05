# Acceso a una BBDD MongoDB desde NodeJS con Express y Jade (Pug)

Acceso a una base de datos **MongoDB** desde **NodeJS** usando el framework **Express** y el motor de plantillas **Jade (Pug)**. Se muestra cómo hacer una lectura de datos - un listado - y una escritura - inserción de datos recogidos en un formulario.

Si tienes poca o nula experiencia con **MongoDB** y/o **NodeJS**, te recomiendo echarle un vistazo a los siguientes repositorios antes de seguir:

* [MongoDB - Instalación y primeros pasos](https://github.com/LuisJoseSanchez/mongodb)
* [Iniciación a NodeJS con ejemplos básicos](https://github.com/LuisJoseSanchez/nodejs-iniciacion)
* [NodeJS, Express y Jade (Pug). Ejemplos básicos.](https://github.com/LuisJoseSanchez/nodejs-express-jade)

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

Accederemos al listado mediante la URL <http://localhost:8080/listado>

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

## Grabación de datos

Ya tenemos un listado disponible en <http://localhost:8080/listado>. Vamos a implementar ahora la inserción de datos en la base de datos `gestion` de **MongoDB**.

Modificamos el fichero `index.js`:

```javascript
var express = require('express');
var mongodb = require('mongodb')
var bodyParser = require('body-parser');

var app = express();

app.set('view engine', 'jade');

app.use(bodyParser.urlencoded({extended: true}));

var dbUrl = 'mongodb://localhost:27017/gestion';

app.get('/formulario', function (req, res) {
  res.render('formulario');
});

app.post('/graba-usuario', function (req, res) {
  let usuario = {
    nombre: req.body.nombre,
    apellido: req.body.apellido,
    edad: req.body.edad,
    pais: req.body.pais
  };

  console.log(JSON.stringify(usuario));

  mongodb.connect(dbUrl, function(err, db) {
    db.collection('usuarios').insert(usuario);
  });
  
  res.render('graba-usuario');
})

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

Crea el fichero `formulario.jade` que será el formulario que recoga los datos que se van a grabar: nombre, apellido, edad y pais.

```jade
extends ./base.jade
block contenido

  div(class="container")
    div(class="row")
      h1 Registro de usuarios

    form(action="/graba-usuario", method="post")
      div(class="row")

        div(class="col s12 m6 l6")
        label(for="nombre") Usuario
        input(type="text", name="nombre", placeholder="Nombre" id="nombre")

        div(class="col s12 m6 l6")
        label(for="apellido") Apellido
        input(type="text", name="apellido", placeholder="Apellido" id="apellido")

        label(for="edad") Edad
        input(type="tenumber", name="edad", placeholder="Edad" id="edad")

        label(for="pais") Pais
        input(type="text", name="pais", placeholder="Pais" id="pais")

      div(class="row")
        div(class="col s12 center")
          input(type="submit", value="Aceptar")
```

Por último, el fichero `graba-usuario.jade` indicará que los datos se han guardado correctamente.

```jade
extends ./base.jade
block contenido
  div(class="container")
    p Usuario guardado correctamente.
```

Para introducir nuevos datos, puedes ir a <http://localhost:8080/formulario> y para ver un listado con todos los usuarios a <http://localhost:8080/listado>.

Si te ha resultado útil este repositorio :wrench: recuerda que puedes ponerle una estrellita :star:
