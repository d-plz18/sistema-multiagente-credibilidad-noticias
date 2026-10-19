# Sistema multiagente de análisis de credibilidad de noticias

Proyecto de la asignatura **Sistemas Inteligentes** (3º, UPM). Es un sistema multiagente hecho con [JADE](https://jade.tilab.com/) que recoge noticias de varias fuentes y calcula una puntuación de credibilidad para cada una. Para ello combina tres análisis independientes: sentimiento/sensacionalismo, sesgo y reputación de la fuente.

## Arquitectura

```
 Adquisición (simulada) ─┐
 Adquisición (fichero)  ─┤
 Adquisición (RSS)      ─┼──►  Coordinador  ──►  Sentimiento ──┐
 Adquisición (NewsAPI)  ─┘         │        ──►  Sesgo       ──┼──►  Coordinador  ──►  Visualización (GUI)
                                   │        ──►  Fuente      ──┘     (agrega notas)
                                   └── busca expertos en el DF (Directory Facilitator)
```

| Agente | Función |
|---|---|
| `AgenteAdquisicion*` | Obtienen noticias de un generador simulado, de `resources/noticias.txt`, del RSS de El País o de NewsAPI, y se las envían al coordinador |
| `AgenteCoordinador` | Busca a los agentes expertos en el DF, les reparte cada noticia y agrega sus puntuaciones en un informe final |
| `AgenteSentimiento` | Detecta lenguaje sensacionalista o *clickbait* |
| `AgenteDetectorSesgo` | Busca palabras clave de sesgo y afirmaciones sin fuente |
| `AgenteVerificadorFuente` | Puntúa la reputación del dominio de origen |
| `AgenteVisualizacion` | Muestra los informes de credibilidad en una interfaz Swing |

Los agentes se comunican con mensajes **FIPA-ACL** y filtran por ontología y `conversation-id`. Los expertos se registran como servicios en el DF.

## Estructura

```
src/practica/
├── agentes/     Agentes JADE
├── behaviour/   Comportamientos (Ticker, Cyclic, Waker…)
├── modelo/      Noticia, InformeCredibilidad, ReporteCredibilidad
├── pln/         Analizadores (sentimiento, sesgo, reputación)
├── utils/       Utilidades del DF
└── Main.java    Arranca el contenedor principal y todos los agentes
lib/             jade.jar, commons-codec.jar
resources/       noticias.txt (noticias de ejemplo)
```

## Ejecución

Requiere Java 17 o superior.

```bash
# Compilar
javac -encoding UTF-8 -cp "lib/*" -d bin $(find src -name "*.java")

# Ejecutar (en Windows usa ";" en lugar de ":" en el classpath)
java -cp "bin:lib/*" practica.Main          # con la GUI de JADE
java -cp "bin:lib/*" practica.Main --nogui  # sin la GUI de JADE
```

También se puede abrir directamente como proyecto de Eclipse o IntelliJ.

### NewsAPI (opcional)

El agente de NewsAPI lee la clave de la variable de entorno `NEWSAPI_KEY`. Si no está definida, ese agente se desactiva y el resto del sistema funciona igual.

```bash
export NEWSAPI_KEY=tu_clave   # Windows PowerShell: $env:NEWSAPI_KEY="tu_clave"
```

## Autores

Trabajo en equipo de Diego Plaza ([@d-plz18](https://github.com/d-plz18)) junto con [@Herrervs](https://github.com/Herrervs), Eva Pombo, Irene Quesada, Álvaro Moreno y el resto del grupo. Repositorio original: [Herrervs/Practica-SSII](https://github.com/Herrervs/Practica-SSII).
