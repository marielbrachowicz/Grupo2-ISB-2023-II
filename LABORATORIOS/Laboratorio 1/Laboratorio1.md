# Resumen Laboratorio 1

El flujo general puede resumirse así:

**GitHub → clonar repositorio → trabajar localmente en VS Code → registrar cambios con Git → subirlos nuevamente a GitHub.**

## 1. Diferencia entre Git y GitHub

### Git

Es el sistema de control que funciona localmente en nuestra computadora. Su función es registrar los cambios realizados en los archivos de un proyecto a lo largo del tiempo.

### GitHub

Es una plataforma en la nube que permite almacenar repositorios Git de manera remota y compartirlos con más personas. Gracias a esto es posible que varios integrantes de un equipo trabajen sobre el mismo proyecto.


## 2. Configuración inicial de Git

Después de instalar Git, es necesario configurar el nombre y correo que identificarán nuestros commits.

```bash
git config --global user.name "Nombre"
git config --global user.email "correo@example.com"
```

La opción `--global` indica que esta configuración se utilizará de manera general en los repositorios de ese usuario en la computadora.

También podemos comprobar que Git se instaló correctamente con:

```bash
git --version
```

---

## 3. Repositorios locales y remotos

Son lugares en donde se almacenan los archivos de un proyecto junto con el historial de cambios administrado por Git

* **Repositorio local:** es la copia del proyecto que se encuentra en nuestra computadora
* **Repositorio remoto:** es la versión compartida del proyecto almacenada en una plataforma como GitHub

Durante el trabajo normalmente modificamos los archivos en el repositorio local y luego enviamos los cambios al repositorio remoto.

---

## 4. Clonar un repositorio

Cuando un repositorio ya existe en GitHub, podemos crear una copia en nuestra computadora utilizando:

```bash
git clone url_del_repositorio
```

El comando `git clone` descarga el proyecto y crea un repositorio local conectado con el repositorio remoto.

### `git init`

A diferencia de `git clone`, se utiliza cuando se quiere creae un repositorio nuevo desde una carpeta local

```bash
git init
```
---

## 5. Flujo básico de trabajo con Git
### Revisar el estado

```bash
git status
```
### Descargar cambios de GitHub

Antes de comenzar a trabajar, podemos actualizar nuestra copia local con:

```bash
git pull
```
### Preparar cambios

Cuando terminamos de modificar uno o varios archivos, utilizamos `git add` para enviarlos al Staging Area

Para un archivo específico:

```bash
git add archivo.md
```

Para agregar todos los cambios:

```bash
git add .
```

### Crear un commit

Luego guardamos esos cambios en el repositorio local mediante:

```bash
git commit -m "Descripción del cambio"
```

### Subir cambios a GitHub

```bash
git push
```

`git push` envía nuestros commits locales al repositorio remoto en GitHub.

---

## 6. Visual Studio Code

Visual Studio Code (VS Code) es el editor que podemos utilizar para trabajar con los archivos del repositorio

Dentro de VS Code se puede:

* abrir la carpeta del repositorio;
* crear y editar archivos;
* trabajar con archivos Markdown;
* utilizar una terminal integrada para ejecutar comandos de Git;
* observar los archivos modificados mediante Source Control;
* realizar commits y sincronizar cambios con GitHub.

---

## 7. Markdown

Es un lenguaje de marcado ligero que permite dar estructura y formato a documentos utilizando caracteres simples.

---

## 8. Sintaxis básica de Markdown

### Títulos y subtítulos

Los títulos se crean utilizando `#`.

```md
# Título principal

## Subtítulo

### Sección

#### Subsección
```

Mientras más símbolos `#` se utilicen, menor será el nivel del título.

### Negrita

```md
**Texto en negrita**
```

Resultado:

**Texto en negrita**

### Cursiva

```md
*Texto en cursiva*
```

Resultado:

*Texto en cursiva*

### Listas

Lista sin numeración:

```md
- Elemento 1
- Elemento 2
- Elemento 3
```

Lista numerada:

```md
1. Primer elemento
2. Segundo elemento
3. Tercer elemento
```

### Enlaces

```md
[GitHub](https://github.com)
```

La palabra mostrada queda asociada con la dirección indicada entre paréntesis.

### Imágenes

La sintaxis para incluir una imagen es similar a la de un enlace, agregando `!` al inicio.

```md
![Descripción de la imagen](ruta-de-la-imagen.png)
```

### Código en línea

Para escribir un comando o fragmento pequeño de código dentro de una oración se utilizan comillas invertidas:

```md
Utilizamos `git status` para revisar el estado del repositorio.
```

### Bloques de código

Para fragmentos más grandes se utilizan tres comillas invertidas.

Por ejemplo:

````md
```bash
git status
git add .
git commit -m "Actualizar resumen"
git push
```
````

También puede indicarse el lenguaje, como `bash`, para facilitar la lectura del código.

---

## 9. README.md

Presenta y documenta el proyecto para que una persona que ingrese al repositorio pueda entender de qué trata y cómo está organizado.
Por ejemplo:

```md
# Proyecto de Señales Médicas

Este repositorio contiene los trabajos desarrollados durante el curso.

## Integrantes

- Estudiante 1
- Estudiante 2
- Estudiante 3
```

GitHub interpreta automáticamente el contenido de `README.md` y lo muestra dentro de la página principal del repositorio, por lo que funciona como una presentación inicial del proyecto.

---
