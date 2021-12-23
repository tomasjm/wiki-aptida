# Wiki del código fuente de la plataforma Aptida

En este repositorio se encuentra la información en forma de Wiki escrita en Markdown correspondiente a la plataforma Aptida, haciendo distinción entre el front-end, back-end y los requerimientos del servidor de producción (donde se aloja el software back-end).

# Tabla de contenidos
- [Documentación del front-end: Aptida](#documentación-del-front-end-aptida)
  - [Tecnologias utilizadas](#tecnologias-utilizadas)
  - [Librerias utilizadas](#librerias-utilizadas)
  - [Estructura del código](#estructura-del-código)
  - [Páginas y rutas](#páginas-y-rutas)
    - [Rutas disponibles](#rutas-disponibles)
  - [Estado lógico de la aplicación (context)](#estado-lógico-de-la-aplicación-context)
  - [Peticiones](#peticiones)
  - [Autenticación](#autenticación)
  - [Hooks](#hooks)
    - [useDatos](#usedatos)
    - [useTable](#usetable)
    - [useMapa](#usemapa)
  - [Componentes importantes](#componentes-importantes)
    - [Tabla](#tabla)
    - [Mapa](#mapa)
    - [Gráfico](#gráfico)
- [Documentación del servidor back-end: API y Base de Datos](#documentación-del-servidor-back-end-api-y-base-de-datos)
  - [Tecnologias utilizadas](#tecnologias-utilizadas-1)
    - [Software](#software)
    - [Base de datos](#base-de-datos)
    - [Comunicación y protocolos](#comunicación-y-protocolos)
    - [Contenedores](#contenedores)
  - [Librerias utilizadas](#librerias-utilizadas-1)
  - [Estructura del código](#estructura-del-código-1)
# Documentación del front-end: Aptida

Esta es la documentación correspondiente al repositorio de `sensores-front`, esto es el front-end, la parte visual de la aplicación, para entender bien el flujo de datos es necesario aprender, a la vez, la estructura de datos utilizada en el servidor de Aptida y la base de datos `sensores-api`.

## Tecnologias utilizadas
* React
* Framework React: NextJS
* Typescript

## Librerias utilizadas
* Para el diseño y estilos: [Tailwindcss](https://tailwindcss.com/)
* Para la utilización de mapas: [react-leaflet](https://react-leaflet.js.org/) / [react-leaflet-universal](https://www.npmjs.com/package/react-leaflet-universal) este para que funcione el componente SSR de NextJS
* Para gráficas se utiliza: [recharts](https://recharts.org/) y para sus intervalos se usa [D3.js](https://d3js.org/)
* Para los formularios se utilizan componentes custom y un componente comunitario [react-day-picker](https://react-day-picker.js.org/) 
* Para los componentes de loading se hace uso de [react-spinners](https://github.com/davidhu2000/react-spinners)
* Para la tabla de datos se hace uso del componente [react-data-table-component](https://github.com/jbetancur/react-data-table-component)
* Para el uso de fechas y localizaciones se hace uso de [Momentjs(https://momentjs.com/)
* Para pasar de HTML a una Imagen se hace uso de [html-to-image](https://www.npmjs.com/package/html-to-image) y [file-saver](https://www.npmjs.com/package/file-saver)
* Para peticiones de datos y caching se usa de la libreria [react-query](https://react-query.tanstack.com/)

## Estructura del código
Se sigue la filosofia de la estructura de archivos de NextJS, por lo tanto es necesario introducirse en la documentación del framework para no estar perdido.

- La carpeta `components` contiene todos los fragmentos de código o componentes que contienen su propia lógica y estructura. Ejemplos de componentes son: el gráfico, la tabla, los inputs, el formulario en donde se contienen los inputs, etc.

- La carpeta `context` contiene todos los contextos (aprender React) para manejar el estado de la aplicación, se sigue la filosofia de Redux.

- La carpeta `hooks` contiene todos los custom hooks (aprender React) para manejar la lógica y datos más complejos de la aplicación.

- La carpeta `lib` contiene solo funciones "helpers" que hacen tareas generales y sencillas.

- La carpeta `pages` contiene los layout y páginas (aprender NextJS) según como funciona la aplicación.

- La carpeta `requests` contiene todas las peticiones de datos hacia el servidor.

- La carpeta `styles` contiene los estilos css.

- Los archivos de configuración corresponden a la configuración de Typescript, NextJS y TailwindCSS.


## Páginas y rutas

La aplicación no guarda el estado en las rutas, si no que los guarda en context.
- Una ventaja de esto es que es más fácil evitar errores, mantener performance de la aplicación y disminuir costos del SSR de NextJS (petición de los directores).
- Desventajas es que no se podrán compartir links que contengan la información necesaria para llegar y cargar el gráfico y usarlo de muestra.

### Rutas disponibles
Si se conoce como funciona NextJS, se sabrá que estas se encuentran en la carpeta de `pages`:
* `_app.tsx` : Es un contenedor en común de todas las páginas (no visible por usuario)
* `index.tsx` : Es la ruta default `"/"` donde se carga el formulario de inicio y selección de visualización solo si y solo si se encuentra autenticado, de caso contrario se muestra el componente `components/Login.tsx`
* `grafico.tsx` : Es la ruta `"/grafico"` en donde se carga el componente `components/Grafico.tsx`.
* `mapa.tsx` : Es la ruta `"/mapa"` en donde se carga el componente `components/Map.tsx`
* `tabla.tsx` : Es la ruta `"/tabla"` en donde se carga el componente de la libreria de `react-data-table-component` 

## Estado lógico de la aplicación (context)
Como no se trata el estado de la aplicación mediante rutas, se hace mediante el uso de Contextos, para entender esto es necesario estudiar React. El context solo sirve para compartir la selección del formulario y el estado de la autenticación.
En la carpeta `context` se tienen dos archivos:

* `app.types.ts` : Donde se guardan constantes que indican tipos de acciones que podrá entender y ejecutar el context.
* `app.context.ts` : Es donde se configura el context, donde se establece el estado general e inicial de la aplicación y como este context reacciona a las diferentes acciones establecidas en el archivo anterior. Las acciones del contexto se establecen como un `reducer`. Insisto que todos estos términos son de React y es necesario conocer esta herramienta.

Se utiliza context como una store general en donde se podrá acceder a estos datos desde cualquier parte de la aplicación (a nivel de componente), para entender el por qué, se puede revisar sobre el concepto `react props to children vs context`.

## Peticiones
Para revisar las peticiones HTTP existentes, es necesario revisar la documentación Swagger del backend, sin embargo, el front-end contiene todas las peticiones necesarias para su funcionamiento incluidas en el archivo `requests/requests.ts`.

Cada petición está envuelta en una función en donde su nombre explica que datos trae y que necesita, en caso de duda, se recomienda revidar la documentación Swagger del back-end.

Para las peticiones se usa el Wrapper de Axios que es una conocida libreria para hacer fetching de datos a un servidor. React-query se utiliza solo para hacer cache de la selección de datos del formulario, y que los datos de visualización se mantengan actualizados ante una eventual inactividad del usuario.

## Autenticación

Para la autenticación se utiliza un método basado en tokens, especificamente bajo método de bearer tokens con JWT, este se basa en que un objeto de datos json se hashea con un SECRET solo conocido por el servidor, este hash es desencriptable por lo tanto no se debe incluir información sensible dentro del objeto. La gracia es que solo conociendo el SECRET se puede VALIDAR el token.

El flujo de autenticación es enviar una petición POST al servidor en la ruta `auth/login` con un body de tipo `application/json` con la estructura:

`
{
  "user": {
    "usuario": "<string>",
    "password": "<string>"
  }
}
`
La respuesta del servidor ante un login de datos correctos, será un JWT (Json Web Token) el cual se utilizará para validar la sesión del usuario. En caso contrario, el servidor enviará un código de error.

Para validar la sesión del usuario, se debe enviar el token a la ruta `auth/token` con la cabecera Authentication con el formato `Bearer <JWT>`, el servidor enviará un código de respuesta que puede ser correcto o incorrecto.

Esta autenticación puede mejorarse implementando seguridad de refresh tokens y access tokens, incluyendo CSRF Tokens y otros, bajo el estándar de flujo de autenticación OAuth 2.0. Esto se trabaja e implementa en el software back-end.

## Hooks

### useDatos
### useTable
### useMapa

## Componentes importantes
### Tabla
### Mapa
### Gráfico


# Documentación del servidor back-end: API y Base de Datos

## Tecnologias utilizadas

### Software
* Typescript
* ExpressJS
* NestJS

### Base de datos
* PostgreSQL

### Comunicación y protocolos
* Formato API REST
* Intercambio de datos mediante JSON
  
### Contenedores
* Docker

## Librerias utilizadas


## Estructura del código

