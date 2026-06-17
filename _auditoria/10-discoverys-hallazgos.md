---
title: Cruce Discovery Sessions × Flujos del Manual
generado: 2026-06-17
fuente_discovery: Notion DB "📅 0. Sesiones de Discovery"
fuente_flujos: docs/manual/00-inventario.md (82 flujos, 9 dominios)
sesiones_analizadas: DISC002, DISC003, DISC032, DISC034
sesiones_pendientes: DISC001, DISC006, DISC016, DISC033, DISC039, DISC043, DISC046, DISC047, DISC048, DISC050 (y más)
---

# Cruce Discovery Sessions × Flujos del Manual Operativo

## Contexto

Este documento es el resultado de un análisis de brecha (_gap analysis_) entre las sesiones de Discovery formalizadas en la base de Notion y los flujos técnicos documentados en el Manual Operativo (`docs/manual/00-inventario.md`, 82 flujos en 9 dominios).

**Por qué existe.** Las sesiones de Discovery capturan reglas de negocio validadas con stakeholders operativos y directivos (COO, CFO, gerentes de sucursal, médicas, Contact Center). El Manual Operativo captura cómo el sistema _implementa_ esas reglas. Cuando ambas fuentes no están alineadas, hay riesgo directo de que el sistema aprobado en Discovery no se haya construido, o de que el sistema construido nunca fue validado como comportamiento esperado.

**Cómo usar este documento.**

- La **Sección 1** es el mapa de trazabilidad: cada hallazgo de Discovery contra su flujo técnico.
- La **Sección 2** son los flujos huérfanos de validación de negocio — riesgo técnico sin respaldo de producto.
- La **Sección 3** son los requerimientos de negocio sin implementación documentada — deuda funcional.
- La **Sección 4** son las discrepancias directas entre Discovery y el código — casos de prueba prioritarios.
- La **Sección 5** y **Sección 6** guían los próximos pasos del equipo QA y Producto.

Alcance: **4 sesiones analizadas** (DISC002, DISC003, DISC032, DISC034). Las sesiones pendientes están priorizadas en la Sección 5. Los números de flujo (`Agenda #07`, `Config #01`, etc.) referencian el índice de `docs/manual/00-inventario.md`.

---

## 1. Mapeo Discovery → Flujos del Manual

