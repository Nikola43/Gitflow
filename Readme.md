# Funcionamiento
![Funcionamiento](https://wac-cdn.atlassian.com/dam/jcr:2bef0bef-22bc-4485-94b9-a9422f70f11c/02%20(2).svg?cdnVersion=1146?raw=true "Funcionamiento")
## Ramas de desarrollo y maestras
En vez de una única rama maestra, este flujo de trabajo utiliza dos ramas para registrar el historial del proyecto. La rama maestra almacena el historial de publicación oficial y la rama de desarrollo sirve como rama de integración para funciones. Asimismo, conviene etiquetar todas las confirmaciones de la rama maestra con un número de versión.

El primer paso es complementar la maestra predeterminada con una rama de desarrollo. Una forma sencilla de hacerlo es que un desarrollador cree una rama de desarrollo vacía localmente y la envíe al servidor:
```sh
$ git branch develop
$ git push -u origin develop
```
Esta rama contendrá el historial completo del proyecto, mientras que la maestra contendrá una versión abreviada. A continuación, otros desarrolladores deberían clonar el repositorio central y crear una rama de seguimiento para desarrollo.

Al utilizar la biblioteca de extensiones de git-flow, ejecutar git flow init en un repositorio existente creará la rama de desarrollo:
```sh
$ git flow init
Initialized empty Git repository in ~/project/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]
Branch name for "next release" development: [develop]

How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []

$ git branch
* develop
 master
```
## Ramas de función
Cada nueva función debe estar en su propia rama, que se puede enviar al repositorio central para copia de seguridad/colaboración. Sin embargo, en vez de ramificarse de la maestra, las ramas de función utilizan la de desarrollo como rama primaria. Cuando una función está completa, se vuelve a fusionar en la de desarrollo. Las funciones nunca deben interactuar directamente con la maestra.
![Ramas](https://wac-cdn.atlassian.com/dam/jcr:b5259cce-6245-49f2-b89b-9871f9ee3fa4/03%20(2).svg?cdnVersion=1146?raw=true "Ramas")
Ten en cuenta que las ramas de función combinadas con la rama de desarrollo conforman, a todos efectos, el flujo de trabajo de rama de función. Sin embargo, el flujo de trabajo de Gitflow no termina aquí.

Las ramas de función normalmente se crean a partir de la última rama de desarrollo.
## Creación de una rama de función
Sin las extensiones de git-flow:
```sh
$ git checkout develop
$ git checkout -b feature_branch
```
Cuando se utiliza la extensión de git-flow:
```sh
$ git flow feature start feature_branch
```
Sigue trabajando y utiliza Git como lo harías normalmente.
## Finalización de una rama de función
Cuando hayas terminado con el trabajo de desarrollo en la función, el siguiente paso es fusionar feature_branch en develop.
Sin las extensiones de git-flow:
```sh
$ git checkout develop
$ git merge feature_branch
```
Con las extensiones de git-flow:
```sh
$ git flow feature finish feature_branch
```
## Ramas de publicación
![Ramas](https://wac-cdn.atlassian.com/dam/jcr:a9cea7b7-23c3-41a7-a4e0-affa053d9ea7/04%20(1).svg?cdnVersion=1146?raw=true "Ramas")
Una vez que el desarrollo ha adquirido suficientes funciones para una publicación (o se está acercando una fecha de publicación predeterminada), bifurcas una rama de versión a partir de una de desarrollo. Al crear esta rama, se inicia el siguiente ciclo de publicación, por lo que no pueden añadirse nuevas funciones tras este punto; solo las soluciones de errores, la generación de documentación y otras tareas orientadas a la publicación deben ir en esta rama. Una vez que esté lista para el lanzamiento, la rama de publicación se fusiona en la maestra y se etiqueta con un número de versión. Además, debería volver a fusionarse en la de desarrollo, que podría haber progresado desde que se inició la publicación.

Utilizar una rama específica para preparar publicaciones hace posible que un equipo perfeccione la publicación actual mientras otro equipo sigue trabajando en las funciones para la siguiente publicación. Asimismo, crea fases de desarrollo bien definidas (por ejemplo, es fácil decir: "Esta semana nos estamos preparando para la versión 4.0" y verlo escrito en la estructura del repositorio).

Crear ramas de publicación es otra operación de ramificación sencilla. Al igual que las ramas de función, las ramas de publicación se basan en la rama de desarrollo. Se puede crear una nueva rama de publicación utilizando los siguientes métodos.
Sin las extensiones de git-flow:
```sh
$ git checkout develop
$ git checkout -b release/0.1.0
```
Con las extensiones de git-flow:
```sh
$ git flow release start 0.1.0
Switched to a new branch 'release/0.1.0'
```
Una vez que la publicación esté lista para su lanzamiento, se fusionará en la maestra y la de desarrollo; a continuación, se eliminará la rama de publicación. Es importante volver a fusionarla en la de desarrollo porque podrían haberse añadido actualizaciones críticas a la rama de publicación y las nuevas funciones tienen que poder acceder a ellas. Si tu organización enfatiza la revisión de código, sería el lugar ideal para una solicitud de incorporación de cambios.

Para finalizar una rama de publicación, utiliza los siguientes métodos:

Sin las extensiones de git-flow:
```sh
$ git checkout master
$ git merge release/0.1.0
```
Con las extensiones de git-flow:
```sh
$ git flow release finish '0.1.0'
```
## Ramas de corrección
![Ramas](https://wac-cdn.atlassian.com/dam/jcr:61ccc620-5249-4338-be66-94d563f2843c/05%20(2).svg?cdnVersion=1146?raw=true "Ramas")
Las ramas de mantenimiento o "corrección" (hotfix) se utilizan para reparar rápidamente las publicaciones de producción. Las ramas de corrección son muy similares a las ramas de publicación y a las de función, salvo porque se basan en la maestra en vez de la de desarrollo. Es la única rama que debería bifurcarse directamente a partir de la maestra. Una vez que la solución esté completa, debería fusionarse en la maestra y la de desarrollo (o la rama de publicación actual), y la maestra debería etiquetarse con un número de versión actualizado.

Tener una línea de desarrollo específica para la solución de errores permite que tu equipo aborde las incidencias sin interrumpir el resto del flujo de trabajo ni esperar al siguiente ciclo de publicación. Puedes considerar las ramas de mantenimiento como ramas de publicación ad hoc que trabajan directamente con la maestra. Una rama de corrección puede crearse utilizando los siguientes métodos:
Sin las extensiones de git-flow:
```sh
$ git checkout master
$ git checkout -b hotfix_branch
```
Con las extensiones de git-flow:
```sh
$ git flow hotfix start hotfix_branch
```
Al igual que al finalizar una rama de publicación, una rama de corrección se fusiona tanto en la maestra como en la de desarrollo.
```sh
git checkout master
git merge hotfix_branch
git checkout develop
git merge hotfix_branch
git branch -D hotfix_branch
```
```sh
$ git flow hotfix finish hotfix_branch
```
## Ejemplo
A continuación, se incluye un ejemplo completo que demuestra un flujo de ramas de función. Vamos a asumir que tenemos una configuración del repositorio con una rama maestra.
```sh
git checkout master
git checkout -b develop
git checkout -b feature_branch
# work happens on feature branch
git checkout develop
git merge feature_branch
git checkout master
git merge develop
git branch -d feature_branch
```
Además del flujo de función y publicación, un ejemplo de corrección sería de la siguiente manera:
```sh
git checkout master
git checkout -b hotfix_branch
# work is done commits are added to the hotfix_branch
git checkout develop
git merge hotfix_branch
git checkout master
git merge hotfix_branch
```
## Resumen
El flujo general de Gitflow es el siguiente:

1 - Se crea una rama de desarrollo a partir de la maestra.\
2 - Una rama de publicación se crea a partir de la de desarrollo.\
3 - Las ramas de función se crean a partir de la de desarrollo.\
4 - Cuando una función está completa, se fusiona en la rama de desarrollo.\ 
5 - Cuando la rama de publicación está lista, se fusiona en la de desarrollo y la maestra.\
6 - Si se detecta una incidencia en la maestra, se crea una rama de corrección a partir de la maestra.\
7 - Una vez que la corrección está completa, se fusiona tanto con la de desarrollo como con la maestra.\

## Librería para gestionar GitFlow
[GitFlow cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)