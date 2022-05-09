# Asegurando su Sistema

## Lo Básico

Por favor lea [https://pkp.sfu.ca/ojs/README](https://pkp.sfu.ca/ojs/README), [https://pkp.sfu.ca/omp/README](https://pkp.sfu.ca/omp/README), o [https://pkp.sfu. a/ocs/README](https://pkp.sfu.ca/ocs/README) para asegurar que el directorio de instalación del software y el área de almacenamiento de archivos (`files_dir` en `config.inc.php`) estén configuradas de forma segura en su servidor.

En general, el directorio `files_dir` no debería ser accesible desde la web y debería colocarse fuera del directorio principal de instalación del software. La aplicación de software administrará el acceso a archivos privados de envío basado en roles y permisos de usuario \(por ejemplo: Los editores tendrán acceso a todos los archivos enviados, mientras que los autores sólo podrán acceder a sus propios archivos\).

Además, para garantizar la seguridad el directorio `files_dir` no debería ser legible por otros usuarios en el servidor. Sólo el servidor web debería tener los permisos necesarios de lectura/escritura para que OJS, OMP, u OCS pueden leer archivos existentes y añadir nuevos archivos a la carpeta, p.ej.

`drwxrwx---   6 ojs www 204B 11 sep 2017 archivos/`

Los detalles exactos de los permisos de archivo dependerán de cómo su servidor web ejecute scripts PHP (esto se llama "API de servidor" o "SAPI"). Por ejemplo, si utiliza `mod_php`, todos los scripts PHP se ejecutarán como el usuario `www-data` o similar (esto no es inherentemente 100% seguro en un servidor multiusuario). Si utiliza CGI, FastCGI, FPM, o un mecanismo similar, probablemente correrá bajo su cuenta de usuario.

Se recomienda que instale un certificado SSL para su instalación OJS, OMP, u OCS y asegurarse de que su sitio siempre utiliza el protocolo HTTPS para administrar el registro de usuarios, iniciar sesión y presentar contenido a los lectores. Una vez que el certificado SSL ha sido instalado y se confirma que funciona \(por ejemplo, se puede acceder al sitio a través de [https://myjournal.org](https://myjournal.org/)\) puede configurar su sitio para que siempre use HTTPS usando la siguiente configuración en `config.inc.php`:

`; Force SSL connections site-wide
force_ssl = On`

También debería utilizar la versión HTTPS para la URL base de su revista, prensa o conferencia:

`; The canonical URL to the OJS installation
base_url = "https://myjournal.org"`

Para asegurar que las solicitudes de restablecimiento de contraseña de los usuarios generen contraseñas seguras y aleatorias, asegúrese de establecer una salt aleatoria y larga en `config.inc.php`:

`; The unique salt to use for generating password reset hashes
salt = "sdlkjfhleiqwrfgbksdlkjgbelruywoeiyt7384gdqlywqvlwjf"`

## Desplegando software PKP de forma segura

> Sección Contribuída por Kenton Good

Un despliegue seguro de software PKP se puede mejorar utilizando las siguientes recomendaciones, que se describen en docs/README en cada descarga del software:

* Dedicar una base de datos a OJS, OMP y OCS; usar credenciales únicas para acceder a ella. Configure esta base de datos para realizar copias de seguridad automatizadas de forma regular. Realice una copia de seguridad manual al actualizar o realizar mantenimiento. Si no tiene acceso a su base de datos póngase en contacto con el administrador del sistema y asegúrese de que las copias de seguridad de la base de datos de su OJS se están realizando.
* Configurar el software (`config.inc.php`) para que use el hashing SHA1 en lugar de MD5.
* Activar captcha o recaptcha en `config.inc.php` y probar que estén funcionando. Esto evitará la mayoría de los registros de usuarios spam.
* Configurar el software (`config.inc.php`) para que use `force_login_ssl` de manera que los usuarios autenticados se comuniquen con el servidor a través de HTTPS. (Deberá crear y configurar correctamente un certificado SSL para que esto funcione correctamente.)
* Instalar el software de manera que el directorio de archivos NO sea un subdirectorio de la instalación de OJS, OMP, u OCS y no se pueda acceder al mismo directamente a través del servidor web.
* Restringir los permisos de archivo tanto como sea posible.
* Despliegar y pruebar un mecanismo de copia de seguridad adecuado. El mecanismo de copia de seguridad debe hacer una copia de seguridad de la base de datos, los archivos del sistema, y el área de almacenamiento de archivos (el parámetro `files_dir` en `config.inc.php`). Idealmente, debe hacer copias de seguridad tanto en el servidor como fuera del mismo.
* Asegurar que el entorno del servidor web se actualiza regularmente, en particular con cualquiera y todos los parches de seguridad.

Si se siguen estos pasos, se reducirá sustancialmente el riesgo de ser víctima de técnicas comunes de piratería. Si ya está ejecutando OJS, OMP u OCS, le recomendamos encarecidamente que revise sus configuraciones existentes y se asegure de que se han seguido estos pasos.

### Gestión Segura de Archivos

Los autores, revisores y editores trabajan con archivos de personas que no conocen y hay algunas precauciones básicas que querrá tomar para reducir la posibilidad de verse comprometido a través de uno de estos archivos. Estos pasos no difieren de la forma en que se trabaja con el correo electrónico u otras actividades en Internet, pero vale la pena remarcarlos en forma general aquí.

* Asegúrese de que tiene instalado un software antivirus y de que está actualizado
* Asegúrese de que su sistema operativo y todo el software (especialmente Word y Excel) se mantienen actualizados, idealmente activando cualquier característica de actualización automática disponible
* Asegúrese de que tiene un sistema de copia de seguridad disponible para sus computadoras de trabajo
* Practique una buena gestión de contraseñas: no use el mismo nombre de usuario/contraseña en OJS, OMP, o OCS como lo haría para cualquier otra cuenta en línea, y no utilice una contraseña fácil de adivinar
* Trate todo lo que reciba desde Internet sabiendo que lo ha recibido de parte de alguien que no conoce y actúe en consecuencia. Si un envío parece sospechoso por cualquier razón (dirección de correo electrónico extraña, título genérico sospechoso o abstracto, etc.), trate los archivos incluidos con un nivel adicional de precaución.

## Encriptación

> Sección contribuída por Kenton Good

Utilizando la encriptación SSL/TLS para asegurar su sitio ofrece ventajas en seguridad, privacidad, protección de censura, optimización de motores de búsqueda e integridad de datos.

### Ventajas de Seguridad y Privacidad

* Los navegadores web han comenzado a mostrar advertencias sobre sitios inseguros para aquellos sitios web que todavía se sirven bajo http. Que los usuarios vean este mensaje cuando visitan su sitio pueden hacer que parezca ilegítimo, o potencialmente malicioso.
* Sin encriptación, las contraseñas de administrador y usuarios se pasan en texto claro, exponiendo potencialmente estas credenciales a cualquiera que escanee la red.
* Sin encriptación, usted no está protegiendo lo que sus usuarios están accediendo o buscando dentro de su sitio. Cualquiera que escanee la red puede ver estas consultas.

### Ventajas de la Optimización de Motores de Búsqueda (SEO)

Google anunció en 2014 que https [se trataría como una señal de clasificación](https://webmasters.googleblog.com/2014/08/https-as-ranking-signal.html) lo que significa que los sitios web que se sirven bajo https serán más encontrables dentro del índice de Google que los servidos bajo http.

### Obteniendo un Certificado

Un certificado permite una conexión segura desde el navegador web del usuario al servidor que aloja su sitio.  La instalación de un certificado para su sitio web no se puede hacer dentro del OJS, OMP, o OCS y requerirá experiencia técnica o asistencia a nivel del servidor.  Al considerar un host de software, determinar su nivel de soporte de certificados es una consideración importante.

Un buen comienzo es preguntar a su proveedor de servicios de alojamiento si tienen una solución existente con soporte para certificados TLS o SSL -- Su proveedor de alojamiento puede ser capaz de crear/emitir un certificado TLS o SSL para usted. Si no tiene acceso de shell a su instalación, esta puede ser la única forma de añadir protección SSL/TLS a su sitio.

Comprar un certificado también puede ser una opción disponible para usted. Las empresas comerciales que proporcionan certificados se llaman [Autoridades de Certificación](https://en.wikipedia.org/wiki/Certificate_authority) (CA). Wikipedia proporciona una lista de [proveedores CA](https://en.wikipedia.org/wiki/Certificate_authority#Providers) por cuota de mercado. Además de proporcionar certificados que permiten la encriptación, los certificados tradicionales emitidos por CA ofrecen varias ventajas, incluida la selección de clientes para ayudar a validar la identidad del cliente y los certificados comodín. Dependiendo de las características que seleccione con su compra, los certificados emitidos por CA pueden costar $100 por año y deben ser renovados antes de su vencimiento.

[Let’s Encrypt](https://letsencrypt.org/) también es una opción popular para obtener suscripción. Let’s Encrypt es una autoridad certificadora (CA) gratuita, automatizada y abierta, que funciona en beneficio del público. Es un servicio prestado por el Grupo de Investigación de Seguridad de Internet (ISRG). Los certificados se proporcionan de forma gratuita, con algunas limitaciones:

* Sólo se ofrecen certificados DV (Validación de Dominio)
* Un poco más técnico, si su proveedor de alojamiento no los soporta todavía tendrá que generarlos desde el shell

### Habilitando SSL (requiere acceso al shell):

#### Generando CSR (Solicitud de Firma de Certificación)

Paso necesario para generar un certificado si va a comprar un certificado de una CA que no es Let's Encrypt. La forma de hacer esto varía de una distribución Linux a otra. Algunos ejemplos comunes de Linux se pueden encontrar aquí:
* [Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04)
* [CentOS/RHEL 7](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-centos-7)
* [Generar certificado LetsEncrypt usando Certbot a través de línea de comandos](https://certbot.eff.org/) (proporciona instrucciones personalizadas dependiendo del entorno de alojamiento Apache, Nginx, etc.)
* [Guía para generar un .CSR en Apache](https://www.alphassl.com/support/create-csr/apache.html)

#### Manteniendo sus certificados

Cuando usted compra un certificado a una CA, el mismo durará normalmente entre 1 y 3 años. Los certificados están configuurados para caducar y tendrán que renovarse, dando lugar a un nuevo gasto en el momento de la renovación. Los certificados de Let’s Encrypt caducan cada 90 días. El software de Let’s Encrypt (certbot) puede configurarse para renovar automáticamente los certificados antes de que caduquen. Lo que significa que, una vez instalado, debería tener un mantenimiento mínimo en el futuro. La automatización se realiza mediante `cron` o `systemd`.

## Gestionando el Spam

Hay una serie de pasos de configuración que puede tomar para combatir el SPAM y otras formas de actividad de registro malicioso en su sitio.

### CAPTCHA/ReCAPTCHA

Configurar un CAPTCHA y habilitarlo para el registro de usuarios, notificaciones y comentarios. Recomendamos encarecidamente usar [ReCAPTCHA de Google](https://www.google.com/recaptcha/intro/). \(Las versiones anteriores de OJS 2 incluyen un captcha PHP, pero eso ha demostrado ser menos seguro que ReCaptcha.\)

Si está utilizando un OJS más antiguo que 2.4.8-3, no podrá utilizar la versión más reciente de ReCAPTCHA \(v2\), y su ReCAPTCHA no funcionará correctamente después del 31 de marzo de 2018. **Se recomienda encarecidamente actualizar**.

Para configurar ReCAPTCHA:

1. Register an account and create a “property” for your website here: [https://www.google.com/recaptcha/intro/](https://www.google.com/recaptcha/intro/)
2. Ensure that the relevant lines for enabling captcha are uncommented in `config.inc.php`
3. Copy and paste the public and private keys you receive as part of the property registration into the `[captcha]` section of `config.inc.php`:

```
recaptcha_public_key = 123456abcdef
Recaptcha_private_key = abcdef123456
```

Don’t forget to test this setup by registering a test account and confirming that a\) the ReCAPTCHA appears and b\) the ReCAPTCHA properly validates.

### Enable Account Validation

OJS, OMP, and OCS can be configured so that an email account validation step must be completed for all new user accounts before they can log in and interact with the system. To do this, uncomment and configure the following lines in `config.inc.php`:

```
; If enabled, email addresses must be validated before login is possible.
require_validation = On

; Maximum number of days before an unvalidated account expires and is deleted
validation_timeout = 14
```

The above configuration will require all new registrations to click on a link and validate their account before being able to log in; and will auto-prune any non-validated accounts after 14 days.

### Cleaning lots of users

If you have been the target of a SPAM bot, enabling the above procedures may not be enough: you may already have a large number of SPAM accounts in your system. The only way to remove users is to merge the spam accounts into an existing account using the Merge Users tool. \(This effectively deletes the problem users. Any submissions, editorial history, etc. from the problem user(s) is merged into the other user account.\)

This tool can be used via the UI, but is slower \(and only OJS/OCS 2 currently have an option to merge more than one user at a time in the UI\). A more effective method is to use the command-line tool:

`$ php tools/mergeUsers.php myUsername spamUser`

As of OJS/OMP 3.2, multiple users can be merged in a single command:

`$ php tools/mergeUsers.php myUsername spamUser1 spamUser2 spamUser3 [...]`

… where `myUsername` is the user that will be merged into, and `spamUser` is the user to be deleted. Prior to OJS/OMP 3.2, this tool only works on one merge at a time, but can be scripted. An example php script would be:

```
<?php
$names = file('/tmp/names.txt', FILE_SKIP_EMPTY_LINES);
foreach ($names as $member_name => $member) {
        echo exec ("php /ojswebroot/tools/mergeUsers.php admin-user " .escapeshellcmd($member));
}
?>
```

The script expects all spam accounts to be identified by username and listed in a `names.txt` file, one name per line, like so:

```
spamuserOne
spamUserTwo
spamUserThree
…
```

The `names.txt` file has to be stored on the server and the location referenced by the script \(eg. “/tmp/names.txt”\). The script should also specify the location of the mergeUsers.php script \(eg. “/ojswebroot/tools/mergeUsers.php”\), and also the user into which all of these accounts should be merged \(eg. “admin-user” - this must be an existing account\). Update those parameters to suit your environment. **Don’t store this script, or the `names.txt` file, in a web-accessible location!**