| DISC ID | Hallazgo / Regla | Flujo del Manual | Dominio | Observación |
|---|---|---|---|---|
| DISC032-H-04 | Sistema no valida empalme del mismo paciente en diferentes cabinas | Agenda #12 (FSM estado de cita) | Agenda y Citas | La validación existe para el estado de cita, pero no hay flujo documentado de validación de empalme de paciente entre espacios físicos. Gap parcial. |
| DISC032-H-03 | Sobreagenda virtual (Cabina 3 virtual, Coyoacán) | Agenda #05, #06 (slots disponibles) | Agenda y Citas | Los endpoints de slots existen pero no modelan el concepto de "recurso virtual" ni la excepción por-sucursal. Gap de modelado. |
| DISC032-H-08 / RN-1.1 | Horarios recurrentes configurados exclusivamente por Finanzas | Config #01 (configuración de horarios y scheduling) | Config y Administración | Flujo Config #01 existe pero no documenta el control de acceso por rol financiero vs gerentes. |
| DISC032-RN-1.3 / DISC034-RN-1.3 | Gerentes solo pueden hacer bloqueos emergentes; cambios recurrentes requieren Eduardo | Agenda #14, #15 (solicitud y registro de bloqueos) | Agenda y Citas | Flujos #14 y #15 cubren la solicitud y el registro, pero no modelan explícitamente la restricción de recurrencia por perfil Finanzas. |
| DISC034-RN-1.1 | Solo médicos pueden solicitar bloqueos (no registrar) | Agenda #14 (solicitud de bloqueo por médica) | Agenda y Citas | Flujo #14 lo cubre parcialmente. No documenta el canal de solicitud (correo) ni la prohibición explícita de auto-bloqueo técnico. |
| DISC034-RN-1.2 / RN-1.3 | Roles autorizados para registrar y eliminar bloqueos (gerente, supervisor, coordinador; Contact Center NO puede eliminar los de sucursal) | Agenda #15, #19 | Agenda y Citas | Los flujos existen pero no documentan la restricción específica de Contact Center vs bloques generados en sucursal. Gap de RBAC documentado. |
| DISC034-RN-2.1 | Catálogo único homologado de tipos de bloqueo (personales, corporativos, especial lactancia) | Agenda #14, #15 | Agenda y Citas | Ningún flujo del manual documenta el catálogo ni sus categorías. El tipo "lactancia con bono" es completamente nuevo. |
| DISC034-RN-3.1 | Bloqueos puntuales y recurrentes (semanal, mensual) | Agenda #14, #15, #19, #20 | Agenda y Citas | FSM #19 y #20 modelan estados pero no modelan explícitamente la dimensión de recurrencia como atributo del bloqueo. |
| DISC034-RN-4.1 | Bloqueo automático de Agenda 2 al agendar procedimiento exclusivo en Agenda 1 (doble agenda) | Agenda #07 (quick-booking) | Agenda y Citas | Flujo #07 no menciona la lógica de doble agenda ni el bloqueo automático cruzado entre agendas. Gap significativo. |
| DISC034-RN-5.1 | Coordinación manual entre gerentes para médicos multisucursal al aplicar bloqueo | Agenda #14, #15 | Agenda y Citas | Ningún flujo documenta la propagación de bloqueo entre sucursales para médicos itinerantes. Solo Agenda #06 cubre slots multi-sucursal. |
| DISC034-RN-6.1 / RN-6.2 | Trazabilidad obligatoria de creación y eliminación de bloqueos (usuario, fecha, hora, rol) | Procesos Async #03 (outbox), Agenda #19 | Agenda y Citas | El FSM #19 no documenta el requerimiento de audit trail específico para eliminación de bloqueos. Gap de trazabilidad. |
| DISC034-RN-7.1 | Front 2.0 debe permitir bloqueo directo sobre citas existentes (eliminar paso "indisponibilidad") | Agenda #14, #15 | Agenda y Citas | Flujos #14 y #15 no documentan el manejo del caso de bloqueo sobre citas ya agendadas ni el flujo de reagenda que dispara. |
| DISC034-H-06 | Bloqueo de Agenda 2 en doble agenda es manual, riesgo de olvido | Agenda #07 (booking) | Agenda y Citas | Gap confirmado: flujo #07 no cubre este escenario. |
| DISC034-H-12 | Reagenda múltiple manual 1:1 cuando médico cancela mismo día con muchas citas | Agenda #09 (reasignación masiva) | Agenda y Citas | Flujo #09 cubre reasignación masiva de citas huérfanas pero no el escenario específico de cancelación masiva por bloqueo de médico con canal de contacto al paciente. |
| DISC002-RN-SVC-001 | Precio base por sucursal específica, NO por región | Agenda #07, Config #01 | Agenda / Config | Ningún flujo del manual documenta la lógica de pricing multi-sucursal. Es un dominio de "Servicios/Pricing" completamente ausente del manual. |
| DISC002-RN-SVC-002 | Múltiples SKUs por servicio (día semana, rol médico, horario) | Agenda #07 (booking) | Agenda y Citas | El booking asume precio único. El modelado multi-SKU no está documentado como flujo. |
| DISC002-RN-SVC-003 | Factor médico aditivo (+100 director, +50 jefe clínico) | Agenda #07 | Agenda y Citas | No cubierto en ningún flujo del manual. |
| DISC002-RN-SVC-004 / DISC003-RN-4.1 / RN-4.2 | Respetar precio de agendación si reagenda dentro de 15 días naturales | Agenda #08 (reagendado) | Agenda y Citas | Flujo #08 documenta reagendado pero no menciona protección de precio original. Gap de lógica de negocio. |
| DISC002-RN-SVC-005 / RN-SVC-006 / DISC003-RN-1.1 / RN-1.2 | Modificación de cita hasta fin de duración programada; no-show exactamente 1 minuto después | Agenda #12 (FSM estados), Agenda #13 (cron auto-noshow) | Agenda y Citas | Flujo #13 documenta el cron pero la regla de "duración completa como ventana" no está explicitada. Hay riesgo de diferencia entre lo que hace el cron (cada 5 min) y la regla exacta del minuto de fin. |
| DISC002-RN-SVC-007 | Cobro flexible pre/post servicio según sucursal | No existe flujo de Cobro en el manual | Cobro | Dominio de Cobro completamente ausente del manual de flujos. |
| DISC002-RN-SVC-008 | Laboratorio sin preparación = reagendar, no cancelar ni reembolsar | No existe flujo de Cobro/Lab | Cobro / Agenda | Ausente del manual. |
| DISC002-RN-SVC-009 | Servicios no médicos (vacunas, labs, cabinas) no bloquean agenda de personal específico | Agenda #05, #06 (slots) | Agenda y Citas | Flujos de slots no documentan esta excepción de asignación de personal. |
| DISC002-RN-SVC-010 | Membresías NO son servicios (son productos) | No existe en el manual | Sin dominio | Dominio de Membresías/Productos completamente ausente. |
| DISC002-RN-SVC-011 | Hospitalización: clínica rastrea solo 5 roles; gestión operativa es de OSPI | No existe en el manual | Sin dominio | Dominio de Hospitalización completamente ausente. |
| DISC003-H-01 | Variabilidad en timing de cobro entre sucursales | No existe flujo de Cobro | Cobro | Ausente del manual. |
| DISC003-H-03 | No-show automático al terminar duración, no a los 10 min del inicio | Agenda #13 (cron auto-noshow) | Agenda y Citas | Flujo #13 usa cron cada 5 min pero la regla exacta del trigger no está documentada con este nivel de precisión. |
| DISC003-H-05 | Período de gracia para precios al reagendar: implementado con códigos descuento manuales | Agenda #08 | Agenda y Citas | Gap entre comportamiento AS-IS manual y la regla de negocio documentada en DISC003. |
| DISC003-H-06 | Complementos en tickets separados generan sobrepago en honorarios médicos | No existe flujo en el manual | Cobro | Ausente. Afecta honorarios médicos como dominio separado. |
| DISC003-H-07 | Control manual de sesiones de paquetes prepagados (notas manuales en ML) | Contratos #04 (listener cierre automático) | Contratos y Planes | El listener de cierre automático asume control sistémico. El AS-IS de ML con notas manuales no está modelado como riesgo de migración. |
| DISC003-H-08 / RN-5.1 / RN-5.2 / RN-5.3 | Devoluciones con proceso burocrático (15 días, 3 formatos, límite efectivo configurable, autorizadores) | No existe flujo de Devoluciones | Cobro | Dominio de Devoluciones completamente ausente del manual. |
| DISC003-RN-2.3 / H-10 | Decisión Clip vs terminal bancaria según factura y autorización Finanzas; validar disponibilidad antes de mostrar | No existe flujo de Cobro | Cobro | Ausente. Impacta UX del módulo de cobro. |
| DISC003-RN-3.1 / RN-3.2 | Agregar complementos a cita existente; comunicación médico-recepción de complementos | No existe flujo de Cobro | Cobro | Ausente. Impacta flujo de consulta y cobro. |
| DISC003-RN-6.1 / RN-6.2 | Control sistémico de sesiones de paquetes con alertas de agotamiento + vinculación a paquete original | Contratos #04 | Contratos y Planes | Contratos #04 documenta cierre automático pero no el control de sesiones restantes ni las alertas. Gap funcional. |
| DISC003-RN-7.1 / RN-7.2 | Valoración médica obligatoria previa para procedimientos en consultorio; lista de procedimientos pendiente de Dirección Médica | No existe flujo de Cobro/Validación médica | Cobro / Agenda | Ausente del manual. Prerrequisito de negocio crítico para agendamiento. |
| DISC032-RN-6.1 | Cotejo de patología mensual: 3 fuentes cruzadas, folios consecutivos sin salto | No existe en el manual | Sin dominio | Dominio de Patología/Laboratorios completamente ausente. |
| DISC032-RN-5.1 | Agendamiento de laboratorios es informativo, no de control de tiempo; múltiples estudios en un solo slot | No existe en el manual | Sin dominio | Ausente del manual de flujos. |
| DISC032-RN-4.1 / RN-4.2 | Comisiones de cosmetólogas por sesión realizada (no agendada) + por paquetes vendidos durante procedimiento | No existe en el manual | Sin dominio | Dominio de Comisiones completamente ausente. |
| DISC032-H-02 | Criterio de asignación de cosmetólogas: equidad por turno de entrada | No existe en el manual | Sin dominio | Ausente del manual. |
| DISC034-H-07 | Sin integración Bookings↔Doctoralia para bloqueos (aviso manual a Mirari) | No existe en el manual | Sin dominio / Integraciones | Doctoralia no está documentada como integración en ningún flujo. |

