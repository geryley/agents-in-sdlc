---
title: "Ejercicio 2: Añadir funcionalidad con el modo de agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Incluso las actualizaciones más sencillas de una aplicación suelen requerir cambios en varios archivos y tareas como ejecutar pruebas. El flujo habitual de desarrollo consiste en encontrar todos los archivos necesarios, hacer los cambios, ejecutar las pruebas, depurar, averiguar qué archivo falta, volver a actualizarlo... y así sucesivamente.

Para eso sirve el modo de agente de Copilot.

El modo de agente de Copilot está diseñado para trabajar de forma más autónoma en el IDE. Se comporta de forma parecida a un desarrollador: primero explora la estructura existente del proyecto, después aplica los cambios necesarios, ejecuta tareas como las pruebas y corrige automáticamente los errores que encuentra. Veamos cómo utilizarlo para añadir funcionalidad al sitio.

> [!NOTE]
> Aunque sus nombres son parecidos, el modo de agente y el agente en la nube están diseñados para experiencias distintas. El modo de agente realiza las tareas en el IDE y permite obtener feedback e interactuar rápidamente. El agente en la nube trabaja de forma asíncrona como un miembro del equipo y se comunica contigo mediante incidencias y solicitudes de incorporación de cambios.

En este ejercicio aprenderás a:

- utilizar el modo de agente de Copilot para explorar el proyecto, identificar archivos relevantes y coordinar los cambios;
- implementar nuevas funciones en la interfaz y en la capa de datos;
- revisar los cambios y las pruebas que genere el modo de agente antes de incorporarlos a la base de código.

## Escenario

A medida que crece la lista de juegos, quieres que las personas puedan filtrar por categoría y editorial. En el ejercicio anterior ya añadiste un helper de editoriales. Ahora completarás el resto del trabajo en la capa de datos, la interfaz y las pruebas con el modo de agente de Copilot.

## Ejecuta el sitio web Tailspin Toys

Antes de hacer cambios, exploremos el sitio web Tailspin Toys para conocer sus funciones actuales.

El sitio web es una plataforma de financiación colectiva para juegos de mesa con temática de desarrollo. Permite consultar juegos y ver sus detalles. Es una única aplicación Astro que genera páginas HTML estáticas durante la compilación. Las páginas consultan directamente una base de datos SQLite local mediante Drizzle ORM en sus metadatos: no hay una API de backend independiente ni un framework de interfaz de cliente. Los helpers reutilizables de acceso a datos están en `src/lib/`, y la interactividad se añade con un pequeño elemento `<script>` de Astro que utiliza las API estándar del DOM.

### Inicia el sitio web

Para facilitar el inicio del sitio, se ha configurado la tarea `npm run dev`, que ejecuta el único servidor de desarrollo de Astro. Sigue estos pasos para iniciarlo en GitHub Codespaces:

1. Vuelve al codespace. Después del ejercicio anterior, deberías estar en `main`.
2. Abre una ventana de terminal nueva en el codespace pulsando <kbd>Control</kbd>+<kbd>`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux).
3. Crea una rama nueva para el trabajo de filtrado y cámbiate a ella:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Ejecuta este comando para iniciar el sitio:

   ```bash
   npm run dev
   ```

   Cuando el servidor de desarrollo Astro esté listo, aparecerá un mensaje con la URL, similar a este:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Si aparece un cuadro de diálogo que te pide abrir una ventana del navegador para `http://localhost:4321`, ciérralo seleccionando **x**.

5. Abre el sitio web manteniendo pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y seleccionando la dirección del servidor Astro `http://localhost:4321` en el terminal.

> [!NOTE]
> En un codespace, al seleccionar un enlace localhost en el terminal de Codespaces, se redirige automáticamente a `https://<your-codespace-name>-4321.app.github.dev/`. Es un túnel privado hacia el codespace, que ahora aloja el servidor web.

### Explora el sitio web

Cuando el sitio esté en ejecución, podrás explorar sus funciones principales:

