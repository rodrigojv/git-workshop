# Workshop de Git

## ¿Qué es Git?

Un sistema _distribuido_ de **control de versiones**.

- Administra los cambios en el código
- Sincroniza código entre miembros de un equipo
- Permite probar cosas de forma separada sin perder lo que funciona
- Permite volver a versiones anteriores del código

## Instalación de Git

Se asume que se tiene instalado Git, si no es así se puede ir a la [página oficial y bajar el instalador](https://git-scm.com/downloads).

## Configuración

Es conveniente configurar tu identidad para poder trabajar con otros.

    $ git config --global user.name "Your Name"
    $ git config --global user.email your.email@example.com

También es útil configurar tu editor favorito a la hora de resolver conflictos.

    $ git config --global core.editor "code --wait"

## Directorio de trabajo

Preparamos un directorio de trabajo vacío para inicializarlo con git.

    $ mkdir demo
    $ cd demo
    $ git init

## Preparando el commit - git add

Creamos un archivo en el directorio de trabajo.

    $ touch index.html

Para ver la reacción de git con este cambio se puede recurrir siempre al comando `git status`

```bash
$ git status
On branch master

No commits yet

Untracked files:
    (use "git add <file>..." to include in what will be committed)
        index.html
```

Si queremos guardar este archivo y decirle a Git que haga un seguimiento primero tenemos que ejecutar el comando `git add`

    $ git add index.html

> Con **git add** le decimos a git, estos son los cambios que queremos que se vayan en el siguiente commit.

## Haciendo commit

Un commit es un snapshot en el tiempo de el estado de nuestro proyecto. Los commits son los que cuentan la historia de los cambios que fueron ocurriendo en nuestro código.

Tienen:

- mensaje: descripción del cambio que se hizo
- un hash: texto identificador del commit
- author: quién hizo los cambios
- otros metadatos

Una vez que se ejecutó el git add podemos hacer commit del archivo.

    $ git commit -m "Agrega index.html"

Esto guarda un snapshot del cambio con un mensaje descriptivo.

Si ejecutamos `git log` podemos ver que git ya nos provee información histórica de los cambios.

```
$ git log

commit 727057b574cdcdff2102449349fa516747bcd52e (HEAD -> master)
Author: Rodrigo Villalba <rodris.jvo@gmail.com>
Date:   Tue Apr 20 11:14:32 2021 -0400

    Agrega index.html
```

🔦 Pro Tip

> Utilizar el siguiente comando para un log más legible, y agregar al bash_profile un alias para el mismo.

    $ git log --all --graph --decorate --oneline

## Branching y merging

Digamos que le agregamos un esqueleto de código html básico al index.html.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    Hola Mundo
</body>
</html>
```

Luego hacemos `git add` y `git commit -m "Agrega html básico` y tenemos el siguiente estado de git.

```
$ git log --all --graph --decorate --oneline
* 9e8809e (HEAD -> master) Agrega html básico
* 06620e2 Agrega index.html
```

Ahora nos piden que implementemos la funcionalidad de agregar un título en color rojo.

Primero creamos la rama

    $ git branch red

Luego nos mudamos a la rama

    $ git checkout red

Ahi editmos index.html y agregamos un style en el head

```html
<style>
  h1 {
    color: red;
  }
</style>
```

Y un `h1` debajo de hola mundo

```html
Hola Mundo
<h1>Rojo</h1>
```

Add y commit para ver el estado.

```
➜ git log --graph --all --decorate --oneline
* 0aecb81 (HEAD -> red) Agrega título rojo
* 9e8809e (master) Agrega html básico
* 06620e2 Agrega index.html
```

Ahora supongamos que tenemos que implementar una funcionalidad separa para agregarla a master, dicha funcionalidad requiere que se agregue un título nuevo. De la misma forma que se hizo anteriormente, creamos a partir de `master` una rama `blue` y le agregamos un título `h2`.

```html
<style>
  h2 {
    color: blue;
  }
</style>
```

```html
Hola Mundo
<h2>Azul</h2>
```

Hacemos las modificaciones y con el posterior commit sobre `blue`, volviendo a master tenemos la siguiente situación en la cual queremos hacer la mezcla de ambas ramas paralelas de desarrollo.

```
$ git log --all --graph --decorate --oneline
* 1125f7b (blue) Agrega título color azul
| * 0aecb81 (red) Agrega título rojo
|/
* 9e8809e (HEAD -> master) Agrega html básico
* 06620e2 Agrega index.html
```

### Mezcla de ramas

Usamos el comando `git merge <rama>` para mezclar los cambios de red a master.

```
$ git merge red
Updating 9e8809e..0aecb81
Fast-forward
 index.html | 6 ++++++
 1 file changed, 6 insertions(+)
```

Notamos que git hizo una mezcla del tipo fast-forward, que es una de las estrategias de mezcla que tiene la herramienta.

Al hacer la mezcla de red, ocurre un conflicto ya que se modificaron líneas similares en el código en ambas ramas y git no supo como resolver esta modificación.

```
$ git merge blue
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

### Resolviendo conflictos

Para resolver el conflicto eventualmente se recomienda usar una herramienta con interfaz editora y Git integrado, por ahora lo resolvermos a mano.

Abrimos el archivo `index.html` veremos hay líneas marcadas con separadores `<<<` y `>>>` en los puntos conflictivos.

```
<<<<<< HEAD
        h1 {
            color: red;
=======
        h2 {
            color: blue;
>>>>>>> blue
```

Lo que está entre los separadores `<<<HEAD CODIGO ===` son los cambios de la rama actual donde estamos parados y lo que está entre `=== CODIGO >>> [rama]` son los cambios de la rama que intentamos mezclar.

Resolvemos los conflictos unificando ambas líneas de código y borrando los separadores.

Al finalizar esto tenemos que guardar el archivo index.html y hacer commit de la edición que hicimos sobre el archivo para resolver los conflictos.

```
➜ git commit -am "Resuelve conflictos"
[master 4c1d079] Resuelve conflictos
```

> `git -am` es un atajo que combina los comandos de `git add .` y `git commit -m`. `git add .` comunica a git que todos los cambios que hicimos en el directorio actual `.`, queremos que se vayan en el siguiente commit. No se recomienda utilizar esto salvo para cambios menores sobre un sólo archivo.

Finalmente tenemos el siguiente resultado.

```
$ git log --all --graph --decorate --oneline
*   4c1d079 (HEAD -> master) Resuelve conflictos
|\
| * 1125f7b (blue) Agrega título color azul
* | 0aecb81 (red) Agrega título rojo
|/
* 9e8809e Agrega html básico
* 06620e2 Agrega index.html
```

## Remotes

**Remotes** son repositorios de git hosteados en otros servidores con los cuales podemos colaborar. El servidor de repositorios git más popular es Github, que además de almacenar nuestros proyectos git, provee otras funcionalidades como revisión de código.

En Github también existe el concepto de **Fork** que es crear una copia propia de un proyecto en nuestra cuenta particular de Github.

El botón de Fork se encuentra arriba a la derecha en la pantalla del repositorio Github. Para más ayuda visitar [esta página](https://help.github.com/articles/fork-a-repo/).

Una vez que se hace Fork del proyecto, podemos clonar el repositorio copiado a nuestrta máquina.

    $ git clone https://github.com/rodrigojv/git-workshop.git

Una vez que se clonó se debería observar un directorio con el nombre `git-workshop`. Este es el `working directory`

    $ cd git-workshop
    $ ls
    README.md

Con el comando `git remote -v` podemos listar los remotes, que son basicamente nombres asignados por Git, con URL indicando la localidad de esos remotes, que son básicamente copias exactas de lo que tenemos localmente.

```
$ git remote -v
origin	https://github.com/rodrigojv/git-workshop.git (fetch)
origin	https://github.com/rodrigojv/git-workshop.git (push)
```

### Comandos para manejar remotes

- `git push <remote> <local branch>:<remote branch>`: envía objetos al remote
- `git fetch`: obtiene nuevos cambios y ramas (y otras referencias) del remoto
- `git pull`: lo mismo que `git fetch; git merge`

### 🔥 Demo de `pull`, `push` y pull request.

1. Crear un archivo en repo clonado
2. Subir ese archivo con push
3. Modificar el archivo en Github
4. Traer los cambios hechos en Github al local
5. Crear un pull request de los cambios

## Ejercicio

1. Fork y clone de https://github.com/jokoframework/joko-utils
2. Realizar algunos de los cambios sugeridos por el instructor
3. Crear un pull request de los cambios

## Importancia de buenos mensajes de commit

**Es importante siempre escribir buenos mensajes de commit.**

Antes que leer otra cosa se recomienda revisar los siguientes artículos:

- https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
- https://chris.beams.io/posts/git-commit/

## Lectura adicional

- [Pro Git](https://git-scm.com/book/en/v2): altamente recomendado. Leer al menos los capítulos del 1 al 5.
- [Oh Shit, Git!?!](https://ohshitgit.com/): una guía corta sobre como recuperarse de errores comunes con Git.
- [How to explain git in simple words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
- [Learn Git Branching](https://learngitbranching.js.org/): un juego en el navegador que te enseña Git.

## Otros recursos

- Integración enchulada de Git en el shell prompt, para ver la rama actual y el estado: [zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt). También existen frameworks como [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh).
