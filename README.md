# 4. Autenticación con Devise

## Lo que lograremos esta ayudantía:

- Instalar gemas nuevas (Devise y bcrypt)
- Incluir autenticación de usuarios en nuestras aplicaciones.
- Crear CRUD de usuarios
- Extender lo que nos ofrece Devise
- Definir y utilizar rutas para la aplicación

## Instalación

Siempre que queramos instalar gemas, es recomendado visitar el [sitio oficial](https://rubygems.org/gems/devise) y la [documentación](https://github.com/heartcombo/devise#the-devise-wiki) respectiva para seguir la instalación recomendad por los creadores.

Antes de comenzar con el desarrollo, recordemos tomar la tarjeta de trello asociada y moverla a "En progreso".
Revisar que estemos en la rama develop, hacer git pull para tener los últimos cambios y crear nueva rama para nuestra funcionalidad:
```
$ git checkout -b autenticacion-devise
```

Ahora si podemos comenzar con el desarrollo. Partimos copiando la siguiente línea en nuestro Gemfile:
```
gem 'devise', '~> 4.7', '>= 4.7.3'
```
y descomentar gema bcrypt que nos servirá para encriptación de contraseñas. Luego, correr: 
```
bundle install
````

Teniendo ya instalada la gema, podemos generar el código en nuestros proyectos que Devise nos proporciona. Para realizar la instalación usamos el comando:
```
$ rails generate devise:install
```
 Nos saldrán 4 instrucciones que debemos seguir:

1. Copiar la siguiente línea para setear la url base:
En `config/environments/development.rb`:
```
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```
En `config/environments/production.rb`:
```
config.action_mailer.default_url_options = { host: 'url-de-app-heroku' }
```

2. Tener una vista en root, es decir que en `routes.rb` hayamos definido una accion `root to: ...`

3. Poner flash messages en application.html.erb
```html
<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

4. Generar vistas de devise, esto incluye todos los forms necesarios para las acciones de autenticación.
```
$ rails generate devise:views users
```

Con Devise ya instalado, debemos continuar creando nuestro modelo de usuario:
```
$ rails generate devise user
```

Vemos que se creó el modelo, las rutas y un archivo de migración. Podemos editar los atributos del usuario directamente en la migración (como `:name` y `:phone`) y concretarlo corriendo:
```
$ rails db:migrate
```

## Personalizar rutas

En nuestro archivo routes podemos extender la línea de devise, para personalizar las rutas, por ejemplo:
```ruby
devise_for :users, path: '', path_names: {sign_in: 'login', sign_out: 'logout', sign_up: 'register'}
```
