# Guía docente Pt2.2 Nextcloud

## Finalidad de la práctica

Esta actividad no busca que el alumnado despliegue un servicio de producción completo. Busca que entienda que un servicio de archivos web no es una carpeta mágica en Internet: es una aplicación instalada en un servidor, con usuarios, permisos, una base de datos y almacenamiento.

Al finalizar deberían poder explicar:

- La diferencia entre Drive o Dropbox y un Nextcloud autogestionado.
- La función de Apache, PHP, Nextcloud, MariaDB y la carpeta de datos.
- Por qué una cuenta, un grupo y un permiso no son lo mismo.
- Por qué una carpeta con información sensible no se comparte igual que un cartel.
- Por qué una práctica local no equivale a un servicio publicado y seguro.

## Modelo mental para explicar en clase

~~~text
Navegador o cliente de escritorio
             ↓ petición HTTP o HTTPS
Apache
             ↓ entrega la petición a PHP
PHP ejecuta Nextcloud
             ↓ consulta o guarda información
MariaDB  ←→  carpeta de datos
             ↓
respuesta al navegador o sincronización al cliente
~~~

- **Apache** es el servidor web. Recibe peticiones HTTP o HTTPS y sirve páginas, imágenes y archivos.
- **PHP** no es otro ordenador ni otro servidor físico. Es el lenguaje y entorno que ejecuta el código de Nextcloud dentro del servidor web.
- **Nextcloud** es la aplicación web. Comprueba la sesión y los permisos, muestra pantallas y coordina datos y archivos.
- **MariaDB** es la base de datos. Guarda usuarios, grupos, permisos, nombres y metadatos de archivos, enlaces compartidos y ajustes.
- **La carpeta de datos** guarda los archivos físicos: carteles, programas, documentos y otros recursos.
- **El cliente de sincronización** se comunica con Nextcloud mediante WebDAV; no copia directamente al disco del servidor.

Frase útil: MariaDB sabe qué existe y quién puede verlo; la carpeta de datos guarda el contenido del archivo.

## Diferencia con Drive y Dropbox

En Drive y Dropbox el alumnado usa un servicio ya desplegado. Google o Dropbox mantiene la aplicación, la base de datos, los servidores, las actualizaciones y las copias de seguridad.

En Nextcloud esas piezas siguen existiendo, pero alguien debe administrarlas. En esta práctica, esa persona es cada alumno o alumna en una instalación local. El aprendizaje no consiste solo en subir archivos: consiste en comprender la responsabilidad de administrar el servicio.

## Alcance de Pt2.2

La práctica usa Ubuntu, Apache, PHP, MariaDB y Nextcloud en localhost.

Se trabaja:

- Instalación de paquetes y servicios.
- Base de datos y usuario de base de datos exclusivo.
- Instalación de Nextcloud con el asistente web.
- Usuarios, grupos, carpetas y permisos.
- Historial de versiones, enlaces protegidos y sincronización.
- Tareas o calendario.

No se pide todavía:

- Publicar Nextcloud en Internet.
- Usar datos personales reales.
- Configurar correo, videollamadas o apps de terceros.
- Instalar certificados HTTPS.
- Ajustar Redis, cron, almacenamiento externo o balanceo de carga.
- Convertir Nextcloud en alojamiento de la web HTML del evento.

La web pública del evento se publicará en Pt2.3. Un enlace público de Nextcloud sirve para compartir un archivo o carpeta; no es hosting web.

## Preparación antes de la clase

1. Decide el entorno: Ubuntu individual, máquina virtual o equipo de aula.
2. Comprueba red, espacio en disco y permisos de sudo.
3. Usa siempre usuarios, contraseñas y archivos ficticios.
4. Prueba la instalación completa con la misma versión de Ubuntu que usará el alumnado.
5. Conserva una copia de la versión de Nextcloud que hayas probado. El archivo latest.zip cambia con el tiempo; para una clase repetible, fija una versión que hayas comprobado.
6. No permitas que se publiquen capturas con contraseñas, enlaces completos o datos personales.

## Por qué la instalación se hace así

La secuencia de la página es correcta:

1. Instalar Apache, MariaDB, PHP y extensiones.
2. Activar Apache y MariaDB.
3. Crear la base de datos nextcloud y un usuario con permisos solo sobre ella.
4. Colocar Nextcloud en /var/www/nextcloud.
5. Dar propiedad al usuario www-data, que normalmente usa Apache en Ubuntu.
6. Crear /var/ncdata para los archivos.
7. Activar reglas y módulos de Apache.
8. Completar el asistente web.

El alumnado crea una base de datos vacía y un usuario exclusivo con acceso a ella. El asistente de Nextcloud crea automáticamente sus tablas y su estructura interna: no se crean tablas una a una ni se ejecuta CREATE TABLE. Durante el asistente se indican el nombre de base de datos, usuario, contraseña y carpeta de datos.

MariaDB es una elección adecuada para una práctica con varios usuarios y sincronización. SQLite puede servir para una prueba mínima, pero no es apropiada para este escenario colaborativo.

## Comprobaciones mínimas

