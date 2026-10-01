# Diarios de Costa Rica — guía de integración

Código nuevo para la estructura **diario madre + capítulos**. Todo está en archivos nuevos; **no se tocó nada del repo**. Estos pasos los haces tú cuando decidas integrarlo.

## Archivos nuevos (copiar tal cual a la carpeta del mod)

| Archivo | Contenido |
| --- | --- |
| `common/journal_entries/ca_costa_rica_journals.txt` | Diario madre, capítulos A–D, caída de Morazán, nueva Liga |
| `common/scripted_progress_bars/zz_ca_costa_rica_progress_bars.txt` | Barras: Soberanía de San José, Descontento josefino, Reunificación |
| `common/scripted_effects/zz_ca_costa_rica_journal_effects.txt` | Planificador de capítulos, cambio de soberanía, balcanización, anexión de la Liga |
| `common/scripted_triggers/zz_ca_costa_rica_journal_triggers.txt` | Leer la soberanía, regla de un capítulo con barra a la vez |
| `events/ca_costa_rica_journal_events.txt` | Eventos de desenlace .300–.362 (mismo namespace `ca_costa_rica_events`) |
| `localization/english/ca_costa_rica_journals_l_english.yml` | Textos nuevos (UTF-8 con BOM) |

## Estructura

```
je_ca_historic_situation_in_costarica  (madre, barra Soberanía 0-100, empieza en 50)
 ├─ B  je_ca_cos_bocas_del_toro     feb-jun 1836   sin barra, en paralelo
 ├─ A  je_ca_pacificacion_norte     jun-sep 1836   barra (ca_pacificacion_progress)
 ├─ C  je_ca_cos_urnas_y_cuartel    ene 1837-mar 1838
 └─ D  je_ca_cos_regreso_aguila     1840-1841      solo ruta separatista
Éxito  -> .300 Estado consolidado (ruta Carrillo o ruta federal)
Fracaso con soberanía > 25  -> .320 Morazán gobierna -> je_ca_cos_caida_morazan -> .350
Fracaso con soberanía <= 25 -> .310 La Liga renace   -> je_ca_cos_nueva_liga -> .360 / .361 / .362
```

Cómo mueve cada capítulo la soberanía:

| Resultado | Soberanía |
| --- | --- |
| A gana (Guanacaste pacificada) | +20 |
| A falla (Guanacaste perdida) | −30 y −2/mes |
| B: la Federación calla (humillación) | +10 |
| B: la Federación va a la guerra | −10 |
| B: la UCA no financia a Galindo | +5 |
| C: golpe josefino triunfa | +25 |
| C: Carrillo gana las elecciones | +15 |
| C: Argüello y alianza federal | −10 |
| C: golpe frustrado | −15 |
| D: Morazán rechazado o se queda en Perú | +15 |

El golpe de mayo de 1838 solo triunfa con soberanía ≥ 40; en Caldera (abril 1842) el ejército solo resiste con soberanía ≥ 60.

## Pasos en archivos existentes

1. **Borrar los diarios viejos.** Quitar los bloques `je_ca_historic_situation_in_costarica` y `#je_ca_pacificacion_norte` de `ca_central_american_states.txt` **y** de `ca_central_american_states_journal.txt` (son duplicados). Si no, habrá dos definiciones con la misma clave.
2. **Rellenar las 4 provincias** en `ca_cos_balkanize_effect`: cambiar `xRELLENAR_CARTAGO`, `xRELLENAR_HEREDIA`, `xRELLENAR_ALAJUELA` y `xRELLENAR_GUANACASTE` por los IDs hexadecimales de `STATE_COSTA_RICA` en `map_data/state_regions`. Mientras tengan esos nombres, el juego dará error.
3. **Descomentar los eventos que usan los capítulos**: 4–11, 81, 99 (A); 12–18, 161 (B); 19, 191, 20, 21 (C); 201–203 (D).
4. **Ajustes dentro de esos eventos:**
   - Evento 4: borrar `add_journal_entry = { type = je_ca_pacificacion_norte }`; ahora el diario abre el evento, no al revés.
   - Evento 14, opción B: dentro de `c:COS = { ... }` añadir `set_variable = ca_cos_bocas_rechazo_temprano`.
   - Evento 18, opción B: dentro de `c:COS = { ... }` añadir `set_variable = ca_cos_bocas_apoyo_federal`.
   - Evento 203, opción A: borrar `trigger_event = { id = ca_costa_rica_events.22 days = 120 }`; el capítulo D dispara Caldera (.340) por fecha.
   - Evento 20: borrar `trigger_event = { id = ca_costa_rica_events.21 days = 1000 }` solo si prefieres que la Ley de Bases salga por otra vía; si no, dejarlo.
5. **Modificadores que faltan** en los eventos viejos: `ca_add_authority` y `age_of_ca_caudillos_modifier` no existen; defínelos o cámbialos antes de descomentar.

## Verificar contra los script_docs (`effects.log` / `triggers.log`)

- `scripted_bar_progress = { name = X value >= N }`: forma de bloque, la misma que ya usa `01_ca_central_america.txt`.
- `je:<clave> ?= { add_progress = { ... } }` desde el país: mismo patrón que `ca_frca_support_effects.txt`.
- `turmoil >= 0.25` en scope de estado (barra de soberanía).
- `change_tag = CCR` (evento .360).
- `create_country` con `province = p:...`: mismo patrón que `ca_create_losaltoswithoutsoconusco_effect`.
- Exilio: .320 y .350 retiran a Carrillo/Morazán con `kill_character`, como el evento 3; si hay un efecto de exilio, cámbialo.
- Parámetros `$VALUE$`, `$TAG$`, `$PROV$` en efectos y triggers con script.

## Notas de diseño

- El diario madre ya **no se invalida cuando desaparece la UCA**; solo si el país deja de ser COS.
- Los capítulos tienen ventana de fechas: si Costa Rica aparece tarde (disolución de 1838), los pasados se saltan.
- Guanacaste solo se separa en la balcanización si no se perdió antes en la Pacificación.
- Si el evento 201 no llega a dispararse (el capítulo D abre en 1841 y su ventana es 1840), el capítulo se cierra por tiempo a fines de 1842 contando como éxito.
