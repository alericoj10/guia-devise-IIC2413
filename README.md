# Autenticación con Devise

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
$ rails generate devise:views
```

## Personalizar Setup de Devise

Con Devise ya instalado, debemos continuar creando nuestro modelo de usuario:
```
$ rails generate devise user
```

Vemos que se creó el modelo, las rutas y un archivo de migración. Podemos editar los atributos del usuario y agregar todos los que queramos directamente en la migración (como `:name` y `:phone`) y concretarlo corriendo:
```
$ rails db:migrate
```

En nuestro archivo `routes.rb` podemos extender la línea de devise, para personalizar las rutas, por ejemplo:
```ruby
devise_for :users, path: '', path_names: {sign_in: 'login', sign_out: 'logout', sign_up: 'register'}
```

## Disponibilzar vistas y renderearlas dinámicamente

En la vista principal (`application.html.erb`) abajo de nuestros flash messages podemos incorporar un pequeño control de sesión:
```ruby
<% if current_user %>
  <%= link_to "Cerrar Sesión", destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to "Iniciar Sesión", new_user_session_path %>
  <%= link_to "Registrarme", new_user_registration_path %>
<% end %>
```

## Permitir nuestros atributos personalizados

En primer lugar debemos modificar los forms de registro (y edición) para incluir los atributos que agregamos anteriormente. En `app/views/devise/registrations/new.html.erb` y `app/views/devise/registrations/edit.html.erb` respectivamente.

Y finalmente, modificamos nuestro `application_controller.rb` para que permita esos nuevos parametros en las requests realizadas por los formularios.

```ruby
class ApplicationController < ActionController::Base
  before_action :configure_permitted_parameters, if: :devise_controller?

  protected

  def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name, :phone])
    devise_parameter_sanitizer.permit(:account_update, keys: [:name, :phone])
  end
end
```