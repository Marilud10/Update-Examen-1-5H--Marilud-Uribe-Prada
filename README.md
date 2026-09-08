# Examen 1

# TutorBot - Sistema de Puntos

Agregué la funcionalidad de puntos por cumplimiento al bot de tutorías. Aquí dejo qué hice y por qué.

## Cómo funciona

Tengo 2 workflows:

- **TutorBot - Puntos por Cumplimiento**: corre todos los días a las 7am, calcula los puntos de cada estudiante según sus tutorías finalizadas y actualiza la hoja de Google Sheets. También notifica por Telegram si alguien ganó un regalo.
- **Bot-Principal**: es el bot con el que hablan los estudiantes en Telegram. Usa un AI Agent para entender qué quieren (agendar, ver o cancelar tutorías, y ahora también consultar puntos).

## Cálculo y guardado de puntos

Esto ya lo tenía resuelto en el nodo `Calcular Puntos y Regalos1`: cuenta las tutorías con `estado = "Finalizada"` de cada estudiante y multiplica por 10. Después `Actualizar Puntos del Estudiante1` guarda el resultado en la hoja `ESTUDIANTES`.

No lo toqué. Dos detalles que quiero recordar:

- Usé un nodo Code en vez de Set/Edit Fields porque necesitaba recorrer varios estudiantes y tutorías a la vez.
- En vez de sumar puntos nuevos al balance, recalculo el total completo cada vez. Así si el cron corre dos veces no se duplican puntos.

## Opción "Ver mis puntos" en el bot

El menú del bot no es un switch con opciones fijas, es el Agent el que decide qué hacer según lo que escribe el estudiante. Para agregar esto tuve que:

1. Clonar el nodo `consultar_mis_tutorias` y renombrarlo a `consultar_mis_puntos`, apuntando a la hoja `ESTUDIANTES` en vez de `TUTORIAS`.

2. Configurarle el filtro por `ID_ESTUDIANTE` usando `fromAI` para que el Agent lo resuelva solo con el contexto de la sesión activa.

3. Escribirle una Tool Description clara para que el Agent sepa cuándo usarla.

4. Conectarlo como tool del `TutorBot Agent`, igual que las demás herramientas.

5. Actualizar el System Message del Agent (sin borrar nada de lo que ya tenía) agregando:

   - La nueva frase del menú ("Quiero ver mis puntos")
   - La tool nueva en la lista de herramientas disponibles
   - El punto 4 del flujo, con el formato exacto de respuesta:

   ```
   Hola [Nombre], actualmente tienes 🏆 [Puntos] puntos acumulados por tu constancia académica. ¡Sigue asistiendo a tus tutorías!
   ```

## Pendiente

- Confirmar que el botón del menú de Telegram tenga la opción "4. Ver mis Puntos" con el texto exacto que espera el Agent.
- Probar todo el flujo: marcar una tutoría como Finalizada → correr el workflow de puntos → preguntarle al bot "quiero ver mis puntos" → confirmar que el número coincide.
- Revisar el caso de un estudiante con 0 puntos (recién registrado).


Foto Telegram

<img width="383" height="511" alt="image" src="https://github.com/user-attachments/assets/108dc105-f054-4c21-ada6-6054256da94a" />

Foto N8n (la parte donde se agrego un nuevo nodo)

<img width="1162" height="550" alt="image" src="https://github.com/user-attachments/assets/3db15cd0-8744-4f07-acdb-967715156416" />

n8n Sistema de puntos

<img width="1189" height="596" alt="image" src="https://github.com/user-attachments/assets/d700a48c-48ce-45ae-b9d8-fc322f4d4860" />

Google Sheet
- Puntos por tutorias Finalizadas
<img width="536" height="457" alt="image" src="https://github.com/user-attachments/assets/b7ada110-f571-4ded-9e56-011b58d2dc22" />