---

## 2. Flujos del Manual sin respaldo de Discovery

Los siguientes flujos existen en el manual técnico pero no tienen sesión de Discovery analizada que los respalde formalmente. Representan decisiones técnicas tomadas sin validación de negocio documentada — riesgo QA de primer orden: el sistema puede comportarse de manera que los usuarios operativos no esperan o no aprobaron.

| Flujo | Dominio | Observación de riesgo |
|---|---|---|
| Auth #01–#11 (todos) | Autenticación e Identidad | Once flujos de auth/identity sin DISC analizada. La invariante `platform_users.id === Cognito sub` (Auth #03) es una decisión de arquitectura sin validación de negocio. Si cambia el proveedor de identidad, no hay sesión de Discovery que defina el comportamiento esperado. |
| Pacientes #02 (Merge de duplicados) | Gestión de Pacientes | Proceso complejo con whitelist de columnas permitidas para merge. Sin Discovery que defina qué campos debe ganar cuál registro — la lógica actual es una suposición técnica. |
| Pacientes #04 (Migración masiva desde Zoho) | Gestión de Pacientes | Proceso BPMN multi-paso. DISC002/003 mencionan Zoho tangencialmente; no hay DISC dedicada a migración de datos históricos ni a los criterios de reconciliación. |
| Pacientes #07 (Historial paginado portal) | Gestión de Pacientes | Sin Discovery de portal de paciente analizada. La experiencia de autoservicio del paciente carece de validación de usuario. |
| Pacientes #08 (Confirmación por link HMAC) | Gestión de Pacientes | Decisión técnica (token HMAC firmado) sin Discovery que exija este mecanismo de confirmación específico. |
| Agenda #10 (Booking portal OTP) | Agenda y Citas | El portal de paciente y el flujo OTP tienen múltiples flujos técnicos sin DISC de respaldo analizada. |
| Agenda #11 (Slots portal con rate-limit IP) | Agenda y Citas | Rate limit por IP: decisión técnica de seguridad sin validación de negocio. Podría afectar pacientes en redes corporativas o compartidas. |
| Contratos #01–#07 (todos) | Contratos y Planes | Siete flujos de contratos/planes (FSM del contrato, listener de cierre automático, ruta clínica, asignación de consejera) sin ninguna DISC analizada. La consejera es un actor crítico cuya operativa no tiene validación de negocio documentada. |
| Bot WA #01–#28 (todos) | Bot WhatsApp | Veintiocho flujos del bot sin ninguna DISC analizada. La cascada de disponibilidad, el sistema OTP WA, handoffs, encuesta post-conversación: ninguno tiene validación de negocio documentada. Es el canal de autoservicio principal del paciente. |
| Notificaciones #01–#08 (todos) | Notificaciones | Ocho flujos de notificaciones sin Discovery analizada. Los canales, el consentimiento LFPDPPP, los templates Meta aprobados: son decisiones con implicación legal no respaldadas por sesión de negocio. |
| Config #02 (Blacklist de pacientes) | Config y Administración | Sin Discovery que defina las reglas operativas de blacklist: quién la puede crear, con qué criterios, qué sucede con citas existentes. |
| Config #03 (Creación de beneficios) | Config y Administración | Sin Discovery de beneficios analizada. El modelo de datos de beneficios puede no reflejar la operativa real. |
| Config #04 (Sugerencias de espacios alternativos) | Config y Administración | El algoritmo de sugerencia de espacios no tiene Discovery de respaldo. Las reglas de priorización son asunciones técnicas. |
| Procesos Async #03 (Outbox Zoho) | Procesos Asíncronos | Patrón técnico LWW (_last-write-wins_) per-field sin Discovery que defina qué campos de Zoho deben sincronizarse ni cuál sistema es fuente de verdad por campo. |
| Procesos Async #06 (Webhook inbound Zoho) | Procesos Asíncronos | Sin Discovery de integración Zoho como flujo de negocio. |
| Procesos Async #07 (Webhook Mindbody) | Procesos Asíncronos | Sin Discovery de Mindbody como flujo de negocio. Las reglas de migración y convivencia entre sistemas no están documentadas. |
| Procesos Async #08 (Webhook Meta templates) | Procesos Asíncronos | Gestión de templates WhatsApp aprobados: decisión técnica sin Discovery que defina el proceso de aprobación, versionado o rollback de templates. |

