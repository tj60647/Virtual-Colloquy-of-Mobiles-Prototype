# Integration pointer - the live system is elsewhere

This document used to point at a cross-repository integration guide. It pointed at a path
that no longer exists, and the integration it described is no longer split across two
repositories.

The whole live system - the ABM engine, the simulation server, the display client, Scene
Studio, the guide, the clip editor, the simulation console, and the sensor, actuator and
transmitter clients - now lives in one place:

- [Virtual-Colloquy-of-Mobiles](https://github.com/tj60647/Virtual-Colloquy-of-Mobiles)

Its integration and runtime contracts are documented there, under `docs/infrastructure/`
(start with `RUNTIME_INTEGRATION_GUIDE.md` and `SIMULATION_SCENEGRAPH_INTEGRATION.md`).

This repository is an archived prototype. Nothing here is a contract for anything.
