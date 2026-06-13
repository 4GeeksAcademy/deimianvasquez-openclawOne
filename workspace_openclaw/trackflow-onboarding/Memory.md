# Memory - TrackFlow Onboarding

## Objetivo
Persistir en disco el estado de onboarding por empleado para sobrevivir reinicios y evitar amnesia de contexto.

## Estructura de almacenamiento
- Registro global de procesos: memory/onboarding-index.json
- Registro por empleado: memory/employees/<employee_id>.json
- Historial de cambios de estado: memory/state-changes.log

## Esquema por empleado
- employee_id
- nombre_completo
- correo
- operacion
- turno
- talla_guantes
- talla_chaleco
- formacion_seguridad_completada
- formacion_seguridad_fecha
- supervisor_confirmado
- supervisor_nombre
- estado (no_iniciado | activo | terminado)
- fecha_inicio
- fecha_ultimo_avance
- fecha_cierre
- entregables_recibidos (array)
- codigo_verificacion_hash
- pairing_aprobado (boolean)

## Reglas de persistencia
- Escribir en disco en cada transicion de estado.
- Escribir en disco en cada entregable recibido.
- Escribir en disco al generar codigo y al aprobar pairing.
- Nunca depender del historial de chat para reconstruir estado.

## Reglas de clasificacion
- no_iniciado: reportado por RRHH, sin contacto del empleado.
- activo: contacto iniciado o onboarding en curso, entregables incompletos.
- terminado: seis entregables completos y validaciones cumplidas.

## Resumen matutino
Cada manana calcular:
- total no_iniciado
- total activo
- total terminado
- cambios de estado desde el dia anterior
- procesos con mas de 24h sin avance

## Politica de recuperacion tras reinicio
Al iniciar sesion:
1. Cargar onboarding-index.json
2. Cargar registros por empleado
3. Recalcular clasificacion y pendientes
4. Continuar procesos activos sin pedir datos ya registrados