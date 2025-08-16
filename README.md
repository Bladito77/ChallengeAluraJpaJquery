Challenge con APi de Gutendex 
5 opciones por consola con realización de bases de datos en postgres (crea 3 tablas) 
Libros
Autores
libros_autores
nombre de la base de datos literatura
            1 - Buscar por título
            2 - Buscar libros registrados
            3 - Lista de autores registrados
            4 - Lista de autores vivos en determinado año
            5 - Lista de libros por idioma
            
            0 - Salir

            Presentado Por Edwin B Torres C

            Extraño mi papá


📚 Challenge Alura JPA + jQuery

Este proyecto es una aplicación backend en Java que consume la API pública de Gutendex, la cual ofrece información sobre libros digitalizados del Project Gutenberg.
El objetivo es realizar consultas, listar libros y mostrar resultados mediante diferentes endpoints, con almacenamiento y manipulación de datos en una base de datos relacional.

🚀 Tecnologías utilizadas

Java 17

Spring Boot

Spring Data JPA

PostgreSQL (puede adaptarse a otras bases)

Maven como gestor de dependencias

jQuery / Ajax para la comunicación con la API desde el frontend

⚙️ Funcionamiento

Consumo de API externa

La aplicación consulta la API de Gutendex a través de endpoints REST.

Ejemplo: /api/libros/titulo?nombre=Don Quijote devuelve información del libro en JSON.

Persistencia en base de datos

Los resultados de las consultas se almacenan en la base de datos usando JPA.

Se pueden listar libros previamente consultados sin necesidad de llamar nuevamente a la API.

Listados y filtros

Se pueden generar listados de:

Libros por título

Autores registrados

Autores vivos en un año específico

Libros por idioma

Visualización

Desde el frontend, con jQuery, se renderizan listas dinámicas.

En el archivo de ejemplos se utiliza un scatter plot para visualizar patrones de datos (ejemplo: cantidad de libros por año o idioma).

📂 Estructura del proyecto
ChallengeAluraJpaJquery/
 ┣ src/main/java/com/example/
 ┃ ┣ model/        → Entidades JPA (Libro, Autor, etc.)
 ┃ ┣ repository/   → Interfaces de repositorios (Spring Data JPA)
 ┃ ┣ service/      → Lógica de negocio y consumo de API Gutendex
 ┃ ┣ controller/   → Controladores REST
 ┃ ┗ principal/    → Clase principal para ejecución
 ┣ src/main/resources/
 ┃ ┣ application.properties → Configuración de DB y API
 ┣ pom.xml         → Dependencias Maven
 ┗ README.md       → Este archivo

▶️ Ejecución

Clonar el repositorio:

git clone https://github.com/usuario/ChallengeAluraJpaJquery.git
cd ChallengeAluraJpaJquery


Configurar la base de datos en application.properties:

spring.datasource.url=jdbc:postgresql://localhost:5432/gutendexdb
spring.datasource.username=postgres
spring.datasource.password=tu_clave
spring.jpa.hibernate.ddl-auto=update


Compilar y ejecutar:

mvn spring-boot:run


Acceder a los endpoints en:

http://localhost:8080/api/libros

📊 Ejemplo de uso

Buscar libro por título:

GET http://localhost:8080/api/libros/titulo?nombre=Hamlet


Listar autores:

GET http://localhost:8080/api/autores


Visualizar scatter con datos cargados (desde el archivo incluido en el repo).

✨ Próximas mejoras

Agregar autenticación JWT para seguridad.

Optimizar cache de consultas a la API Gutendex.

Ampliar visualizaciones interactivas en frontend.

Opciones del menú

Buscar por título

Buscar libros registrados

Lista de autores registrados

Lista de autores vivos en determinado año

Lista de libros por idioma

Salir

¿Qué hace cada opción (y qué pasa tras bambalinas)?

1) Buscar por título

Te pide un título por consola.

Codifica el texto y consulta Gutendex: https://gutendex.com/books/?search=<tu_titulo> usando HttpClient (ConsumoApi.obtenerDatos).

Mapea el JSON a objetos con ObjectMapper (ConvierteDatos → Wrapper → DatosLibros).

Muestra en consola: Título, Autor (si viene), Idioma (toma el primero de languages y lo traduce con el enum Idioma), y Descargas.

Persistencia: por cada resultado, si no existe en DB (LibroRepository.findByTituloContainsIgnoreCase), crea un LibroEntity y guarda también los autores como AutorEntity (evita duplicados con AutorRepository.findByNombre).

Resultado: queda almacenado en PostgreSQL y ya puedes listarlo sin volver a llamar a la API.

2) Buscar libros registrados

Lee todos los libros desde la DB con LibroRepository.findAll().

Imprime: Título, Autor, Idioma, Descargas.

No llama a Gutendex; es solo lectura local.

3) Lista de autores registrados

Toma todos los libros (findAll()), extrae los autores de cada libro y arma un set (sin duplicados) ordenado alfabéticamente.

Imprime cada autor por consola.

Útil para ver rápidamente a quiénes ya tienes guardados.

4) Lista de autores vivos en determinado año

Te pide un año (ej. 1900).

Recorre los autores de los libros guardados y filtra a quienes nacieron antes o en ese año y no han muerto para ese año (o se desconoce su fallecimiento).

Devuelve la lista ordenada (usa un TreeSet con comparador por nombre) y la imprime.

Todo el cálculo se hace local con los datos persistidos (no golpea la API).

5) Lista de libros por idioma

Muestra primero los idiomas disponibles que hay en la DB (por ejemplo EN, ES, FR, OTRO).

Te pide un código (EN, ES, etc.), filtra en memoria los LibroEntity por getIdioma() y los imprime.

Tampoco usa la API; trabaja sobre lo que ya guardaste.

0) Salir

Cierra el loop del menú y termina la app.

Flujo general de la app (resumen rápido)

Arranque: LiteraturaApplication implementa CommandLineRunner, crea Principal y llama muestraElMenu().

Consulta externa: solo la opción 1 pega a Gutendex.

Mapeo JSON → objetos: Wrapper (lista results) y DatosLibros (título, autores, idiomas, descargas).

Persistencia: LibroEntity y AutorEntity con Spring Data JPA hacia PostgreSQL.

Consultas locales: opciones 2–5 trabajan con la DB (rápido, sin internet).
