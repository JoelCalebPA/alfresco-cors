# Alfresco CORS configuration

Para habilitar el CORS en el servidor Alfresco se debe agregar las siguientes lineas al fichero ubicado en la ruta `alfresco-7.2.0/tomcat/shared/classes/alfresco-global.properties`

```properties
cors.enabled=true
cors.allowed.origins=http://localhost:8080
cors.allowed.methods=GET,POST,PUT,DELETE
cors.allowed.headers=Authorization,Content-Type,Cache-Control,X-Requested-With,X-CSRF-Token
cors.exposed.headers=Access-Control-Allow-Origin,Access-Control-Allow-Credentials
cors.support.credentials=true
cors.preflight.maxage=10
```
En la linea `cors.allowed.origins` indicamos, separados por coma, los host a los que el servidor de Alfresco permitira peticiones (CORS habilitado para estos hosts).

Luego de agregar la configuración reiniciamos el servicio de Alfresco.

## Configuración alterna

En la versión 7.0 de Alfresco había un problema al colocar el comodin `*` (habilitado para cualquier host) como valor de la propiedad `cors.allowed.origins` y no tomaba efecto la configuración de CORS. Si el caso requeria de que el cors este habilitado para todos host (usualmente en ambientes de desarrollo) se tenía que hacer la configuración de la siguiente manera.

Primero, agregamos los siguientes JARs a la carpeta `alfresco-7.2.0/tomcat/webapps/alfresco/WEB-INF/lib`

- cors-filter-2.5
- java-property-utils-1.9.1

Ahora editamos el fichero `alfresco-7.2.0/tomcat/webapps/alfresco/WEB-INF/server.xml` y agregamos las siguientes lineas casi al final del contenido, antes de la etiqueta `</web-app>`:

```xml
<filter>
	<filter-name>CORS</filter-name>
	<filter-class>com.thetransactioncompany.cors.CORSFilter</filter-class>
	<init-param>
		<param-name>cors.allowGenericHttpRequests</param-name>
		<param-value>true</param-value>
	</init-param>
	<init-param>
		<param-name>cors.allowOrigin</param-name>
		<param-value>*</param-value>
	</init-param>
	<init-param>
		<param-name>cors.allowSubdomains</param-name>
		<param-value>true</param-value>
	</init-param>
	<init-param>
		<param-name>cors.supportedMethods</param-name>
		<param-value>GET, HEAD, POST, PUT, DELETE, OPTIONS</param-value>
	</init-param>
	<init-param>
		<param-name>cors.supportedHeaders</param-name>
		<param-value>origin, authorization, x-file-size, x-file-name, content-type, accept, x-file-type, range</param-value>
	</init-param>
	<init-param>
		<param-name>cors.exposedHeaders</param-name>
		<param-value>Accept-Ranges, Content-Encoding, Content-Length, Content-Range</param-value>
	</init-param>
	<init-param>
		<param-name>cors.supportsCredentials</param-name>
		<param-value>true</param-value>
	</init-param>
	<init-param>
		<param-name>cors.maxAge</param-name>
		<param-value>3600</param-value>
	</init-param>
</filter>

<filter-mapping>
	<filter-name>CORS</filter-name>
	<url-pattern>/api/*</url-pattern>
	<url-pattern>/service/*</url-pattern>
	<url-pattern>/s/*</url-pattern>
	<url-pattern>/cmisbrowser/*</url-pattern>
	<url-pattern>/definitions/*</url-pattern>
</filter-mapping>
```

Luego de agregar la configuración reiniciamos el servicio de Alfresco.
