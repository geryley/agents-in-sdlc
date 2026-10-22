---
title: "Lección 3: Modos de agente: Plan y Autopilot"
description: "Utiliza Plan para acordar un enfoque, Autopilot para crear el filtrado a partir de una incidencia e Interactive para revisar el resultado."
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

Hemos empezado añadiendo una pequeña función al proyecto. Sin embargo, los cambios mayores requieren un proceso más completo. Por suerte, GitHub Copilot CLI se integra con los flujos de trabajo existentes de la organización para ayudarnos a crear lo adecuado de la forma correcta. Esta es la primera de varias lecciones en las que seguirás un proceso habitual de desarrollo con agentes: empezarás con una incidencia para crear una función, comprobarás que el código es válido y se comporta como se espera, y finalmente la combinarás correctamente en el proyecto.

> [!NOTE]
> Continuarás el flujo de trabajo de la función con la misma conversación y rama. Normalmente usarías ramas o solicitudes de incorporación de cambios distintas para los diferentes tipos de archivos, pero aquí tomaremos un atajo para centrarnos en los conceptos principales.

En esta lección:

- iniciarás una conversación de Copilot nueva desde una incidencia de GitHub;
- definirás los requisitos en el modo Plan;
- implementarás la nueva función con el modo Autopilot;
- revisarás el código;
- validarás manualmente la función en un navegador reenviado.

Durante el resto del trabajo actualizarás las instrucciones del repositorio, personalizarás la habilidad existente `quality-checks`, añadirás validación mediante MCP, crearás un agente de QA y abrirás la solicitud de incorporación de cambios de la función.

## Escenario

El catálogo de Tailspin Toys está creciendo y las personas necesitan filtrar los juegos por categoría y editorial. La incidencia del backlog describe la función, pero hay que acordar detalles —como la combinación de categorías— antes de programarla. Utilizarás el modo Plan para decidir estos puntos y después autorizarás una implementación acotada con Autopilot.

## Contexto

Incorporar agentes de programación con IA al flujo de desarrollo no cambia los fundamentos. ¡Si acaso, los hace aún más importantes! La mayoría de los desarrolladores siguen un proceso parecido a este:

1. Partir de una incidencia registrada que describa el trabajo.
2. Crear un plan de lo que se debe desarrollar.
3. Desarrollar y revisar el código.
4. Ejecutar pruebas para validar el código.
5. Validar manualmente la nueva funcionalidad.
6. Crear una solicitud de incorporación de cambios (PR).
7. Cuando se haya revisado el código y se superen las comprobaciones de integración continua, combinarlo.

> [!NOTE]
> Los detalles varían según el equipo y la organización, pero la mayoría sigue alguna variante del proceso anterior.

Al ceñirte a este enfoque, te aseguras de que el código generado por IA cumpla los requisitos y pase por el mismo proceso de evaluación que el código escrito manualmente.

## Modos de conversación

El **modo de conversación** controla el nivel de autonomía del agente. Pulsa <kbd>Shift</kbd>+<kbd>Tab</kbd> para alternar entre los modos:

- **Interactive**: tú y el agente colaboráis. El agente propone cambios y espera tus indicaciones antes de continuar.
- **Plan**: el agente crea primero un plan y no puede editar los archivos del proyecto.
- **Autopilot**: el agente trabaja de forma autónoma: escribe código, ejecuta pruebas e itera hasta completar la tarea.

Empieza en el modo Plan, revisa el plan y después utiliza Autopilot para implementarlo.

## Empieza por la incidencia

Antes de iniciar el trabajo de filtrado, vuelve a tu Codespace y comprueba que el repositorio y el terminal estén preparados.

1. Vuelve a tu Codespace. Si está detenido, reinícialo antes de continuar.
2. Confirma que la PR de las valoraciones con estrellas está combinada.
3. Si el terminal no está abierto, pulsa <kbd>Control</kbd>+<kbd>`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux).
4. Actualiza `main` y crea una rama para el trabajo de filtrado:

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Inicia Copilot CLI:

   ```bash
   copilot --yolo
   ```

6. Pulsa <kbd>Tab</kbd> dos veces para abrir la pestaña **Issues**.
7. Pulsa <kbd>A</kbd> para mostrar todas las incidencias.
8. Utiliza las teclas de flecha para resaltar la incidencia **Allow users to filter games by category and publisher**.
9. Pulsa <kbd>C</kbd> para añadir la incidencia al prompt y volver a la pestaña **Session**.

Observa que el prompt empieza ahora por `#7` (o un número similar). El carácter `#` permite añadir al contexto una incidencia o solicitud de incorporación de cambios de GitHub.