---

## 3. Hallazgos de Discovery sin flujo documentado

Requerimientos capturados en sesiones de Discovery con validación de stakeholders que no tienen flujo correspondiente en el manual técnico. Representan funcionalidad potencialmente no implementada, implementada de forma diferente a lo aprobado, o implementada pero no documentada.

### Dominio: Cobro — 0 de 18 reglas cubiertas en el manual

El dominio de Cobro está completamente ausente del manual técnico. Las 18 reglas siguientes fueron validadas por COO y CFO.

| DISC ID | Hallazgo | Impacto estimado | Recomendación |
|---|---|---|---|
| DISC003-RN-2.1 | Timing de cobro flexible pre/post servicio por sucursal | Alto — si el sistema no soporta cobro post-servicio, sucursales que operan así quedan bloqueadas | Documentar el flujo de cobro con su variante pre/post y el campo de configuración por sucursal. |
| DISC003-RN-2.2 | Múltiples métodos de pago en un solo cobro (suma exacta al monto total) | Alto — pacientes con pagos mixtos (efectivo + tarjeta) no pueden ser atendidos en caja | Crear flujo Cobro #01: registro de pago con split de métodos. |
| DISC003-RN-2.3 | Decisión Clip vs terminal bancaria según factura + validación de autorización Finanzas en tiempo real | Alto — impacta UX de caja y podría mostrar métodos de pago no autorizados | Documentar la lógica de selección de terminal y la consulta de autorización. |
| DISC003-RN-2.4 | Prepago 100% obligatorio para tratamientos en María Linda | Crítico — si no está implementado, tratamientos se atienden sin cobro | Verificar en staging si la regla existe. Documentar flujo o abrir ticket de implementación. |
| DISC003-RN-3.1 / RN-3.2 | Agregar complementos a cita existente + comunicación médico-recepción | Alto — médico puede solicitar un complemento que recepción no registra ni cobra | Crear flujo Cobro #02: agregar complemento a cita en curso. |
| DISC003-RN-4.1 / RN-4.2 | Protección automática de precio de agendación + período de gracia 15 días configurable | Crítico — aprobado por COO y CFO; actualmente se aplica con códigos manuales (DISC003-H-05). Si el sistema cobra precio actualizado, hay impacto directo al paciente | Crear flujo Agenda #08-bis: reagendado con protección de precio. Verificar si existe lógica en `rescheduleReceptionAction`. |
| DISC003-RN-5.1 a RN-5.5 | Devoluciones: límite configurable, niveles de autorizador, documentación obligatoria, autorización médica para tratamientos realizados, registro sistémico | Crítico — proceso operativo completo sin ninguna implementación documentada | Crear flujo Cobro #03: devoluciones. Incluir FSM de solicitud → autorización → ejecución. |
| DISC003-RN-6.1 / RN-6.2 | Control sistémico de sesiones de paquetes con alertas de agotamiento + vinculación a paquete original | Alto — actualmente con notas manuales en ML (DISC003-H-07). Sin control sistémico, pacientes pueden consumir más sesiones de las contratadas | Ampliar Contratos #04 para documentar contador de sesiones y lógica de alerta. |
| DISC003-RN-7.1 / RN-7.2 | Valoración médica obligatoria previa para procedimientos en consultorio; lista de procedimientos pendiente de Dirección Médica | Crítico — si el sistema permite agendar procedimientos sin valoración, hay riesgo médico y legal | Crear flujo Agenda #xx: prerequisito de valoración médica para agendamiento de procedimientos. |
| DISC003-RN-8.1 / RN-8.2 | Datos mínimos en ticket de venta para conciliación + eliminación de captura manual de 4 dígitos de tarjeta | Medio — afecta conciliación contable y posible riesgo PCI si se capturan datos de tarjeta manualmente | Documentar campos requeridos del ticket y la restricción de datos de tarjeta. |

