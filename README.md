# Fundamentos de Datos — Challenge 3: Inteligencia Geo-Temporal y de Redes

## Integrantes

- Juan Esteban Garcia - 1020222158
- Manuela Castaño - 1011510403
- Juan Felipe Restrepo - 1027740136

**Curso:** Fundamentos de Datos — Maestría en Ciencia de los Datos, Universidad EAFIT
**Docente:** Jorge Iván Padilla-Buriticá | **Periodo:** 2026-1

## Descripción del proyecto

Este repositorio contiene el desarrollo del taller *"Inteligencia Geo-Temporal y de Redes: Optimización de
Activos Críticos — TechLogistics S.A."*. TechLogistics S.A. (empresa ficticia usada como caso de estudio)
administra dos redes de sensores georreferenciadas —una red agroindustrial en el oriente antioqueño y una red
eléctrica nacional— que hasta ahora se han analizado por separado. El objetivo del taller es integrar tres
dimensiones de análisis (**series de tiempo**, **procesamiento de señales** y **grafos/geoespacial**) siguiendo
la metodología **CRISP-DM**, para responder tres preguntas de negocio de la junta directiva:

1. ¿Cómo se propaga el ruido dentro de la red de sensores?
2. ¿Dónde se localizan los puntos críticos (calor / baja biomasa) en el territorio?
3. ¿Cuál es el pronóstico de carga de la red eléctrica y qué tan crítico es cada nodo?

El desarrollo cubre cuatro fases: (1) comprensión de datos y geo-visualización, (2) procesamiento de señales y
filtrado, (3) análisis de grafos y topología de red, y (4) modelado predictivo y respuesta a las preguntas de
negocio del escenario *"La Falla del Nodo 214"*.

## Estructura del repositorio

```
FD_Challenge3/
├── data/
│   ├── input/                              # Datos fuente (sin modificar)
│   │   ├── agro_clean.csv / agro_noise.csv     # Red agroindustrial (2000 lecturas c/u)
│   │   └── ener_clean.csv / ener_noise.csv     # Red eléctrica (2000 lecturas c/u)
│   └── output/                             # Productos intermedios generados por el notebook
│       ├── agro3_filtrada.csv                  # Agro_3: cruda, limpia y filtrada (Butterworth)
│       ├── centralidad_nodos_ener.csv          # Métricas de centralidad, red eléctrica
│       └── centralidad_nodos_agro.csv          # Métricas de centralidad, red agroindustrial
├── notebooks/
│   └── Challenge3_TechLogistics.ipynb      # Desarrollo técnico completo (las 4 fases)
├── figures/                                # Figuras exportadas en PNG (evidencia del informe)
├── reports/
│   └── Informe_Tecnico_TechLogistics.pdf   # Informe ejecutivo para la junta directiva
├── documentacion/                          # Enunciado, checklist de entrega y diccionario de datos
├── requirements.txt
└── README.md
```

## Datos

Cada archivo trae 2.000 lecturas con 14 columnas: 10 variables de sensor (`Agro_1`–`Agro_10` o
`Ener_1`–`Ener_10`), coordenadas (`Latitude`, `Longitude`) y la topología de red (`Source_Node`, `Target_Node`).
Las versiones `*_noise` contienen ruido blanco gaussiano aditivo (AWGN) sobre las señales y un error aleatorio
(*jitter*) sobre las coordenadas GPS, simulando fallas de precisión de los sensores. El detalle de cada variable
está documentado en `documentacion/Lecture_03_dictionary.pdf`.

## Cómo reproducir el análisis

```bash
python -m venv .venv
.venv\Scripts\activate            # En Windows (PowerShell/cmd)
pip install -r requirements.txt

jupyter notebook notebooks/Challenge3_TechLogistics.ipynb
# o, para ejecutarlo completo desde la terminal:
jupyter nbconvert --to notebook --execute --inplace notebooks/Challenge3_TechLogistics.ipynb
```

El notebook lee los datos desde `data/input/`, guarda las figuras en `figures/` y los productos intermedios en
`data/output/`, de modo que es completamente reproducible desde cero.

## Resumen de hallazgos principales

- **Geoespacial (Fase 1):** no se encontró evidencia estadísticamente significativa de agrupamiento espacial en
  las zonas de NDVI bajo (prueba de vecino más cercano, R ≈ 0.99).
- **Series de tiempo (Fase 1):** las variables de mercado y macro de la red eléctrica son no estacionarias I(1);
  el Costo del Gas se comporta como un *random walk con deriva* (drift), no como un random walk puro.
- **Señales (Fase 2):** el ruido inyectado se concentra proporcionalmente en frecuencias medias/altas; un filtro
  Butterworth pasa-bajo reduce el RMSE de reconstrucción en ~60% y el error de pronóstico en ~62%.
- **Grafos (Fase 3):** la topología de red se recupera con exactitud a partir de los datos ruidosos (los IDs de
  nodo no llevan ruido). El "nodo cuello de botella" de la red eléctrica es una subestación (nodo de origen),
  identificada tanto por su betweenness centrality como por su volumen de telemetría.
- **Negocio (Fase 4):** existe causalidad de Granger unidireccional del Factor de Potencia hacia el Voltaje;
  agregar la centralidad del nodo como variable exógena no mejora el AIC del modelo ARIMAX de demanda.

El desarrollo completo, argumentado y con evidencia gráfica, está en `notebooks/Challenge3_TechLogistics.ipynb`
y en `reports/Informe_Tecnico_TechLogistics.pdf`.
