# Diccionario de variables: focalización de programas sociales, ENDES 2025

Variables de la Encuesta Demográfica y de Salud Familiar (ENDES) 2025 del INEI usadas en `reporte_focalizacion.qmd`. Los códigos se verificaron contra los diccionarios oficiales, el cuestionario del hogar y los propios datos.

- **Fuente:** INEI, microdatos ENDES 2025 (<https://proyectos.inei.gob.pe/microdatos/>), módulos 1629, 1630 y 1641.
- **Llave de unión:** `HHID`, identificador del hogar. Viene con espacios a la izquierda y se limpia con `str_trim()`. En RECH1 y RECH4 se agrega el número de orden de la persona (`HVIDX` / `IDXH4`).

## Archivos

| Archivo en `datos/` | Archivo original del INEI | Módulo | Unidad (una fila por…) | Filas |
|---|---|---|---|---|
| `RECH0_2025.csv` | `RECH0_2025.csv` | 1629 | vivienda/hogar seleccionado | 37,331 |
| `RECH1_2025.csv` | `RECH1_2025.csv` | 1629 | miembro del hogar | 131,929 |
| `RECH4_2025.csv` | `RECH4_2025.csv` | 1629 | miembro del hogar | 131,929 |
| `RECH23_2025.csv` | `RECH23_2025.csv` | 1630 | vivienda/hogar seleccionado | 37,331 |
| `programas_sociales_hogar_2025.csv` | `Programas Sociales x Hogar_2025.csv` | 1641 | hogar con entrevista completa | 33,569 |

RECH0 y RECH23 incluyen viviendas no entrevistadas. El análisis usa solo los 33,569 hogares con entrevista completa (`HV015 == 1`).

## RECH0: vivienda y diseño muestral

| Variable | Descripción | Valores | Uso en el reporte |
|---|---|---|---|
| `HHID` | Identificador del hogar | Texto (15) | Llave de unión |
| `HV001` | Conglomerado | 1–3175 | Conglomerado (UPM) del diseño muestral |
| `HV005` | Factor de ponderación | Entero con 6 decimales implícitos | Peso = `HV005 / 1e6` |
| `HV012` | Miembros residentes habituales | 0–25 | Tamaño del hogar; pondera la población para construir quintiles |
| `HV015` | Resultado de la entrevista | 1 Completa · 2 Entrevistado ausente · 3 Hogar ausente · 4 Aplazada · 5 Rechazada · 6 Desocupada · 7 Destruida · 8 No encontrada · 9 Otro | Filtro `HV015 == 1` |
| `HV022` | Estrato | 1–250 | Estrato del diseño muestral |
| `HV024` | Departamento | 1 Amazonas · 2 Áncash · 3 Apurímac · 4 Arequipa · 5 Ayacucho · 6 Cajamarca · 7 Callao · 8 Cusco · 9 Huancavelica · 10 Huánuco · 11 Ica · 12 Junín · 13 La Libertad · 14 Lambayeque · 15 Lima · 16 Loreto · 17 Madre de Dios · 18 Moquegua · 19 Pasco · 20 Piura · 21 Puno · 22 San Martín · 23 Tacna · 24 Tumbes · 25 Ucayali | Cortes departamentales (se verificó contra los 2 primeros dígitos de `UBIGEO`) |
| `HV025` | Área de residencia | 1 Urbano · 2 Rural | Cortes por área |
| `UBIGEO` | Ubicación geográfica | Texto (6) | Solo como control de `HV024` |

## RECH23: índice de riqueza

| Variable | Descripción | Valores | Uso en el reporte |
|---|---|---|---|
| `HV270` | Quintil de riqueza | 1 Más pobre · 2 · 3 · 4 · 5 Más rico | Medida principal de pobreza (quintiles de población, no de hogares) |
| `HV271` | Puntaje del índice de riqueza | Continuo, de −2.1 a 2.1 aprox. | Sensibilidad: deciles, curva de concentración y quintiles por área |

## RECH1: miembros del hogar

| Variable | Descripción | Valores | Uso en el reporte |
|---|---|---|---|
| `HVIDX` | Número de orden del miembro | 1–25 | Llave persona (con `HHID`) |
| `HV102` | ¿Vive habitualmente aquí? | 0 No · 1 Sí | Residente habitual |
| `HV104` | Sexo | 1 Hombre · 2 Mujer | Identificar mujeres de 15 a 49 años |
| `HV105` | Edad | 0–97 (98 = no sabe; no aparece en 2025) | Niños < 4 y < 5, personas de 0 a 19, adultos de 65+ |
| `HV112` | Número de orden de la madre | 0 = madre no vive en el hogar; 1–20 | Vincular a cada niño < 5 con su madre (universo de Juntos) |

## RECH4: actividad y seguro de los miembros

| Variable | Descripción | Valores | Uso en el reporte |
|---|---|---|---|
| `IDXH4` | Número de orden del miembro | 1–25 | Llave persona (con `HHID`); equivale a `HVIDX` |
| `SH13` | Actividad la semana pasada | 1–7 actividades · **8 Es jubilado/pensionista** · 96 Otro · 98 No sabe | Universo de Pensión 65 "sin pensión" (65+ con `SH13` ≠ 8) |
| `SH11A` | Afiliado a EsSalud | 0 No · 1 Sí | Variante estricta de Pensión 65 (solo en sensibilidad) |

## Programas Sociales x Hogar

Todas las preguntas tienen la forma "¿Algún miembro de su hogar es beneficiario de…?", con los códigos **1 Sí · 2 No · 8 No sabe**. El diccionario del INEI dice que "no sabe" es el código 3, pero el cuestionario y los datos usan 8 y no aparece ningún 3. Un **vacío** significa que la pregunta no se hizo por el filtro del cuestionario: el hogar está fuera del universo del programa.

| Variable | Programa | Filtro del cuestionario (a quién se pregunta) | Uso en el reporte |
|---|---|---|---|
| `QH91` | Beca 18 | Hogares con personas de 16 a 25 años | Excluido (61 hogares beneficiarios) |
| `QH93` | Trabaja Perú / Lurawi Perú | Todos (14 vacíos por error de flujo) | Excluido (107 hogares beneficiarios) |
| `QH95` | Juntos | Todos los hogares | Analizado en el universo: madre de 15–49 con hijo < 5 en el hogar |
| `QH99` | Pensión 65 | Hogares con residente habitual de 65+ | Analizado (universo amplio y "65+ sin pensión") |
| `QH100B` | Cuna Más: visitas a familias (SAF) | Hogares con niños < 4 o mujeres de 12 a 49 | Parte de "Cuna Más" (cualquiera de los dos servicios) |
| `QH101` | Vaso de Leche | Todos los hogares | Analizado (todos los hogares) |
| `QH103` | Comedor Popular | Todos los hogares | Analizado (todos los hogares) |
| `QH106` | Cuna Más: cuidado diurno | Hogares con niños < 4 | Parte de "Cuna Más" (cualquiera de los dos servicios) |

## Variables construidas en el reporte

| Variable | Definición |
|---|---|
| `peso` | `HV005 / 1e6` |
| `quintil` | `HV270` con etiquetas Q1 (más pobre) … Q5 (más rico) |
| `grupo_riqueza` | 40% más pobre (Q1–Q2) · Q3 · 40% más rico (Q4–Q5) |
| `decil` | Deciles de población de `HV271` a nivel nacional |
| `quintil_area` | Quintiles de población de `HV271` construidos por separado en urbano y rural |
| `u_juntos` | Hogar con al menos un niño < 5 cuya madre (`HV112`) es residente de 15 a 49 años |
| `u_p65_amplio` | Hogar al que se le preguntó `QH99` (residente de 65+) |
| `u_p65_sinpens` | `u_p65_amplio` y al menos un residente de 65+ con `SH13` ≠ 8 |
| `u_cunamas` | Hogar con al menos un niño < 4 |
| `cunamas` | Sí si `QH100B` = 1 o `QH106` = 1; No si ambos = 2 |
| Exclusión | % de hogares Q1–Q2 del universo que **no** recibe el programa |
| Inclusión | % de hogares Q4–Q5 del universo que **sí** recibe el programa |