### Dominio: Espacios Físicos / Cabinas — ausente del manual

| DISC ID | Hallazgo | Impacto estimado | Recomendación |
|---|---|---|---|
| DISC032-RN-1.2 | Cada cabina solo puede ofrecer servicios según su equipo físico (restricción de servicio por cabina) | Crítico — si el sistema no valida esto, se pueden agendar servicios imposibles de realizar en el espacio asignado | Crear flujo Config #xx: catálogo de capacidades por cabina. Verificar si existe en `service_catalog`. |
| DISC032-RN-2.1 | Asignación de cosmetólogas: equidad por turno de entrada, digitalizar proceso (eliminar hoja impresa) | Alto — proceso actualmente en papel; riesgo de conflictos de asignación | Crear flujo Agenda #xx: asignación de cosmetóloga por turno. |
| DISC032-RN-3.1 | Validación de empalme: mismo paciente no puede tener citas simultáneas en diferentes espacios | Crítico — bug confirmado en sistema actual (DISC032-H-04). Pacientes pueden tener doble cita al mismo tiempo | Crear flujo Agenda #xx: validación de unicidad de paciente en el tiempo. Abrir ticket de implementación. |
| DISC032-RN-3.2 | Sobreagenda virtual (Cabina 3) solo para depilación, solo en Coyoacán; no replicar sin espacios disponibles | Alto — si se replica la lógica a otras sucursales sin esta restricción, genera sobreagenda sin respaldo físico | Documentar la excepción en Agenda #05/#06. |
| DISC032-RN-4.1 / RN-4.2 | Comisiones de cosmetólogas por sesión realizada + por paquetes vendidos durante procedimiento | Alto — sin sistema de comisiones, cálculo es manual y propenso a errores | Crear flujo Comisiones #01. Dominio completamente nuevo en el manual. |
| DISC032-RN-5.1 | Agendamiento de laboratorios informativo: múltiples estudios en un solo slot, cobro en único ticket | Medio — si el sistema genera un slot por estudio, genera carga de agenda falsa y cobros separados | Documentar excepción de laboratorios en Agenda #05/#06 y en flujo de Cobro. |
| DISC032-RN-6.1 | Cotejo de patología mensual: 3 fuentes cruzadas, folios consecutivos sin salto | Medio — proceso mensual de control sin flujo técnico; riesgo de discrepancias no detectadas | Crear flujo Patología #01: conciliación mensual. Dominio nuevo. |

### Dominio: Bloqueos — reglas adicionales no documentadas en flujos existentes

| DISC ID | Hallazgo | Impacto estimado | Recomendación |
|---|---|---|---|
| DISC034-RN-2.1 | Catálogo único homologado de tipos de bloqueo para ambas marcas (personales, corporativos, lactancia) | Crítico — sin catálogo unificado, cada sucursal usa términos distintos; imposible reportear o auditar bloqueos por tipo | Crear flujo Config #xx: catálogo de tipos de bloqueo. Actualizar Agenda #14/#15 con referencia al catálogo. |
| DISC034-RN-2.2 | Tipo especial "lactancia/maternidad" que genera bono al médico (integración con nómina) | Alto — bono de nómina sin registro sistémico es procesado manualmente o no se procesa | Documentar integración con nómina como nuevo flujo. |
| DISC034-RN-4.1 | Bloqueo automático de Agenda 2 al agendar procedimiento exclusivo en Agenda 1 (doble agenda) | Crítico — riesgo de olvido confirmado en DISC034-H-06; impacta disponibilidad real vs disponibilidad mostrada al paciente | Actualizar Agenda #07 para documentar bloqueo automático cruzado. Verificar implementación en staging. |
| DISC034-RN-5.1 | Notificación automática entre sucursales al bloquear médico multisucursal | Alto — sin notificación, la sucursal B no sabe que el médico fue bloqueado en sucursal A | Crear flujo Notificaciones #xx: notificación de bloqueo cross-sucursal. |
| DISC034-RN-6.1 / RN-6.2 | Registro obligatorio de usuario que crea Y elimina bloqueos (con fecha, hora, rol) | Crítico — requerimiento de auditoría; sin trazabilidad, imposible investigar bloqueos erróneos o maliciosos | Actualizar Agenda #19 con requerimiento de audit trail. Verificar `audit_log` en schema. |
| DISC034-RN-7.1 | Bloqueo directo sobre citas existentes sin paso intermedio de "indisponibilidad" | Alto — UX actual obliga a un paso extra; aumenta riesgo de olvido de reagenda | Actualizar Agenda #14/#15 para documentar el flujo de bloqueo sobre cita existente y el reagende que dispara. |