- **Página de inicio**: muestra una lista de juegos de mesa con títulos, imágenes y descripciones.
- **Página de detalles del juego**: al seleccionar un juego, se abre una página con más información, como el título, la descripción, la editorial y la categoría.

## Explora el backlog con Copilot

> [!TIP]
> **Abre Copilot Chat**
>
> Antes de empezar los ejercicios siguientes, vuelve al codespace, abre el panel Copilot Chat y selecciona **New Chat** para iniciar una conversación limpia. El modo y el modelo varían según el ejercicio; cada paso los indicará cuando sea relevante.

La implementación inicial del sitio funciona, pero queremos mejorarla con nuevas capacidades. Empecemos por revisar el backlog. Al crear el proyecto a partir de la plantilla, se generó automáticamente un backlog de incidencias. Pide a GitHub Copilot que te muestre esos elementos.

1. En el menú desplegable de agentes de la vista Chat, selecciona **Agent**. El agente planifica e implementa cambios en varios archivos de forma autónoma, ejecuta comandos de terminal e invoca herramientas.

   ![Captura del selector de agentes en la vista Chat.](../../../_images/shared-chat-mode-selector.png)

2. Selecciona **Claude Sonnet 4.5** en la lista de modelos disponibles.

> [!CAUTION]
> Los autores de este taller no expresan preferencia por un modelo en particular. Durante su creación utilizamos Claude Sonnet 4.5 y lo incluimos en las instrucciones para procurar que las sugerencias sean relativamente coherentes y la experiencia resulte adecuada. Sin embargo, como los LLM son probabilísticos, las sugerencias pueden diferir de las indicadas en el taller. Es normal y esperado.

> [!NOTE]
> Debido al carácter probabilístico de los LLM, Copilot puede utilizar un comando MCP distinto, pero debería poder completar la tarea.

3. Envía este prompt a Copilot para consultar el backlog de incidencias:

   ```plaintext
   Muéstrame el backlog de elementos de mi repositorio de GitHub. Ayúdame a priorizarlos según su utilidad para los usuarios.
   ```

4. Selecciona **Continue** para ejecutar el comando que enumera todas las incidencias.
5. Revisa la lista generada.

Observa que Copilot también ha priorizado las incidencias según las que considera más útiles para los usuarios.

## Revisa los archivos de instrucciones

