# Production Core SDK

### A software framework for connected production systems.

Production Core is a modular production systems framework built to
connect, control, monitor, and represent the technologies that power
modern live entertainment and immersive environments.
<p align="center">
  <img src="docs/images/Production%20Core%20UI%20.png"
       alt="Production Core SDK interface"
       width="900">
</p>

<p align="center">
  <em>Production Core application interface and production workspace.</em>
</p>
Live productions increasingly depend on interconnected ecosystems of
media servers, video switchers, PTZ cameras, LED processors, networked
devices, OSC/MIDI systems, show control, and custom software.

Production Core explores a simple question:

> What if these systems could share a common software architecture?

The project is being developed from the perspective of real-world
touring, live production, media server, networking, and immersive
technology workflows.

---

## The Vision

Production Core is designed to become a software layer between
production hardware, protocols, applications, and operators.

Instead of every integration becoming another isolated control system,
Production Core provides reusable architecture for:

- State management
- Event-driven communication
- Device representation
- Production modules
- Network discovery
- Application lifecycle
- Logging and diagnostics
- UI/runtime synchronization
- Production system visualization

The long-term goal is not simply remote control.

The goal is to create a framework capable of understanding the
production system itself.

---

## Architecture
<p align="center">
  <img src="docs/images/Core%20system%20logic.png"
       alt="Production Core system architecture"
       width="800">
</p>

<p align="center">
  <em>Production Core manager architecture and system relationships.</em>
</p>
Production Core follows a manager-driven runtime architecture.

STATE → runtime truth  
EVENT → system communication  
LOGGER → runtime history  
CONFIG → persistent configuration  
APP → application lifecycle  
MODULE → production capabilities  
DEVICE → hardware representation  
NETWORK → connectivity and discovery

Modules represent **what the system can do**.

Devices represent **what hardware exists**.

State represents **what is true right now**.

Events allow the system to react when that truth changes.

---

## Current Status

🚧 Production Core is under active development.

The public repository is currently being prepared for its first
developer release.

Documentation, architecture references, examples, and selected
components will be published incrementally.

---

## Built For

Production Core is being designed around real production environments:

- Concert touring
- Media server systems
- Broadcast workflows
- Immersive installations
- Corporate production
- Experiential systems
- Show control
- Production networking

---

## Project Philosophy

Production technology should behave like an interconnected system,
not a collection of isolated devices.

Production Core is an exploration of what happens when software
engineering, production engineering, networking, and real-time
technology are treated as one discipline.

---

## Roadmap

Production Core is actively evolving toward:

- Device discovery
- OSC integration
- PTZ control
- Video switcher integration
- Media server integration
- Live System Map
- External control surfaces
- Production network monitoring
- Extensible third-party modules

A detailed public roadmap is coming soon.

---

## J1VISIONS

Production Core SDK is developed by J1VISIONS.

**Experience Systems Engineering for live entertainment and
immersive technology.**