### Dominio: Pricing de Servicios — ausente del manual

| DISC ID | Hallazgo | Impacto estimado | Recomendación |
|---|---|---|---|
| DISC002-RN-SVC-001 | Precio base por sucursal específica (no por región) | Crítico — si el booking usa precio regional, el cobro al paciente puede ser incorrecto | Crear flujo Servicios #01: estructura de precios por sucursal. |
| DISC002-RN-SVC-002 | Múltiples SKUs por servicio (día semana, rol médico, horario) | Crítico — la lógica de selección de SKU impacta directamente el precio final; sin documentación no es testeable | Crear flujo Servicios #02: resolución de SKU en el momento del booking. |
| DISC002-RN-SVC-003 | Factor médico aditivo sobre SKU (director +100 MXN, jefe clínico +50 MXN) | Alto — factor no documentado puede no estar implementado; pacientes pagarían precio incorrecto | Documentar como parte de Servicios #02 o Agenda #07. |
| DISC002-RN-SVC-009 | Servicios no médicos no bloquean agenda de personal específico; asignación al momento de prestación | Alto — si el sistema bloquea agenda de personal para labs o vacunas, genera falsa indisponibilidad | Actualizar Agenda #05/#06 con la excepción de servicios no médicos. |
| DISC002-RN-SVC-010 | Membresías clasificadas como productos, no servicios; implicaciones fiscales ERP | Alto — clasificación incorrecta genera errores en integración con NetSuite | Crear flujo Membresías #01. Dominio completamente nuevo. |
| DISC002-RN-SVC-011 | Hospitalización: el sistema rastrea solo 5 roles; gestión operativa es de OSPI | Medio — riesgo de scope creep si el equipo asume que el sistema debe gestionar hospitalización completa | Documentar los límites del sistema respecto a hospitalización y la separación con OSPI. |

---

## 4. Discrepancias y conflictos detectados

Los siguientes casos presentan contradicción directa entre lo que la sesión de Discovery documenta como comportamiento esperado y lo que el manual técnico o el código implementan. Son los casos de prueba de mayor prioridad para el sprint de QA.

---

**D-01 — Timing del no-show automático**

| | Detalle |
|---|---|
| **Discovery dice** | DISC003-H-03 / RN-1.2: El no-show automático ocurre exactamente 1 minuto después de finalizar la duración completa de la cita. Ejemplo: cita 10:00 de 30 min → no-show a las 10:31. |
| **Manual / código dice** | Agenda #13: Cron pg-boss corre `*/5 * * * *`. La granularidad es de 5 minutos. El flujo dice "marcado automático de inasistencias cada 5 min" sin especificar el momento exacto dentro del ciclo. |
| **Conflicto** | El cron puede ejecutar el marcado hasta 4 minutos antes o después del momento exacto requerido por la regla de negocio. Si el cron corre a 10:30 y hay un grace period implícito no documentado, puede generar falsos no-shows. |
| **Impacto QA** | Alto — caso de prueba de timing. Requiere prueba con cita de duración conocida y verificación del timestamp exacto de marcado. |

---

**D-02 — RBAC de eliminación de bloqueos por origen del bloqueo**

| | Detalle |
|---|---|
| **Discovery dice** | DISC034-RN-1.3: Contact Center NO puede eliminar bloqueos generados desde sucursal (solo los que ellos mismos generaron). Regla granular sobre el origen del bloqueo. |
| **Manual / código dice** | Agenda #14, #15, #19: Los flujos documentan roles autorizados (gerente, admin corporativo, admin plataforma) pero no modelan la distinción "bloqueo creado por sucursal vs bloqueo creado por Contact Center". El RBAC actual en `rbac-matrix.ts` no tiene este atributo de origen. |
| **Conflicto** | El sistema puede estar permitiendo a Contact Center eliminar bloqueos de sucursal, o bloqueando eliminaciones legítimas del propio Contact Center, dependiendo de cómo resuelve `requireRole`. |
| **Impacto QA** | Crítico — caso de prueba de autorización. Requiere test con usuario Contact Center intentando eliminar un bloqueo generado por sucursal (debe fallar) y uno generado por Contact Center (debe pasar). |

---

**D-03 — Reversión de no-show a "asistida"**

| | Detalle |
|---|---|
| **Discovery dice** | DISC003-RN-1.3 (Reina Madre): Una cita marcada como no-show puede revertirse a "asistida" si el paciente llega tarde, con autorización de gerente o coordinador. Proceso aprobado por COO. |
| **Manual / código dice** | Agenda #12 (FSM): El FSM de citas no documenta la transición `no_show → completed/asistida`. Las transiciones permitidas en `ALLOWED_CONTRACT_TRANSITIONS` no incluyen explícitamente esta reversión. |
| **Conflicto** | Posible bloqueo técnico de una operación de negocio válida y aprobada. Si el FSM no permite `no_show → asistida`, el staff no puede corregir un no-show erróneo sin intervención de plataforma. |
| **Impacto QA** | Crítico — caso de prueba de FSM. Requiere verificar en staging si la transición existe. Si no existe, es un ticket de implementación con prioridad alta. |