Antes de pedir al agente que genere código, revisaremos el archivo de instrucciones que proporcionará contexto para su trabajo. Utilizaremos el archivo de instrucciones de [interfaz de usuario (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md), que explica cómo abordar la incorporación de funcionalidades al sitio.

1. En el codespace, abre `.github/instructions/ui.instructions.md`.
2. Observa las directrices generales sobre cómo añadir funcionalidad. Incluyen:
   - una descripción general de la arquitectura;
   - principios para diseñar componentes y facilitar las pruebas y la accesibilidad;
   - enlaces a instrucciones específicas para distintos tipos de archivo, incluidos:
     - Astro;
     - Tailwind CSS;
     - Drizzle.

> [!TIP]
> Los archivos de instrucciones pueden hacer referencia a otros archivos de instrucciones y a archivos del proyecto. Las rutas son relativas al archivo de instrucciones. Así puedes reutilizar contenido, dividir instrucciones complejas en partes más manejables y proporcionar ejemplos y plantillas.

## Implementa la funcionalidad de filtrado

Para completar los filtros, será necesario realizar al menos tres cambios en la aplicación:

- añadir o perfeccionar la lógica de filtrado restante en la capa de datos (`src/lib/`);
- añadir o actualizar pruebas para el comportamiento del filtrado;
- actualizar la página de listado de juegos para incorporar la interfaz de filtrado.

Además, las pruebas deben ejecutarse y superarse antes de combinar los cambios en la base de código. ¡El modo de agente de Copilot puede realizar estas tareas! Añadamos la funcionalidad.

1. Continúa en la conversación actual de Copilot o selecciona **New Chat** para iniciar otra.
2. Selecciona **Add Context**, **Instructions** y **ui** como archivo de instrucciones.

   ![Captura de la selección del archivo de instrucciones de UI](../../../_images/ex3-select-instructions-file.png)

3. Comprueba que **Agent** siga seleccionado en el menú desplegable de agentes de la vista Chat.

   ![Captura del selector de agentes en la vista Chat.](../../../_images/shared-chat-mode-selector.png)

4. Comprueba que **Claude Sonnet 4.5** siga seleccionado como modelo.
5. Pide a Copilot que implemente la funcionalidad según la incidencia relacionada del backlog:

   ```plaintext
   Actualiza el sitio para incluir filtros por editorial y categoría según los requisitos de la incidencia de GitHub correspondiente del backlog. Ya existe un helper de editoriales del ejercicio anterior; consérvalo y perfecciónalo según sea necesario mientras completas el resto de la capa de datos, la interfaz y las pruebas. Comprueba que todas las pruebas se superan antes de terminar. El servidor ya está en ejecución, así que no hace falta iniciarlo.
   ```

6. Observa cómo Copilot empieza explorando el proyecto y buscando los archivos relacionados con la funcionalidad. Debería encontrar tanto los helpers de la capa de datos y la interfaz como las pruebas. Después modificará los archivos y ejecutará las pruebas.

   ![Captura de Copilot explorando los archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot realizará varias tareas, como explorar el proyecto, modificar archivos y ejecutar pruebas. Según la complejidad de la tarea y la base de código, puede tardar unos minutos. Durante el proceso, podrían aparecer los botones **Keep** y **Undo** en el editor. Al terminar, habrá un botón **Keep** o **Undo** para cada cambio; no es necesario seleccionarlos mientras el trabajo siga en curso.

7. Cuando Copilot lo solicite, selecciona **Continue** para ejecutar las pruebas.

   ![Captura de un cuadro de diálogo en el panel Copilot Chat que pide confirmar la ejecución de las pruebas](../../../_images/ex3-agent-mode-run-tests.png)

8. Es posible que haya pausas y que algunas pruebas fallen durante el proceso. No pasa nada: Copilot alterna entre la generación de código y la ejecución de pruebas hasta terminar la tarea y no detectar errores.

   ![Captura de una sesión completa de Copilot Chat en modo de agente](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Explora el código generado para detectar posibles problemas.

> [!CAUTION]
> Recuerda que siempre es importante revisar el código generado por Copilot o cualquier otra herramienta de IA.

10. Vuelve al navegador donde está abierto el sitio y explora la nueva funcionalidad.
11. Cuando hayas comprobado que todo funciona y revisado el código, selecciona **Keep** en la ventana de Copilot Chat.

## Resumen y siguientes pasos

¡Enhorabuena! En este ejercicio has explorado cómo utilizar el modo de agente de GitHub Copilot para añadir nuevas funciones al sitio web Tailspin Toys. Has aprendido a:

- utilizar el modo de agente de GitHub Copilot para implementar nuevas funciones en la interfaz y la capa de datos;
- dejar que el modo de agente explore el proyecto, identifique archivos relevantes y coordine los cambios;
- revisar los cambios y las pruebas generadas antes de incorporarlos a la base de código.

Ahora [probaremos la función con el servidor MCP de Playwright][next-lesson] y abriremos una solicitud de incorporación de cambios.

### Ejercicio de exploración adicional: Implementa la paginación

A medida que crezca la lista de juegos, será necesario añadir paginación. Utiliza lo aprendido en este ejercicio para pedir a Copilot que la implemente. Ten en cuenta lo siguiente:

- sigue las prácticas recomendadas existentes, incluidos los archivos de instrucciones;
- decide cómo quieres implementar la paginación: por ejemplo, si las personas podrán seleccionar el tamaño de página o si será fijo;
- al redactar el prompt, proporciona a Copilot las directrices necesarias para que implemente la solución que deseas;
- quizá tengas que iterar con GitHub Copilot, pedir cambios y proporcionar contexto. Es lo habitual al trabajar con Copilot.

## Recursos

- [Copilot en los modos Ask, Edit y Agent: qué hacen y cuándo utilizarlos][choose-mode]
- [Modo de agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