## Planifica la función de filtrado

La planificación permite definir el enfoque para implementar una función o realizar una tarea antes de asignársela a Copilot. Siempre conviene dedicar algo de tiempo a planificar cualquier tarea compleja. Cambiaremos al modo Plan y pediremos a Copilot que elabore el plan.

1. Pulsa <kbd>Shift</kbd>+<kbd>Tab</kbd> para cambiar al modo Plan. Comprueba que el indicador situado debajo del prompt muestre **Plan**.
2. Después de la referencia a la incidencia añadida en el paso anterior, escribe este prompt:

   ```plaintext
   Crea un plan para implementar esta función.
   ```

Copilot empezará a elaborar el plan: primero explorará el proyecto y después determinará el mejor enfoque.

3. Durante el proceso, quizá Copilot te pregunte cómo deben funcionar los filtros. Responde según tus preferencias; no hay respuestas incorrectas.
4. Cuando termine el plan, pulsa <kbd>Control</kbd>+<kbd>E</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) para ampliarlo.
5. Desplázate por el plan y revísalo.
6. Pide a Copilot que revise cualquier aspecto que no coincida con tus decisiones.

## Autoriza Autopilot

El plan está redactado y revisado; ahora toca implementarlo. Dejaremos que Copilot lo haga con el modo Autopilot.

Autopilot permite que Copilot itere sobre el problema hasta considerar que está resuelto.

1. Selecciona **Accept plan and build on autopilot (recommended)** o la opción equivalente en la versión instalada.
2. Comprueba que el indicador situado debajo del prompt muestre **Autopilot**.
3. Observa cómo Copilot sigue el plan, genera código y ejecuta pruebas.

> [!NOTE]
> Al aprobar el plan puede empezar la implementación inmediatamente, así que revísalo antes. Si Copilot indica que faltan dependencias o que hay un conflicto de puertos, resuelve el problema de configuración antes de dar las comprobaciones por completadas.

## Revisa y valida la implementación

Una vez generado el código, hay que revisarlo antes de combinarlo, como cualquier otro código. Revisemos el código y ejecutemos el sitio para comprobar que todo funciona.

1. Pulsa <kbd>Shift</kbd>+<kbd>Tab</kbd> para acceder al modo Interactive. Comprueba que el indicador ya no muestre **Plan** ni **Autopilot**.
2. Escribe `/diff` y revisa la implementación de los filtros y las pruebas.
3. Compara el resultado con la incidencia y con las decisiones de la planificación.
4. Cuando termines la revisión, pulsa <kbd>Esc</kbd> para salir de la vista de diferencias.
5. Revisa los resultados de las comprobaciones del proyecto y pide a Copilot que solucione los fallos.

## Explora la nueva función

El código parece correcto, pero ¿se ejecuta? Iniciemos la aplicación como antes y abramos el sitio mediante el puerto reenviado de Codespaces.

1. Pide a Copilot que inicie la aplicación:

   ```plaintext
   Inicia la aplicación para que pueda probar la función de filtrado en el navegador. Indícame la URL y deja el servidor en ejecución.
   ```

2. Cuando Codespaces indique que el puerto `4321` está disponible, selecciona **Open in Browser**.
3. Prueba los filtros por categoría, los filtros por editorial y las combinaciones acordadas en el plan.
4. Confirma que el restablecimiento y los resultados vacíos se comportan según la incidencia y tus decisiones.
5. Vuelve a tu Codespace y pide a Copilot que detenga el servidor de desarrollo que ha iniciado.

## Resumen y siguientes pasos

Has utilizado distintos modos de conversación para crear y revisar una función. En esta lección:

- has iniciado una conversación de Copilot desde una incidencia de GitHub;
- has definido los requisitos en el modo Plan;
- has implementado la función con Autopilot;
- has revisado el código;
- has validado manualmente la función en un navegador reenviado.

A continuación, profundizaremos en cómo generar código de acuerdo con prácticas documentadas mediante el uso de [instrucciones personalizadas][next-lesson].

## Recursos

- [Autopilot en GitHub Copilot CLI][autopilot]
- [Referencia de comandos de Copilot CLI][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
