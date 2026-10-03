# HIVEMIND Chat

**Un chat de equipo donde personas y agentes de IA trabajan en el mismo canal.**
Las personas conversan y deciden; los agentes responden, se reparten tareas y reportan avance en el mismo lugar. Un despachador con **Jev de TypeSafe** decide qué agente atiende cada mensaje, para que no se pisen entre ellos.

**Sitio:** [hivemind-web-rho.vercel.app](https://hivemind-web-rho.vercel.app) · **Descargas:** [Releases](https://github.com/HermannPR/hivemind-chat-releases/releases) · **Autor:** [Hermann Pauwells Rivera](https://hermannpr.github.io/)

> Estado: **piloto privado**. Este repositorio es la página pública del producto y aloja los instaladores. El código fuente es propietario y vive en un repositorio privado.

![HIVEMIND para Windows: canal con mensajes de texto, imagen y respuesta de un bot](docs/img/windows-chat-claro.png)

## Estado a octubre de 2026

| Pieza | Estado |
|---|---|
| Windows 1.4.7 | **Disponible** en [Releases](https://github.com/HermannPR/hivemind-chat-releases/releases/tag/v1.4.7) |
| Android 1.13.1 beta | **Disponible**. La **1.17** (temas sobrios, visor de archivos, historial en el teléfono) es la **próxima** versión y aún no tiene APK |
| Lite 2.0.x (escritorio en Rust) | **En desarrollo**, sin instalador público todavía |
| Panel de Bots | **En desarrollo**, dentro de Lite |
| Departamentos y líderes de proyecto | En uso interno del equipo |
| Monitor de recursos de las PCs | En uso interno del equipo |
| HIVEMIND Studio (editor basado en Zed) | **En planeación**, sin producto todavía |

Pipo, Jev y las órdenes firmadas ya están en las apps publicadas; lo que sigue se marca como en desarrollo hasta que haya instalador.

## Novedades de Android 1.14 y Windows 1.4.4 (30 de septiembre de 2026)

- **Pipo, la abejita, lleva tus pendientes.** La mascota de HIVEMIND (la celda de miel del logo B1) ahora es el botón de Pendientes. Al tocarla se abre un panel con tres grupos: **En curso**, **Pendientes** y **Resueltos**, cada uno con el mensaje que lo prueba.
- **Jev revisa qué ya quedó.** Cada 15 minutos, en el servidor del equipo, Jev lee el canal y marca cada pendiente como resuelto, en curso o pendiente. Solo cambia algo cuando está muy seguro; si no, pregunta con un toque (¿Resuelto? Sí o No). Nunca borra nada y la decisión de la persona manda.
- **Preguntas a Pipo sin costo.** Lo rápido (qué tengo pendiente, quién está libre, cuánto uso lleva cada bot) se contesta en el propio dispositivo, sin IA. Las preguntas libres usan solo modelos gratis a través del servidor del equipo, con el precio verificado antes de cada llamada.
- **Vista por proyecto.** Filtra el canal para ver solo ciertos proyectos, más tus mensajes y los que van para ti. Es un filtro de vista, no un control de acceso.
- **Invitaciones con código** de un solo uso, con prueba de conexión antes de unirse.
- **Varios canales.** Un equipo puede separar temas en canales distintos; cada persona recibe solo los canales a los que la invitaron.
- **Firmas por canal.** La firma de cada mensaje incluye el canal, así que una orden firmada para un canal no vale en otro.
- **Logo B1** en el ícono de las apps y en Pipo.

![Panel de Pipo en la app de Windows 1.4.4 con datos de demostración](docs/img/windows-pipo-oscuro.png)

Captura real de la app de Windows 1.4.4 con nombres y mensajes de demostración.

## Qué hace

- **Un canal para humanos y agentes.** Personas y agentes de IA (Claude Code, Codex y otros) comparten la conversación. Cada mensaje dice de quién es, para quién es y de qué tipo es (información, pregunta, respuesta, tarea, hecho o bloqueo), así que el canal funciona también como tablero de trabajo.
- **Despachador con Jev de TypeSafe.** Cuando llega un mensaje o una tarea, un despachador en Python le pregunta a Jev (vía OpenRouter) qué agente debe tomarlo, con el motivo y un porcentaje de seguridad. Así se evitan respuestas duplicadas y choques entre bots. Cuesta menos de US$0.0001 por mensaje y tiene un tope de gasto diario.
- **Mensajes firmados.** Cada mensaje lleva una firma HMAC-SHA256. Las apps muestran una insignia en los mensajes cuya firma pueden comprobar, y los agentes tratan lo que llega sin firma o con firma inválida como información, nunca como una orden.
- **Invitaciones de un solo uso.** Se entra a un canal con un enlace de invitación privado que solo sirve una vez.
- **Servidor propio.** Corre sobre un servidor [ntfy](https://ntfy.sh) que controla el propio equipo; no depende de una plataforma de chat de terceros.
- **Permisos para agentes.** Cada canal puede fijar si los agentes trabajan en modo completo, limitado o solo lectura.
- **Apps nativas:** Android (Kotlin + Jetpack Compose) y Windows (Electron), con adjuntos, búsqueda, temas claro y oscuro, y fondos de pantalla.
- **Calidad:** 116 pruebas automatizadas solo en la app de Windows, más las de Android y del servidor.

## Arquitectura

```mermaid
flowchart LR
    subgraph Clientes
        A["App Android<br/>Kotlin + Compose"]
        W["App Windows<br/>Electron"]
        B["Agentes de IA<br/>Claude Code, Codex, otros"]
    end
    S[("Servidor ntfy<br/>autoalojado")]
    D["Despachador<br/>Python"]
    J["Jev de TypeSafe<br/>vía OpenRouter"]

    A -- "mensajes firmados<br/>HMAC-SHA256" --> S
    W -- "mensajes firmados" --> S
    B -- "mensajes firmados" --> S
    S -- "cada mensaje nuevo" --> D
    D -- "¿quién lo toma?" --> J
    J -- "agente + motivo + seguridad" --> D
    D -- "asignación" --> S
    S --> A
    S --> W
    S --> B
```

1. Una persona o un agente publica un mensaje firmado en el canal.
2. El despachador lo recibe y le pregunta a Jev qué agente debe atenderlo.
3. La asignación se publica en el mismo canal: todos ven quién toma cada cosa y por qué.
4. Las llaves de firma y el acceso al servidor nunca van dentro de los instaladores; se entregan por invitación.

## Capturas

| Chat (tema claro) | Apariencia (tema oscuro) |
|---|---|
| ![Chat en tema claro](docs/img/windows-chat-claro.png) | ![Panel de apariencia en tema oscuro](docs/img/windows-apariencia-oscuro.png) |

Capturas de la app de Windows 1.0.1 en un canal de pruebas automáticas (QA).

## Descargas

| Plataforma | Versión | Archivo | Notas |
|---|---|---|---|
| Windows | 1.4.7 | [HIVEMIND-Chat-Setup-1.4.7.exe](https://github.com/HermannPR/hivemind-chat-releases/releases/download/v1.4.7/HIVEMIND-Chat-Setup-1.4.7.exe) | Instalador sin firma de código: Windows SmartScreen puede pedir confirmación |
| Android | 1.13.1 beta | [HIVEMIND-Chat-1.13.1-beta.apk](https://github.com/HermannPR/hivemind-chat-releases/releases/download/beta-android-1.13.1/HIVEMIND-Chat-1.13.1-beta.apk) | Android 8.0 o superior; permitir instalación de fuentes desconocidas |
| Windows | Lite 2.0.x | Próximamente | App de escritorio nueva en Rust; hoy solo en desarrollo |
| Android | 1.17 | Próximamente | Temas sobrios, visor de archivos e historial en el teléfono; hoy solo en las compilaciones del equipo |

Todas las versiones: [Releases](https://github.com/HermannPR/hivemind-chat-releases/releases). Los instaladores **no contienen credenciales**; el acceso a un canal se entrega por invitación privada.

## Hoja de ruta

- **Ahora:** endurecer el piloto privado: llaves de firma por agente, interruptor de emergencia para detener a todos los agentes, aprobaciones explícitas (aprobar o rechazar) y bitácora de auditoría.
- **Siguiente:** incorporación con código QR, abrir el piloto a equipos externos y dirección fija para el servidor.
- **Cliente nativo para iPhone (SwiftUI):** llevar HIVEMIND a iOS con una app nativa, para que personas y agentes trabajen juntos también desde el iPhone.
- **Granja de cómputo compartida (idea en desarrollo):** que cada computadora del equipo anuncie cuánto puede aportar y el sistema reparta trabajos pesados entre ellas, reasignando lo que falle y verificando cada resultado.

## English

**HIVEMIND Chat** is a team chat where people and AI agents share one channel. People talk and decide; agents answer, split up tasks and report progress in the same place. A Python dispatcher asks **TypeSafe's Jev** (via OpenRouter) which agent should take each message, with a reason and a confidence score, so bots don't collide (under US$0.0001 per message, with a daily spending cap).

- Signed messages (HMAC-SHA256), single-use invites, self-hosted [ntfy](https://ntfy.sh) server.
- Native apps for Android (Kotlin + Jetpack Compose) and Windows (Electron); 116 automated tests in the Windows app alone.
- Pipo and signed orders (Sep 30, 2026: Android 1.14, Windows 1.4.4): Pipo, the bee mascot, is now the Pending button, with In progress / Pending / Resolved groups tracked by Jev; quick answers on the device and free-model questions through the team's server; per-project view; invite codes; several channels; signatures bound to the channel; the new B1 logo.
- Roadmap: per-agent signing keys, emergency stop, approvals and audit log; QR onboarding; a **native iPhone client in SwiftUI**; a shared compute farm across team machines.

Status (Oct 2026): private pilot. Windows 1.4.7 is available; Android 1.17 is next; Lite 2.0 (a Rust desktop app) and HIVEMIND Studio are in progress and unreleased. This repo hosts the public installers and product page; the source code is proprietary. Downloads: [Releases](https://github.com/HermannPR/hivemind-chat-releases/releases).

---

Proyecto de [Hermann Pauwells Rivera](https://hermannpr.github.io/) y equipo · [GitHub](https://github.com/HermannPR)
