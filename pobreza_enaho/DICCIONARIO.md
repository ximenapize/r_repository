# Pobreza monetaria por departamento, 2016-2025: datos y diccionario

**Fuente de los datos:** INEI, Encuesta Nacional de Hogares (ENAHO), Condiciones de Vida y Pobreza, metodología actualizada,
módulo 34 «Sumarias (variables calculadas)», base anual. Descargado de <https://proyectos.inei.gob.pe/microdatos/> con
[`inei-microdatos`](https://github.com/fiorellarmartins/inei-microdatos). Los archivos están sin modificar, solo renombrados.

**Documento metodológico:** INEI, *Perú: Evolución de la pobreza monetaria, 2016-2025. Informe técnico* (en adelante, «el Informe»).
Las páginas citadas son las impresas en el Informe.

## Archivos

`data/sumaria_AAAA.dta`, uno por año (2016 a 2025). Una fila es un hogar.

| Archivo | Código INEI | Archivo | Código INEI |
|---|---|---|---|
| `sumaria_2016.dta` | 546-Modulo34 | `sumaria_2021.dta` | 759-Modulo34 |
| `sumaria_2017.dta` | 603-Modulo34 | `sumaria_2022.dta` | 784-Modulo34 |
| `sumaria_2018.dta` | 634-Modulo34 | `sumaria_2023.dta` | 906-Modulo34 |
| `sumaria_2019.dta` | 687-Modulo34 | `sumaria_2024.dta` | 966-Modulo34 |
| `sumaria_2020.dta` | 737-Modulo34 | `sumaria_2025.dta` | 1031-Modulo34 |

No se subieron las versiones `-12g` (gasto en 12 grupos, hasta 63 MB), que no se usan aquí.

## Variables usadas

| Variable | Etiqueta en la base | Uso |
|---|---|---|
| `ubigeo` | ubicación geográfica | Los 2 primeros dígitos son el código de departamento (`01` Amazonas … `25` Ucayali). |
| `gashog2d` | gasto total bruto | Gasto anual del hogar, ya deflactado. |
| `mieperho` | total de miembros del hogar | Denominador del gasto per cápita y multiplicador del factor de expansión. |
| `linea` | línea de pobreza total | Umbral de pobreza (soles por persona al mes) según dominio y año. |
| `linpe` | línea de pobreza alimentaria | Umbral de pobreza extrema (no se usa en el mapa; sirve para pobreza extrema). |
| `pobreza` | pobreza | Clasificación del INEI: 1 pobre extremo, 2 pobre no extremo, 3 no pobre. Se usa solo para validar. |
| `factor07` | factor de expansión anual, proyecciones CPV-2007 | Peso del hogar. |
| `conglome` | conglomerado | Unidad primaria de muestreo (diseño muestral). |
| `estrato` | estrato geográfico | Estrato (diseño muestral). |
| `dominio` | dominio | Dominio geográfico (1 Costa Norte … 8 Lima Metropolitana). |
| `año` | año | Año de la encuesta (en los archivos aparece como `aÑo`). |

## Fórmulas

```
gasto per cápita mensual   gpcm  = gashog2d / (12 * mieperho)
pobre                      pobre = 1 si gpcm < linea, 0 en otro caso
peso de persona            w     = factor07 * mieperho
incidencia (P0), %         P0    = 100 * sum(w * pobre) / sum(w)      por año y departamento
```

La incidencia se pondera por persona (`factor07 * mieperho`) porque la pobreza se mide sobre personas y no sobre hogares.
Los errores estándar deben calcularse con el diseño muestral (`conglome`, `estrato`, `factor07`).

## Citas al Informe

- **El gasto como indicador de bienestar.** «La medición se basa exclusivamente en el gasto como indicador de bienestar, al ser una mejor
  aproximación al ingreso permanente, y presentar menores niveles de sub-declaración en comparación con el ingreso.» (Informe, p. 11 y p. 67.)
- **Qué incluye el gasto.** «i) las compras monetarias de bienes y servicios, ii) el autoconsumo y autosuministro, iii) los pagos en especie,
  iv) las transferencias recibidas de otros hogares y v) las donaciones o transferencias de instituciones.» (Informe, p. 11 y p. 67.)
- **Regla de clasificación.** Se consideran pobres monetarios «las personas que residen en hogares cuyo gasto (proxy del ingreso permanente)
  per cápita es insuficiente para adquirir una canasta básica de alimentos y no alimentos». Los pobres extremos son quienes están por debajo
  del costo de la canasta básica de alimentos. (Informe, p. 67, sección 4.1.)
- **Enfoque.** Medición monetaria, absoluta y objetiva: el umbral es un valor fijo, la línea de pobreza, que no depende de la distribución
  del bienestar. Hay dos líneas, la de pobreza extrema y la de pobreza total. (Informe, p. 11, sección 1.1.1.)
- **Incidencia de la pobreza (P0).** Es el primer índice FGT (Foster, Greer y Thorbecke, 1984): «mide la proporción de la población que se
  encuentra en situación de pobreza o pobreza extrema respecto del total de la población», es decir, «la proporción de la población cuyo
  consumo se encuentra por debajo del valor de la línea de pobreza». (Informe, p. 67, sección 4.1.)
- **Deflactación.** El gasto está en «términos reales a precios de Lima Metropolitana» tras tres etapas: (1) precios promedio del año de la
  encuesta con el IPC, (2) valores constantes respecto del año base, (3) deflactor espacial de precios entre regiones. `gashog2d` ya viene
  deflactado. (Informe, p. 393, sección VIII.)
- **Factor de expansión.** Se actualizó con las últimas proyecciones de población disponibles. (Informe, p. 20.)
- **Cobertura departamental.** Las estimaciones por departamento tienen coeficientes de variación ≤ 15%, excepto Ica, Madre de Dios y
  Moquegua, que son referenciales. (Informe, sección 4.2.4.)

## Validación

Con estas fórmulas, la clasificación `gpcm < linea` coincide al 100% con la variable `pobreza` del INEI en los 10 años.
Incidencia de pobreza total: Cajamarca 2025 = 41,0%, igual que el Informe (sección 4.2.4); Perú 2016 = 20,7%, 2020 = 30,1%, 2025 = 25,7%.
