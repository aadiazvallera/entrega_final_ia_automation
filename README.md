# Ecosistema de Automatización IA con Control

Entrega Final - IA Automation

## Caso de uso
Generación de posts de LinkedIn para un cliente ficticio (DataPyme). 
El sistema redacta el borrador con IA usando las directrices de marca del cliente y se detiene en un punto de validación humana: nada se envía hasta que una persona lo revisa.

## Stack
- Orquestador: Make
- Base de datos: Airtable (memoria y registro del sistema)
- Motor de IA: COMPLETAR_MODELO (Max Tokens limitado a 800)
- Canal de salida: Gmail

## Escenario A - Generación y revisión
1. **Trigger**: Airtable > Watch Records sobre la vista "Para generar" (Estado = Generando), con el campo Última modificación.
2. **Filtro "Validar Idea"**: "length(Idea) >= 5" (operador numérico). Si la Idea está vacía o es muy corta, el flujo se corta.
3. **Traer Cliente**: obtiene Nombre, Email y Tono del cliente.
4. **Buscar directrices**: trae las directrices de marca de ese cliente.
5. **Unir directrices**: las junta en un solo texto (Text aggregator).
6. **Redactar post con IA**: prompt dinámico con la Idea, el Tono y las directrices. La IA usa solo esa información.
7. **Guardar borrador**: escribe el Borrador y los Tokens usados, y pone el Estado en "Procesado por IA".
8. **Pedir aprobación**: Gmail envía el borrador al cliente para su revisión y guarda el Gmail Thread ID.
9. **Pasar a revisión**: Estado = "En revisión". El flujo termina acá y espera la aprobación humana.

## Punto HITL
El escenario siempre termina en "En revisión". Una persona recibe el mail con el borrador y, si está de acuerdo, cambia el Estado a "Aprobado por Humano" en Airtable.

## Manejo de errores
- Error handler en el módulo de IA: si falla la API, registra el detalle en la tabla Log de Errores, marca la fila con Estado = Error y cierra con la directiva Break (Retry).
- Datos incompletos: el filtro Validar Idea corta la ejecución.
- Anti-bucles: al terminar, el Estado deja de ser "Generando", así que la fila sale de la vista del trigger y no se reprocesa.

## Base de datos (Airtable)
- **Clientes**: Nombre, Email del cliente, Tono preferido.
- **Contenidos**: Idea, Cliente (relación), Estado, Borrador, Aprobado, Comentarios de revisión, Fechas, URL publicada, Última modificación, Gmail Thread ID, Tokens usados, Motivo de error.
- **Directrices de Marca**: Tema, Categoría, Contenido, Cliente (relación).
- **Log de Errores**: Error, Módulo que falló, Mensaje, Contenido (relación), Fecha.

Ciclo de estados: Pendiente > Generando > Procesado por IA > En revisión > Aprobado por Humano > Enviado (y Error ante un fallo).

## Pruebas realizadas
- Camino feliz: COMPLETAR_CANTIDAD ejecuciones exitosas.
- Camino infeliz: fila con Idea muy corta, frenada por el filtro.
- Fallo de la API de IA forzado: registro creado en Log de Errores.

## Contenido del repositorio
- ["docs/": diagrama de arquitectura (PDF)](https://drive.google.com/file/d/1seZJYd4BTOEqOJaCTGof8moSUg-sIQqv/view?usp=sharing)
- ["blueprints/":](https://drive.google.com/file/d/15wHiccLWvWMDXbEymblRqz5VpAjdS2DP/view?usp=sharing) 
- "screenshots/": <img width="1232" height="337" alt="image" src="https://github.com/user-attachments/assets/f56c88b0-c753-4f21-bb5a-5be750e2fe11" />
- <img width="544" height="575" alt="image" src="https://github.com/user-attachments/assets/1fff9511-19f7-4386-8be3-fc0eaf36d344" />
- <img width="525" height="511" alt="image" src="https://github.com/user-attachments/assets/d13ea5cf-25d8-47a2-8852-f90080c4a353" />
- <img width="528" height="575" alt="image" src="https://github.com/user-attachments/assets/99795051-7ec0-43b6-a672-29f4e918dc25" />
- <img width="491" height="339" alt="image" src="https://github.com/user-attachments/assets/7d95507c-1707-4264-9a9d-f1bf0a9cfcd3" />
- <img width="492" height="577" alt="image" src="https://github.com/user-attachments/assets/4df28ebe-7cda-49a0-bc97-0d35c1d582d2" />
- <img width="412" height="576" alt="image" src="https://github.com/user-attachments/assets/947c2f01-c79c-4077-95e2-2fe2beab456b" />
- <img width="510" height="579" alt="image" src="https://github.com/user-attachments/assets/d4095961-49ee-4ed4-9ffc-f120bbc8ba73" />
- <img width="511" height="584" alt="image" src="https://github.com/user-attachments/assets/063753b1-8b86-47b2-9857-4a7444af4518" />
- <img width="442" height="584" alt="image" src="https://github.com/user-attachments/assets/ebb2aa48-3407-4508-80e8-d7f2534b9746" />
- <img width="527" height="594" alt="image" src="https://github.com/user-attachments/assets/cebb593a-071f-4d17-a295-9e64ddc4c2a0" />
- <img width="521" height="584" alt="image" src="https://github.com/user-attachments/assets/5c45331b-7495-4d90-b2a5-795d1cfa5b3e" />


## Enlaces
- [Base de datos en Airtable (solo lectura): ](https://airtable.com/invite/l?inviteId=invjl8I9dE6EgO6hb&inviteToken=fa8e814a02d0eaebdd31030cc741c559966f49dad761d08143c60e5259789a49&utm_medium=email&utm_source=product_team&utm_content=transactional-alerts)
- [Video demo (3 min):](https://www.loom.com/share/9a3693b36c8744d59bd97bac618cad89)
