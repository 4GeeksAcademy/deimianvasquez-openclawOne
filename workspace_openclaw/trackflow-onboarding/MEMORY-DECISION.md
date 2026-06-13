# MEMORY-DECISION - TrackFlow Onboarding

## 1) Decision de tipo de memoria

### Opcion elegida
Se elige usar principalmente la carpeta /memory (notas y registros persistentes en disco), complementada por Memory.md para reglas estables del agente.

### Justificacion
El onboarding requiere historial cronologico y estado por empleado entre sesiones. Eso encaja mejor con /memory porque:
- guarda eventos y transiciones de estado en archivos persistentes;
- permite recuperar procesos concretos por empleado;
- escala mejor cuando hay muchos onboardings simultaneos.
Memory.md se usa para politicas permanentes (reglas de clasificacion, restricciones y criterios de cierre), no para estado operativo diario.

## 2) Estrategia de busqueda

### Metodo
Se configura QMD como buscador de memoria, reemplazando el memory_search por defecto.

### Modos activos en QMD
- busqueda por palabras clave: activada
- similitud semantica: activada
- reranking: activado

### Coherencia con el caso de uso
- Exacta: para recuperar un empleado por identificador o correo.
- Textual: para localizar eventos concretos (por ejemplo "codigo verificacion enviado").
- Semantica: para consultas aproximadas de RRHH (por ejemplo "quienes llevan mas de una semana sin avanzar").

## 3) Amnesia de contexto: que debe recordar el agente si reinicia manana

## 3.1 Datos por empleado que deben sobrevivir
- identidad: nombre_completo, correo
- estado de proceso: no_iniciado, activo, terminado
- entregables recibidos: 1 a 6
- verificacion: codigo generado, pairing_aprobado
- fechas: fecha_inicio, fecha_ultimo_avance, fecha_cierre
- campos de negocio: operacion, turno, tallas EPI, formacion, supervisor

## 3.2 Datos globales que deben sobrevivir
- total de cambios de estado por dia
- ultimo corte para resumen matutino
- lista de procesos activos
- lista de procesos con mas de 24h sin avance

## 3.3 Donde se persiste cada dato
- reglas permanentes: Memory.md
- indice global de procesos: /memory/onboarding-index.json
- estado por empleado: /memory/employees/<employee_id>.json
- auditoria de transiciones: /memory/state-changes.log

## 3.4 Como se recupera
- consultas exactas (id/correo): busqueda exacta sobre archivos por empleado
- consultas textuales: QMD keyword search
- consultas aproximadas: QMD semantic search + reranking

## 4) Politica de escritura persistente

Se escribe en disco inmediatamente cuando ocurre cualquiera de estos eventos:
- RRHH reporta nuevo empleado
- envio de email de bienvenida
- primer contacto del empleado por Telegram
- generacion/envio de codigo de verificacion
- aprobacion o rechazo de pairing
- recepcion de cada entregable
- cambio de estado
- cierre de proceso

No se considera valido depender del historial de chat para restaurar estado.

## 5) Verificacion tras reinicio (evidencia requerida)

## Escenario de prueba
1. Crear empleado de prueba y dejarlo en estado activo con al menos 2 entregables.
2. Reiniciar agente/proceso OpenClaw.
3. Consultar estado del empleado y resumen global.
4. Confirmar que:
   - el estado se conserva;
   - los entregables previos siguen presentes;
   - el resumen matutino cuenta correctamente ese proceso;
   - no se solicititan de nuevo datos ya registrados.

## Resultado de prueba (completar por estudiante)
- Empleado de prueba:
- Estado antes del reinicio:
- Estado despues del reinicio:
- Consulta QMD usada:
- Resultado devuelto por QMD:
- Conclusiones:
