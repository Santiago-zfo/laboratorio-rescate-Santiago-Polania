# Bitácora del laboratorio de rescate

## Reto 1: Ver el historial
* **Comandos usados:** `git log`, `git log --oneline`, `git log --oneline --graph --all`
* **¿Qué hace cada comando?**
  * `git log`: Muestra el historial detallado de todos los commits realizados (hash completo, autor, fecha y mensaje completo).
  * `git log --oneline`: Resume el historial mostrando cada commit en una sola línea con su código corto y el título del mensaje.
  * `git log --oneline --graph --all`: Muestra el historial resumido acompañado de un gráfico ASCII lateral que permite visualizar las ramas, bifurcaciones y uniones de todo el repositorio.
* **¿Qué es el código que aparece al inicio de cada commit?**
  * Es el **hash SHA-1**, un identificador único generado por Git para cada commit que sirve para referenciarlo, inspeccionarlo o volver a esa versión exacta del proyecto.

---

## Reto 2: Ver qué cambió antes de guardar
* **Comandos usados:** `git diff`, `git add notas.txt`, `git diff --staged`, `git commit -m "docs: modifica línea 2 de notas"`
* **¿Por qué el segundo `git diff` no mostró nada?**
  * Porque `git diff` sin opciones compara únicamente el directorio de trabajo (*Working Directory*) contra el área de preparación (*Staging Area*). Al ejecutar `git add notas.txt`, los cambios pasaron al área de preparación y ya no había diferencias pendientes sin preparar.
* **¿Qué muestra `--staged`?**
  * Muestra las diferencias exactas entre los archivos que ya fueron agregados al área de preparación (`git add`) y el último commit guardado en el repositorio.

---

## Reto 3: "Borré un archivo por error"
* **Comandos usados:** `del notas.txt` (en PowerShell), `git status`, `git restore notas.txt`
* **¿Qué hace `git restore`?**
  * Descarta los cambios no guardados en el directorio de trabajo y restaura el archivo exactamente al estado en el que se encontraba en el último commit (o en el área de preparación).
* **¿Funcionaría si nunca hubieras hecho commit de ese archivo?**
  * No. Si el archivo nunca fue rastreado ni guardado en un commit (o al menos agregado al *staging area*), Git no tiene una copia previa registrada desde la cual recuperarlo.

---

## Reto 4: "Agregué un archivo que no debía subir"
* **Comandos usados:** `git add .`, `git status`, `git restore --staged secreto.env`, creación de `.gitignore` con `*.env`, `git add .gitignore`, `git commit -m "chore: agrega .gitignore para archivos .env"`, `git push`
* **¿Qué diferencia hay entre `git restore` y `git restore --staged`?**
  * `git restore <archivo>` deshace las modificaciones directamente en el archivo físico dentro de la carpeta del proyecto (borra los cambios locales). En cambio, `git restore --staged <archivo>` únicamente saca el archivo del área de preparación (*unstage*), pero conserva intacto el archivo y sus cambios en el disco.

---

## Reto 5: "Escribí mal el mensaje del commit"
* **Comandos usados:** `git add notas.txt`, `git commit -m "cambios"`, `git commit --amend -m "docs: agrega línea 4 a notas"`, `git log --oneline`, `git push`
* **¿Qué hace este comando?**
  * `git commit --amend -m "..."` reemplaza el último commit local por uno nuevo con el mensaje corregido (y cualquier archivo nuevo que se haya agregado al *staging*), evitando dejar mensajes poco descriptivos en el historial antes de subirlos.

---

## Reto 6: "Subí un commit que dañó el proyecto"
* **Comandos usados:** `git add notas.txt`, `git commit -m "fix: limpia notas"`, `git push`, `git revert HEAD`, `git push`
* **¿`git revert` borró el commit equivocado o creó uno nuevo?**
  * Creó un **commit nuevo** (`Revert "fix: limpia notas"`) que aplica exactamente los cambios inversos al commit anterior para devolver el archivo a su estado previo, sin eliminar el registro del error en el historial.
* **¿Por qué es la opción segura cuando ya se hizo push?**
  * Porque no reescribe ni altera el historial público existente. Si otros compañeros ya descargaron ese commit remoto, crear un nuevo commit de reversión evita conflictos de sincronización y pérdida de historial en el equipo.

---

## Reto 7: "Hice un commit antes de tiempo"
* **Comandos usados:** `git add borrador.txt`, `git commit -m "feat: agrega borrador"`, `git reset --soft HEAD~1`, `git add borrador.txt`, `git commit -m "feat: agrega borrador completo"`, `git push`
* **¿Qué diferencia hay entre `git reset --soft` y `git revert`? ¿Cuándo se usa cada uno?**
  * `git reset --soft HEAD~1` retrocede el puntero del historial eliminando el último commit, pero mantiene los archivos modificados listos en el área de preparación (*staging*). Se usa **únicamente en local**, cuando aún no se ha hecho `git push`.
  * `git revert` mantiene el commit original en el historial y crea un nuevo commit que deshace sus efectos. Se usa **cuando los cambios ya fueron subidos** a un repositorio remoto compartido.

---

## Reto 8: "Tengo trabajo a medias y debo cambiar de tarea"
* **Comandos usados:** `git stash`, `git status`, `git stash list`, `git switch -c fix/urgente`, `git switch main`, `git stash pop`, `git add notas.txt`, `git commit -m "docs: agrega línea 5 a notas"`, `git push`
* **¿Para qué sirve `git stash`?**
  * Guarda temporalmente en una pila oculta (*stash*) las modificaciones locales sin terminar (tanto rastreadas como en *staging*) y deja el directorio de trabajo limpio para poder cambiar de rama sin hacer commits incompletos.
* **¿Qué hace `git stash pop`?**
  * Recupera el último conjunto de cambios guardado en el *stash*, lo vuelve a aplicar sobre la rama actual y lo elimina de la lista de guardados temporales.

---

## Reto 9: Resultado final del historial
*(Reemplaza estas líneas pegando aquí la salida exacta de `git log --oneline --graph` en tu consola)*

```text
* xxxxxxx (HEAD -> main, origin/main) docs: agrega línea 5 a notas
* xxxxxxx feat: agrega borrador completo
* xxxxxxx Revert "fix: limpia notas"
* xxxxxxx fix: limpia notas
* xxxxxxx docs: agrega línea 4 a notas
* xxxxxxx chore: agrega .gitignore para archivos .env
* xxxxxxx docs: modifica línea 2 de notas
* xxxxxxx feat: agrega notas y bitácora
* xxxxxxx Initial commit