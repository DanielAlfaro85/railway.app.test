## Link de la pagina `https://morning-fortress-75258.herokuapp.com`

### ¿Cómo instalar Heroku?

Primero tenemos que ir a nuestra maquina virtual de Debian e ingresar el comando `curl https://cli-assets.heroku.com/install-ubuntu.sh | sh`.
Esto nos instalará Heroku.

Para confirmar que la instalación fue exitosa, debemos ingresar el comando `heroku -v`.

### Crear la aplicación con laravel

Para esto ingresamos `composer create-project laravel/laravel heroku.isw811.xyz` y nos creará la aplicación.

Despues configuramos nuestro host virtual para que nos permita acceder a la aplicación desde nuestra maquina virtual.
```config/hosts
<VirtualHost *:80>
  ServerAdmin webmaster@heroku.isw811.xyz
  ServerName heroku.isw811.xyz
  ServerAlias www.heroku.isw811.xyz
  

  DirectoryIndex index.php
  DocumentRoot /home/vagrant/sites/heroku.isw811.xyz/public


  <Directory /home/vagrant/sites/heroku.isw811.xyz/public>
  DirectoryIndex index.php
  AllowOverride All
  Require all granted
</Directory>

 ErrorLog ${APACHE_LOG_DIR}/heroku.isw811.xyz.log
 CustomLog ${APACHE_LOG_DIR}/heroku.isw811.xyz_access.log combined
</VirtualHost>
```
Y realizamos la copia a la carpeta `/etc/apache2/sites-available/` con el comando `sudo cp heroku.isw811.xyz.conf /etc/apache2/sites-available/`, luego activamos la configuración con `sudo a2ensite heroku.isw811.xyz`, reiniciamos el servicio con `sudo systemctl reload apache2` y por ultimo verificamos su estado con `sudo systemctl -t`.

Luego nos vamos a nuestra maquina local y configuramos nuestros dominios para que logren funcionar.

### Configurar nuestra aplicación con heroku

Ahora si en nuestra maquina local nos dirigimos a nuestro repositorio y lo inicializamos con `git init` y despues hacemos `git add .` para agregar los cambios que realizamos y `git commit -m "inicialización"` para guardar los cambios.

Creamos un archivo Procfile con el comando `echo "web: vendor/bin/heroku-php-apache2 public/" > Procfile`

Ahora si en nuestro server virtual ingresamos `heroku login -i` para iniciar sesion con nuestra cuenta de heroku y escribimos `heroku create` y hacemos un `git push heroku master` para crear la aplicación en heroku.

Para hacer que nuestra pagina laravel funcione correctamente debemos ingresar el comando `cat .env | grep APP_KEY=base64`, despues de esto nos mostrara una clave aleatoria que debemos copiar y utilizar `heroku config:set APP_KEY=<la clave>` para configurarla y ya con eso estaria funciando.

### Conectar nuestra aplicación a una base de datos

Antes de hacer la conexion vamos a hacer un inicio de sesion y un registro para nuestra pagina, para esto podemos utilizar el comando `composer require laravel/ui` que nos creara una carpeta `vendor/laravel/ui` y nos permitira hacer el registro y el inicio de sesion.

Ahora ingresamos el comando `php artisan ui boostrap --auth` que nos cree las vistas de nuestra aplicación y nos permitira hacer el registro y el inicio de sesion. Despues ingresamos el comando npm install && npm run dev para nos permitir hacer el inicio de sesion y registro.

Antes de subir los cambios nos vamos a donde estan nuestras rutas en laravel en el archivo `routes/web.php` y forzamos el protocolo https con el siguiente codigo para que cargue nuestras vistas bien.

```php
if (env('APP_ENV') === 'production') {
    URL::forceScheme('https');
}
```

Despues hacemos `git add .` para agregar los cambios que realizamos y `git commit -m "Commit"` para guardar los cambios. Y tambien hacemos un `heroku config:set APP_ENV=production` para que la aplicación se ejecute en produccion y hacemos un `git push heroku master` para subir los cambios a heroku.

Despues nos vamos a la pagina de heroku para agregar la base de datos, nos vamos a addons y seleccionamos la base de datos que queremos utilizar. Tambien la podemos agregar usando el comando `heroku addons:create heroku-postgresql:hobby-dev` y nos vamos a la pagina de heroku para configurar la base de datos.

Despues nos vamos al archivo database.php y agregamos el siguiente codigo.
```php
$url = parse_url(getenv("DATABASE_URL"));
```	

Luego en el mismo archivo , en el apartado de connections se agrega el siguiente arreglo

```php
 'pgsql-hobby-dev' => [
 'driver' => 'pgsql',
 'url' => env('DATABASE_URL'),
 'host' => env('DB_HOST', $url["host"]),
 'port' => env('DB_PORT', $url["port"]),
 'database' => env('DB_DATABASE', substr($url["path"],1)),
 'username' => env('DB_USERNAME', $url["user"]),
 'password' => env('DB_PASSWORD', $url["pass"]),
 'charset' => 'utf8',
 'prefix' => '',
 'prefix_indexes' => true,
 'schema' => 'public',
 'sslmode' => 'prefer',
 ],
```
Despues hacemos `git add .` para agregar los cambios que realizamos y `git commit -m "Commit"` para guardar los cambios. Y tambien hacemos un `heroku config:set DB_CONNECTION=pgsql-hobby-dev` para que la aplicación se ejecute en produccion y hacemos un `git push heroku master` para subir los cambios a heroku.

Para que ahora si funcione por completo nuestro registro y inicio de sesion de usuario debemos ejecutar las migraciones con el comando `heroku run php artisan migrate` y ya con esto tendriamos nuestra autenticacion y registro funcionando.