---

**D-04 — Protección de precio en reagendado**

| | Detalle |
|---|---|
| **Discovery dice** | DISC002-RN-SVC-004 / DISC003-RN-4.1 / RN-4.2: El precio a cobrar es el vigente al momento de AGENDAR. Período de gracia de 15 días naturales al reagendar. El sistema debe respetar esto automáticamente. Validado por COO y CFO. |
| **Manual / código dice** | Agenda #08: El flujo documenta `rescheduleReceptionAction` pero no menciona protección de precio. DISC003-H-05 confirma que actualmente se implementa con "códigos de descuento manuales", lo que significa que el sistema no lo automatiza. |
| **Conflicto** | El flujo técnico actual no implementa la regla de negocio validada por COO y CFO. Si el desarrollador tomó el flujo del manual como referencia, la protección de precio no fue implementada. |
| **Impacto QA** | Crítico — regla de protección al consumidor. Requiere caso de prueba: (1) agendar cita con precio P, (2) modificar precio del servicio a P+N, (3) reagendar dentro de 15 días, (4) verificar que el cobro sea P y no P+N. |

---

**D-05 — Empalme de paciente en cabinas simultáneas**

| | Detalle |
|---|---|
| **Discovery dice** | DISC032-RN-3.1: El sistema debe impedir que el mismo paciente tenga citas simultáneas en diferentes cabinas (validación paciente + horario + cualquier espacio). Consenso alcanzado en sesión. |
| **Manual / código dice** | Agenda #12, #05/#06: Los slots disponibles se calculan por médico/recurso específico. No hay documentación de validación cruzada de paciente contra múltiples espacios simultáneos. `getAvailableSlotsV2` filtra por médico pero no tiene la restricción de unicidad de paciente en el tiempo. |
| **Conflicto** | El sistema puede estar permitiendo agendar al mismo paciente en dos cabinas al mismo tiempo. DISC032-H-04 confirma que este bug existe en el sistema actual. |
| **Impacto QA** | Alto — caso de prueba de validación de booking. Requiere caso: (1) agendar paciente X en Cabina 1 a las 10:00, (2) intentar agendar al mismo paciente en Cabina 2 a las 10:00, (3) verificar rechazo. |

---

**D-06 — Auto-bloqueo de médicas**

| | Detalle |
|---|---|
| **Discovery dice** | DISC034-H-09: Médicos técnicamente pueden auto-bloquearse en María Linda pero no deben. Sin restricción en sistema. |
| **Manual / código dice** | Agenda #14 (FSM solicitud de bloqueo): El flujo documenta que la médica inicia el flujo de solicitud. El control técnico recae en `requireRole`. Si el rol de médica tiene acceso a la ruta de registro de bloqueos, el sistema no impide el auto-bloqueo. |
| **Conflicto** | El RBAC puede no estar restringiendo la creación directa de bloqueos por parte de médicas (vs la solicitud formal a través del canal de correo). |
| **Impacto QA** | Medio — riesgo operativo. Caso de prueba: acceder con usuario de rol médico a la ruta de registro de bloqueos; debe ser rechazado o no visible. |

---

**D-07 — Laboratorio sin preparación: reagendar vs cancelar**

| | Detalle |
|---|---|
| **Discovery dice** | DISC002-RN-SVC-008: Si el paciente no cumple la preparación requerida para laboratorio, se reagenda (NO se cancela ni reembolsa). El pago es válido. |
| **Manual / código dice** | No existe ningún flujo que documente el manejo de "laboratorio sin preparación". El escenario existe en DISC003 como Flujo 7 AS-IS pero no tiene contrapartida técnica documentada. |
| **Conflicto** | Comportamiento no implementado o sin cobertura de pruebas. Si el sistema cancela la cita en lugar de reagendarla, hay impacto financiero directo para el paciente. |
| **Impacto QA** | Alto — implicación de reembolso. Verificar qué acción está disponible en el sistema cuando se registra "paciente sin preparación" en un laboratorio. |

---

## 5. Sesiones de Discovery pendientes de analizar

Las siguientes sesiones no han sido incluidas en este análisis. Se ordenan por prioridad QA descendente, basada en la criticidad del dominio y la densidad de gaps detectados en las sesiones ya analizadas.