### Instalación

- http://localhost responde.
- El alumnado explica: Apache recibe peticiones, PHP ejecuta Nextcloud y MariaDB guarda datos de la aplicación.
- http://localhost/nextcloud muestra el inicio de sesión.
- El administrador puede entrar.
- La carpeta de datos es /var/ncdata.

### Organización

- Existen los grupos Organización y Comunicación.
- Las carpetas se comparten con grupos, no con enlaces públicos.
- Hay una prueba de permiso permitido y otra denegada.
- Inscripciones solo puede abrirla Organización.

### Colaboración

- Se ha restaurado una versión anterior.
- El enlace público tiene contraseña y fecha de caducidad.
- El cliente sincroniza únicamente Evento.
- Hay tres tareas o eventos coherentes con el evento.

## Problemas frecuentes

| Síntoma | Primera comprobación | Explicación |
|---|---|---|
| http://localhost no abre | sudo systemctl status apache2 | Apache no está iniciado o su instalación falló. |
| El asistente no conecta con MariaDB | Revisa base, usuario, contraseña y php-mysql | La aplicación necesita credenciales válidas y el conector de PHP. |
| Error de permisos | Revisa propietario de /var/www/nextcloud y /var/ncdata | Apache debe poder leer y escribir donde corresponde. |
| Aviso de dominio no confiable | Revisa trusted_domains | Nextcloud bloquea direcciones no declaradas para prevenir ataques de cabecera Host. |
| El cliente no sincroniza | Comprueba URL, cuenta, red y permisos | La sincronización depende de WebDAV y del acceso a la carpeta. |
| Dos personas se pisan cambios | Usa historial y coordinación | El bloqueo evita problemas al guardar, pero no fusiona dos ediciones de un mismo documento. |

## Qué significa que el servicio sea local

Con localhost, navegador y Nextcloud están en la misma máquina. Es un contexto de aprendizaje seguro: reduce la complejidad de red y evita exponer servicios del alumnado.

Si se accede desde otro equipo de la red hay que:

1. Usar la IP o nombre de red del servidor.
2. Añadir esa dirección a trusted_domains.
3. Revisar red y cortafuegos.
4. Usar HTTPS si se transmiten credenciales o datos fuera de un entorno de pruebas cerrado.

No es recomendable hacer pública la máquina de un alumno.

## Qué requeriría un servicio real

- **HTTPS** para proteger credenciales y contenido en tránsito.
- **Actualizaciones** de Ubuntu, Apache, PHP, MariaDB, Nextcloud y sus apps.
- **Copias de seguridad verificadas** de base de datos, carpeta de datos y configuración.
- **Tareas en segundo plano**: cron es el método recomendado para instancias con varios usuarios o uso continuado.
- **Supervisión de avisos** sobre seguridad, WebDAV, configuración y rendimiento.
- **Rendimiento**: con más carga puede ser necesario Redis o Valkey para bloqueo de archivos.
- **Privacidad y roles**: usuarios individuales, contraseñas seguras, permisos por grupos y datos mínimos.

No añadas Redis, cron o HTTPS como requisito de la primera entrega. Preséntalos como la capa que transforma una prueba local en un servicio fiable.

## Copias de seguridad

Una copia útil conserva:

1. La carpeta de datos.
2. La base de datos.
3. La configuración de Nextcloud, especialmente config.php.
4. Las aplicaciones o temas personalizados si se utilizan.

Antes de actualizar o restaurar, se recomienda activar modo de mantenimiento y comprobar que la copia puede restaurarse. Para esta práctica basta con que identifiquen los tres elementos principales.

## Evaluación

La rúbrica de Pt2.2 valora sobre 10:

- Instalación del servicio: 2 puntos.
- Base de datos y configuración: 1,5 puntos.
- Usuarios, grupos y permisos: 2 puntos.
- Versiones y enlace seguro: 1,5 puntos.
- Sincronización y tareas: 1,5 puntos.
- Documento de evidencias y conclusión: 1,5 puntos.

Si una captura no demuestra con claridad la acción, pide una demostración oral breve.

Preguntas útiles:

- ¿Qué información guardaría MariaDB y qué se guardaría en la carpeta de datos?
- ¿Por qué Comunicación no debe poder consultar Inscripciones?
- ¿Para qué sirve el historial de versiones?
- ¿Por qué un enlace con contraseña y caducidad es más seguro?
- ¿Por qué Nextcloud no reemplaza el alojamiento web de Dotació.cat?

## Fuentes oficiales

- [Requisitos del sistema](https://docs.nextcloud.com/server/stable/admin_manual/installation/system_requirements.html)
- [Avisos de administración y seguridad](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/security_setup_warnings.html)
- [Tareas en segundo plano y cron](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/background_jobs_configuration.html)
- [Bloqueo transaccional de archivos](https://docs.nextcloud.com/server/stable/admin_manual/configuration_files/files_locking_transactional.html)
- [Copias de seguridad](https://docs.nextcloud.com/server/stable/admin_manual/maintenance/backup.html)
- [Configuración y dominios de confianza](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/config_sample_php_parameters.html)
