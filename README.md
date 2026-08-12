# OposiApp — Planificador de Oposiciones (iOS / iPadOS)

Aplicación nativa para iPhone y iPad pensada para organizar la preparación de una oposición: tareas diarias, planificación por calendario, gestión de apuntes/documentos y seguimiento de resultados (tests y deporte) con recomendaciones automáticas de dónde invertir más o menos tiempo.

---

## 1. Objetivo del proyecto

Centralizar en una sola app todo lo necesario para preparar una oposición:

- Qué tengo que estudiar hoy y cuánto tiempo le dedico.
- Qué voy a estudiar cada día durante las próximas semanas/meses.
- Dónde guardo mis apuntes y documentos, organizados por tema.
- Cómo evoluciono en los tests y en la parte física, y en qué debería centrarme más.

Los datos deben quedar guardados en el dispositivo y estar disponibles cada vez que se abra la app, sin depender de un servidor externo en la primera versión.

---

## 2. Stack técnico

| Componente | Tecnología |
|---|---|
| UI | SwiftUI (una sola base de código para iPhone y iPad) |
| Persistencia de datos | SwiftData (local, integrado con SwiftUI) |
| Sincronización futura (opcional) | CloudKit |
| Importación de archivos | `UIDocumentPickerViewController` (vía bridge UIKit) |
| Control de versiones | GitHub |
| Requisito mínimo | iOS 17+ / iPadOS 17+ (por compatibilidad con SwiftData) |

No se requiere backend propio para la versión inicial. Todo el almacenamiento es local en el dispositivo.

---

## 3. Módulos de la aplicación

### 3.1 Tarea diaria + cronómetro
- Listado de tareas asignadas al día actual (procedentes del calendario de planificación).
- Cronómetro con dos modos:
  - **Cuenta arriba**: mide el tiempo real dedicado a una tarea.
  - **Cuenta atrás (Pomodoro)**: temporizador configurable (ej. 25/5 min) con alarma al finalizar.
- Cada sesión registrada queda guardada como una `SesionEstudio` vinculada a la tarea y al bloque/tema correspondiente.

### 3.2 Calendario de planificación
- Vista de calendario donde se pueden crear bloques de temario asignados a un día concreto.
- Cada bloque incluye: tema, horas previstas, y (opcionalmente) notas.
- Al llegar ese día, el bloque aparece automáticamente en el apartado de "tarea diaria".

### 3.3 Gestión de archivos y apuntes
- Importación de archivos (PDF y otros formatos) desde el selector de documentos del sistema.
- Organización en carpetas por bloque/tema.
- Visualización básica de PDF dentro de la app (QuickLook).

### 3.4 Resultados y recomendaciones
- Registro de notas de tests realizados (tema, nota, fecha).
- Registro de marcas deportivas (tipo de prueba, marca, fecha).
- Cruce automático de estos datos con las horas invertidas por tema (`SesionEstudio`) para generar una recomendación simple:
  - Tema con **nota baja y pocas horas** → recomendar aumentar tiempo.
  - Tema con **nota alta y muchas horas** → recomendar reducir tiempo y reforzar otro.
- En una fase posterior, esta lógica podrá evolucionar hacia recomendaciones basadas en IA.

---

## 4. Modelos de datos (SwiftData)

```swift
@Model
class Tema {
    var nombre: String
    var bloque: String
}

@Model
class BloqueTemario {
    var tema: Tema
    var fecha: Date
    var horasPrevistas: Double
    var notas: String?
}

@Model
class SesionEstudio {
    var tema: Tema
    var fecha: Date
    var minutosReales: Int
    var tipoSesion: String // "libre" o "pomodoro"
}

@Model
class DocumentoApunte {
    var nombreArchivo: String
    var rutaLocal: String
    var tema: Tema
    var fechaSubida: Date
}

@Model
class ResultadoTest {
    var tema: Tema
    var nota: Double
    var fecha: Date
}

@Model
class MarcaDeporte {
    var tipoPrueba: String
    var marca: String
    var fecha: Date
}
```

---

## 5. Estructura de carpetas propuesta para el repositorio

```
OposiApp/
├── OposiApp.xcodeproj
├── OposiApp/
│   ├── App/
│   │   └── OposiAppApp.swift
│   ├── Models/
│   │   ├── Tema.swift
│   │   ├── BloqueTemario.swift
│   │   ├── SesionEstudio.swift
│   │   ├── DocumentoApunte.swift
│   │   ├── ResultadoTest.swift
│   │   └── MarcaDeporte.swift
│   ├── Views/
│   │   ├── TareaDiaria/
│   │   ├── Calendario/
│   │   ├── Archivos/
│   │   └── Resultados/
│   ├── ViewModels/
│   ├── Utils/
│   └── Resources/
├── README.md
└── .gitignore
```

---

## 6. Hoja de ruta (roadmap)

1. **Fase 1** – Tarea diaria + cronómetro (cuenta arriba y pomodoro).
2. **Fase 2** – Calendario de planificación de bloques de temario.
3. **Fase 3** – Importación y organización de archivos/apuntes.
4. **Fase 4** – Registro de resultados (tests y deporte) + motor de recomendaciones.
5. **Fase 5 (opcional futura)** – Sincronización entre dispositivos (CloudKit) y recomendaciones asistidas por IA.

---

## 7. Diseño e interfaz

- Estilo limpio y minimalista, coherente con las Human Interface Guidelines de Apple.
- Navegación por pestañas (`TabView`) con los 4 módulos principales.
- Adaptado tanto a iPhone (una columna) como a iPad (vista de dos columnas con `NavigationSplitView`).