| Prioridad | DISC ID | Dominio estimado | Razón para priorizar |
|---|---|---|---|
| 🔴 1 | DISC003-bis / DISC-Cobro (cualquier sesión de Cobro o Facturación no analizada) | Cobro | El dominio de Cobro tiene 0 flujos en el manual. 18 reglas de negocio validadas por COO y CFO sin ninguna contrapartida técnica documentada. Es la brecha más grande encontrada en este análisis. |
| 🔴 2 | DISC001 o DISC-Pricing | Pricing de Servicios | SKUs múltiples por servicio y protección de precio son reglas críticas aprobadas por COO. Sin ningún flujo técnico. Impacta directamente el precio cobrado al paciente. |
| 🔴 3 | DISC-Portal de Paciente / OTP paciente | Portal de Paciente | Once flujos técnicos del portal (Auth #07–08, Pacientes #07–08, Agenda #10–11) sin ningún respaldo Discovery analizado. El portal es el canal de autoservicio del paciente. |
| 🔴 4 | DISC-Bot WhatsApp (si existe DISC dedicada) | Bot WhatsApp | 28 flujos técnicos sin Discovery de respaldo. El bot WA es el canal de autoservicio principal por volumen de interacciones. |
| 🟠 5 | DISC-Contratos y Planes | Contratos y Planes | 7 flujos técnicos (FSM del contrato, ruta clínica, asignación de consejera) sin ninguna DISC analizada. La consejera es un actor operativo crítico. |
| 🟠 6 | DISC-Staff y RBAC (si existe separada de las parciales en DISC034) | Gestión de Staff | 13 flujos de staff. Las reglas de RBAC y jerarquía de roles tienen impacto transversal en todos los dominios. Las restricciones de Contact Center detectadas en D-02 sugieren que hay más reglas granulares no documentadas. |
| 🟠 7 | DISC-Comisiones (cosmetólogas + médicos) | Comisiones | Referenciado en DISC032 y DISC003 como pendiente. Sin ningún flujo técnico ni Discovery completa analizada. Alta complejidad operativa con implicaciones de nómina. |
| 🟡 8 | DISC-Notificaciones y Comunicación | Notificaciones | 8 flujos técnicos. Integración LFPDPPP, consentimiento, templates Meta aprobados son decisiones con implicación legal. Sin Discovery analizada. |
| 🟡 9 | DISC004 / DISC-Membresías y Anticipos | Membresías | Reclasificadas como "productos, no servicios" (DISC002-RN-SVC-010) con implicaciones fiscales pendientes con ERP NetSuite. |
| 🟡 10 | DISC-Hospitalización | Hospitalización | DISC002-RN-SVC-011 documenta separación con OSPI. Dominio completamente ausente del manual técnico. Menor urgencia por ser operación de OSPI. |
| 🟢 11 | DISC-Integración Zoho CRM y Mindbody | Integraciones | Webhooks documentados técnicamente pero sin Discovery de negocio para los campos a sincronizar. |
| 🟢 12 | DISC-Doctoralia (mencionada como pendiente en DISC034-I-02) | Integraciones externas | Integración identificada como gap en DISC034 pero sin sesión de Discovery aún agendada. |

---

## 6. Próximos pasos recomendados

### Paso 1 — Validar las 7 discrepancias en staging (sprint actual)

Las discrepancias D-01 a D-07 tienen validación de negocio y afectan directamente el comportamiento del sistema en producción. Para cada una: ejecutar el caso de prueba en staging, registrar resultado en Linear con evidencia, y clasificar como bug (si el comportamiento actual difiere de Discovery) o como gap de documentación (si el sistema ya lo implementa correctamente pero el manual no lo refleja).

Orden sugerido por criticidad: D-03 → D-04 → D-05 → D-02 → D-01 → D-07 → D-06.

### Paso 2 — Abrir sesión de Discovery para el dominio de Cobro

El dominio de Cobro tiene 18 reglas de negocio validadas por COO y CFO sin ningún flujo técnico documentado. Antes de iniciar cualquier desarrollo del módulo de cobro, se debe convocar una sesión de Discovery de cierre que confirme el estado de implementación de cada RN en el sistema actual (Mindbody/ML) y defina cuáles migrar a Reina Madre 2.0 en qué orden.

### Paso 3 — Crear flujos faltantes para los tres dominios ausentes

Los dominios de **Cobro**, **Pricing de Servicios** y **Espacios Físicos/Cabinas** no tienen ni un solo flujo en el manual. El mínimo viable para comenzar el desarrollo de cada uno es: (1) inventario de flujos necesarios, (2) un flujo por dominio como plantilla, (3) revisión con el stakeholder operativo correspondiente antes de cerrar el flujo como "validado".

### Paso 4 — Agregar trazabilidad inversa al inventario de flujos

Actualizar `docs/manual/00-inventario.md` para que cada flujo incluya la columna "DISC de respaldo" con el ID de la sesión que validó ese flujo, o el valor "pendiente" cuando no existe. Esto convierte el inventario en un instrumento de auditoría continua y permite detectar flujos huérfanos automáticamente al agregar nuevas sesiones de Discovery.

### Paso 5 — Priorizar análisis de DISC003-bis y DISC-Portal antes del siguiente milestone

Las sesiones de Discovery con mayor deuda relativa (Cobro, Portal de Paciente, Bot WA) deben ser analizadas y cruzadas con el manual antes de cerrar el milestone actual. El análisis de cada sesión puede seguir el mismo formato de este documento, extendiendo las tablas de las secciones 1, 2 y 3 con los hallazgos correspondientes.

---

_Documento generado el 2026-06-17. Fuente: Notion DB "📅 0. Sesiones de Discovery" × `docs/manual/00-inventario.md`. Para correcciones o adiciones contactar al equipo QA._