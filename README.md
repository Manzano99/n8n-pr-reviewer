# AI Code Reviewer Bot (n8n + Google Gemini)

> Un bot de revisión de código automatizado que actúa como un programador senior en tus Pull Requests, detectando bugs, vulnerabilidades de seguridad y problemas de rendimiento en tiempo real.

![n8n](https://img.shields.io/badge/Workflow-n8n-JP?style=flat&logo=n8n&color=ff6d5a)
![Gemini](https://img.shields.io/badge/AI-Google%20Gemini%202.0-blue?style=flat&logo=google&logoColor=white)
![GitHub](https://img.shields.io/badge/Platform-GitHub-black?style=flat&logo=github)

## 📖 Descripción

Este proyecto es una implementación que utiliza Inteligencia Artificial para realizar auditorías de código estático.

Cada vez que se abre o actualiza un **Pull Request** en el repositorio, el bot intercepta los cambios, los procesa y publica un comentario detallado con feedback técnico priorizado.

## Arquitectura del Flujo

El sistema está construido sobre **n8n** y sigue una arquitectura ETL lineal:

1.  **Trigger (GitHub):** Escucha eventos `pull_request.opened` y `pull_request.synchronize`.
2.  **Extract (HTTP):** Descarga el `diff` crudo del PR para analizar solo los cambios.
3.  **Transform (JavaScript):**
    * **Sanitización:** Elimina archivos irrelevantes (`package-lock.json`, `.min.js`, etc.) para ahorrar tokens.
    * **Validación:** Trunca diffs excesivamente largos para proteger la ventana de contexto.
    * **Preparación:** Construye un prompt estructurado separando Contexto (System) y Datos (User).
4.  **Process (AI Agent):** Utiliza **Google Gemini 2.0 Flash** para analizar la lógica, seguridad y eficiencia del código.
5.  **Load (GitHub):** Publica el análisis como un comentario en el PR original.

<img width="1237" height="537" alt="image" src="https://github.com/user-attachments/assets/52a7a8f5-eb78-4e51-9fba-1ed66e4d1b32" />


## Funcionalidades Clave

-   ** Auditoría de Seguridad:** Detecta credenciales hardcodeadas (API Keys, Passwords) e inyecciones (SQL/XSS).
-   ** Análisis de Rendimiento:** Identifica bucles ineficientes (O(n^2)) y operaciones costosas.
-   ** Clean Code:** Sugiere mejoras de legibilidad y buenas prácticas.
-   ** Context Aware:** Distingue entre archivos de configuración y lógica de negocio.
-   ** Feedback Automático:** Comenta directamente en GitHub en formato Markdown.

## Ejemplo de Resultado

Cuando se sube código con errores intencionados, el bot responde así:

> **Automated Code Review**
>
> **Alta Prioridad:**
> * **Seguridad:** La contraseña `DB_PASSWORD` está hardcodeada. Vulnerabilidad crítica. Usar variables de entorno.
> * **Seguridad:** Vulnerabilidad a Inyección SQL en `getUserData`. Concatenación directa de strings detectada.
>
> **Media Prioridad:**
> * **Performance:** Complejidad O(n^2) detectada en `processAllUsers`. Bucles anidados innecesarios.
>
> *Powered by Google Gemini 2.0 Flash*

## Instalación y Uso

### Prerrequisitos
* Una instancia de **n8n** (Self-hosted o Cloud).
* Cuenta en **Google AI Studio** (API Key gratuita).
* Cuenta en **GitHub** con permisos para crear Personal Access Tokens.

### Configuración

1.  **Clonar:** Descarga el archivo `json` de este repositorio.
2.  **Importar:** En n8n, usa "Import from File" y selecciona el JSON.
3.  **Credenciales:** Configura las credenciales en n8n para:
    * `GitHub API` (Necesita scopes `repo` y `admin:repo_hook`).
    * `Google Gemini API`.
4.  **Activar:** Enciende el flujo (`Active`). El nodo Trigger registrará automáticamente el Webhook en tu repositorio de GitHub.
