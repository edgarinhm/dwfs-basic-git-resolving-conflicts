# dwfs-basic-git-resolving-conflicts

Un ejemplo de conflicto de fusión es cuando dos desarrolladores trabajan en sus ramas dependientes. Ambos desarrolladores están trabajando en el mismo archivo llamado Feature.js. Cada una de sus tareas consiste en añadir una nueva característica a un método existente. El desarrollador 1 tiene una rama llamada feature1, y el desarrollador 2 tiene una rama llamada feature2. 
El desarrollador 1 empuja el código con los cambios al repositorio remoto. Desarrollador 2 empuja sus cambios.

Veamos cómo ocurriría esto en Git. Tanto el desarrollador 1 como el 2 consultan el repositorio principal el lunes por la mañana. Ambos tienen la misma copia. Ambos desarrolladores revisan una nueva rama - feature 1 y 2.


Developer1

    git pull
    git checkout -b feature1

Developer2

    git pull
    git checkout -b feature2

    
El desarrollador 1 hace sus cambios en un archivo llamado Feature.js y luego confirma los cambios en el repositorio para su aprobación a través de un PR (pull request)

    git add Feature.js
    git commit -m 'chore: added feature 1!!'
    git pull origin main
    git push -u origin feature1

Crear el PR revisarlo y luego fusionarlo en la rama principal. 
Mientras tanto, el desarrollador 2 está empezando a codificar en su característica. Una vez más, pasan por el mismo proceso que el desarrollador 1:

    git add Feature.js
    git commit -m “chore: added feature 2!!!”
    git pull origin main
 
    From github.com:demo/demo-repo
    * branch            main       -> FETCH_HEAD
      9012934..d3b3cc0  main       -> origin/main
    Auto-merging Feature.js
    CONFLICT (content): Merge conflict in Feature.js
    Automatic merge failed; fix conflicts and then commit the result.

Git nos hace saber que se ha producido un conflicto de fusión y necesita ser arreglado antes de que pueda ser empujado al repositorio remoto. Ejecutar git status también nos dará el mismo nivel de detalle:
  
    git status
    On branch feature2
    You have unmerged paths.
      (fix conflicts and run "git commit")
      (use "git merge --abort" to abort the merge)

    Unmerged paths:
      (use "git add <file>..." to mark resolution)
            both modified:   Feature.js

      no changes added to commit (use "git add" and/or "git commit -a")

Para fusionar, el desarrollador 2 necesita ver y comparar los cambios del desarrollador 1. Es una buena práctica ver primero qué rama está causando el problema de fusión. El desarrollador 2 ejecuta el siguiente comando:

    git log –merge

    commit 79bca730b68e5045b38b96bec35ad374f44fe4e3 (HEAD -> feature2)
    Author: Developer 2 
    <developer2@demo.com>
    Date:   Sat Jan 29 16:55:40 2022 +0000

        chore: add feature 2

    commit 678b0648107b7c53e90682f2eb8103c59f3cb0c0
    Author: Developer 1 
    <developer1@demo.com>
    Date:   Sat Jan 29 16:53:40 2022 +0000

        chore: add feature 1

Podemos ver en el código anterior que los cambios conflictivos del equipo se produjeron en las ramas de las características 1 y 2. El desarrollador 2 quiere ver ahora el cambio que está causando el conflicto.

    git diff
    
    diff --cc Feature.js
    index 1b1136f,c3be92f..0000000
    --- a/Feature.js
    +++ b/Feature.js
    @@@ -1,4 -1,4 +1,8 @@@
      let add = (a, b) => {
    ++<<<<<<< HEAD
    +  if(a + b > 10) { return 'way too much'}
    ++=======
    +   if(a + b > 10){ return 'too much' }
    ++>>>>>>> d3b3cc0d9b6b084eef3e0afe111adf9fe612898e
        return a + b;
      }

La única diferencia es la redacción de la sentencia return. El desarrollador 1 añadió "demasiado", pero el desarrollador 2 añadió "demasiado". Todo lo demás es idéntico en términos de fusión y es una solución bastante fácil. Git mostrará flechas <<< >>> para señalar los cambios. El desarrollador 2 elimina los marcadores para que el código esté listo para su envío:

    let add = (a, b) => {
      if(a + b > 10) { return 'way too much'}
      return a + b;
    }

    git add Feature.js
    git commit -m 'fix merge conflicts'
    git push -u origin feature2

El desarrollador 2 ha solucionado ahora un conflicto de fusión y puede crear su PR para que el código se fusione en la línea principal.