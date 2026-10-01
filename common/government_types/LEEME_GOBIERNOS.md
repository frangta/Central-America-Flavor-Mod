# Tipos de gobierno CAFM — versión corregida + Costa Rica

Todo va en archivos nuevos. **El repo no se tocó.**

## Instalación

1. **Borrar** `common/government_types/ca_government_types.txt`.
2. **Copiar** `common/government_types/00_ca_government_types.txt`. El prefijo `00_ca_` hace que cargue antes que el vanilla `00_government_types.txt`. El juego usa el **primer** tipo válido, así que con el nombre viejo los tipos vanilla ganaban siempre.
3. **Copiar** `localization/english/ca_government_types_l_english.yml` (UTF-8 con BOM).

## Orden de evaluación (el primero válido gana)

| # | Tipo | Categoría | Condición (además de cultura centroamericana) | Título |
| --- | --- | --- | --- | --- |
| 1 | `gov_ca_cos_confederacion` | Alt | Tag CCR o `ca_cos_confederacion` | President of the Confederation |
| 2 | `gov_ca_cos_ciudad_liga` | Alt | `ca_cos_ciudad_liga` | Political Chief |
| 3 | `gov_ca_cos_reino` | Alt | COS + monarquía | King / Queen |
| 4 | `gov_ca_cos_republica_obrera` | Alt | COS + `costa_rica_socialist_republic` | President of the Council |
| 5 | `gov_ca_cos_jefatura_morazan` | Alt/hist. | COS + `ca_cos_gobierno_morazan` | Provisional Head of State |
| 6 | `gov_ca_cos_jefatura_vitalicia` | Histórico | COS + `ca_carrilo_decret` | Perpetual Head of State |
| 7 | `gov_ca_cos_primera_republica` | Histórico | COS + `costa_rica_1st_republic` | President of the Republic |
| 8 | `gov_ca_cos_estado_1844` | Histórico | COS + `free_costa_rica_reform` | Head of State |
| 9 | `gov_ca_jefe_estado` | Histórico | COS + república presidencial + `costa_rica_1830s` | Head of State |
| 10 | `gov_ca_cos_republica_olimpo` | Dinámico | COS + ≥ 1880 + censitario/riqueza + intelligentsia en gobierno | President of the Republic |
| 11 | `gov_ca_cos_cuarteles` | Dinámico | COS + autocracia + ejército en gobierno | General President |
| 12 | `gov_ca_jefe_supremo` | Genérico | Autocracia + (`jefe_supremo_var` o gobernante caudillo) | Perpetual Dictator |
| 13 | `gov_ca_junta` | Genérico | Autocracia + ejército en gobierno | General |
| 14 | `gov_ca_republica_bananera` | Genérico | Ley bananera | President |
| 15 | `gov_ca_republica_cafetalera` | Genérico | Ley del café + oligarquía/riqueza | President |
| 16 | `gov_ca_director_supremo` | Genérico | < 1855 + (UCA o república presidencial) | Supreme Director |
| 17 | `gov_ca_presidential_oligarchy` | Genérico | República presidencial + oligarquía | President |
| 18 | `gov_ca_presidential_democracy` | Genérico | República presidencial + sufragio | President |

La cultura se revisa con `only_latin_centralamericans_cultures`, que ya existe en `zz_ca_scripted_triggers.txt`.

## Qué se corrigió del archivo original

- Los 8 tipos exigen cultura centroamericana. Antes, Junta, Democracia y Oligarquía podían aplicarse a cualquier país del mundo.
- El Jefe de Estado va antes que el Director Supremo, que ahora tiene límite de fecha (< 1855) para no tapar a los demás.
- El Jefe Supremo también vale con un gobernante caudillo (`ideology_central_america_caudillo`), porque `jefe_supremo_var` no se define en ningún archivo.
- `this = c:UCA` → `c:UCA ?= this`; `ig:ig_armed_forces = {` → `?=`.
- Cafetalera, Bananera y Oligarquía pasan a `presidential_elective`, porque tienen voto censitario u oligarquía.
- Títulos propios `RULER_TITLE_CA_*`, con versión femenina, y localización completa.
- `gov_ca_costarica_noocracy` (comentado) se retiró: lo sustituye la República del Olimpo. El resto del bloque comentado de otros países queda igual.

## Dependencias (sin esto, algunos tipos no aparecen)

- **Tipos 4, 7 y 8:** dependen de enmiendas que hoy no se pueden activar (bug C2 del informe: su `possible` exige NO tener la ley que es su `parent`). Hay que corregir esas enmiendas.
- **Tipos 1, 2 y 5:** usan variables de los diarios nuevos (paquete `cafm_costa_rica_diarios`).
- **`jefe_supremo_var`:** si quieres el Jefe Supremo por vía histórica en otros países, algún evento debe definir esa variable.

## Verificar en el juego

- Que `00_ca_` realmente cargue antes que el vanilla: cambia leyes con la consola y revisa el título del gobernante.
- `has_ideology = ideology:...` dentro de `ruler ?= { }` en un `possible` de tipo de gobierno.
- Que `change_to_dictatorial` y `post_change_to_dictatorial` existan en tu versión (1.13); el archivo original ya los usaba.
