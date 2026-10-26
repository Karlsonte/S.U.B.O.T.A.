# ⚙️ S.U.B.O.T.A. 
#### (*System Unit of Binary Operations & Technical Assistance*)
#### Your home that knows you
##### Multifunctional Local Assistant and Automation Platform [Alpha Version]
![Python](https://img.shields.io/badge/Python-3.13%2B-blue?style=flat-square&logo=python)
![Asyncio](https://img.shields.io/badge/Core-Asyncio-4D88FE?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/API-FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/DB-PostgreSQL-316192?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Event%20Bus-Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Status](https://img.shields.io/badge/Status-Alpha%20%2F%20In%20Dev-orange?style=flat-square)
![Tests](https://img.shields.io/badge/Tests-40%2B%20Pytest-green?style=flat-square)
![License](https://img.shields.io/github/license/Karlsonte/Subota-Multifunctional-Assistant?style=flat-square)

### Introduction and Motivation

The S.U.B.O.T.A. project is inspired by the idea of creating a **maximally autonomous, locally controlled assistant** that serves not only as a voice interface but also as a centralized platform for automation and security.

**The idea is inspired by the concept of a "Servitor"** — a purpose-built assistant designed to perform specific tasks according to defined rules and under its creator's control. In S.U.B.O.T.A., this concept represents a reliable digital assistant that can monitor, automate, and manage tasks while remaining predictable and controllable.

### Key Goal of the Project

#### "Jarvis... Sometimes you gotta run before you can walk" - Tony Stark (Iron Man 2008)

Create a **private, extensible, and fault-tolerant ecosystem** where the core logic runs locally and the user retains control over system data, automation, and device management.

The main emphasis is on:

1.  **Data Sovereignty:** The user retains full control over data and functionality.
2.  **Modular Architecture:** Ensuring easy addition of new features and system resilience to failures.
3.  **Multi-Channel Interaction:** Support for voice control, Telegram, Web/REST API.

<p><br></p>

## 1. Historical Context and Iterations (S.U.B.O.T.A. 1.0 & 2.0)

The S.U.B.O.T.A. project went through two prototype iterations, which became a critical foundation for the design of the current asynchronous platform (3.0). These prototypes demonstrated both the potential of the idea and the architectural limitations of a synchronous, monolithic approach.

### S.U.B.O.T.A. 1.0 — The First Attempt at JARVIS

The idea for S.U.B.O.T.A. started with a simple inspiration: JARVIS from *Iron Man*.

I have always loved the technology in the Marvel *Iron Man* films and Christopher Nolan's *The Dark Knight* trilogy. For me, Tony Stark's most fascinating creation wasn't his suit or his cars — it was JARVIS. Batman had the Batcomputer, too: an incredibly capable system, but ultimately a computer following rules. JARVIS felt closer to the kind of intelligent assistant I wanted to build.

I knew I couldn't create a real JARVIS. I had only just finished learning Python, and typical beginner projects — simple websites and Twitter clones — didn't excite me. I wanted to try something more ambitious, even if I had no idea how far I could take it.

So I started experimenting.

I found a basic YouTube tutorial and spent a couple of hours copying a rough prototype. It wasn't much: an endless loop, a microphone, Vosk speech recognition, and a single command — "time" — that returned the system time. Recognition was unreliable, and the command had to match the expected phrase almost exactly. Still, it worked. Sometimes.

I added a few more commands for things like weather, Steam, and opening a browser. But expanding the command list meant adding more phrase variations and more `if/else` statements. That approach quickly became tedious, and I didn't yet know how to solve the problem properly.

There was one feature I was particularly proud of: **S.U.B.O.T.A. could do math using spoken language.** I could say, "Calculate seven point zero five plus eight point two," and it would work out the result and speak it back as "fifteen point two five."

Eventually, I reached the limits of what I knew. Rather than keep adding features without understanding how to build the system properly, I put the project aside and started studying what other people had done.

That was the end of the first attempt — but not the end of S.U.B.O.T.A.

##### 🖼️ **Code Demo**
* [numbers_text_to_int.py](./showcase_code/v1_0/numbers_text_to_int.py)
---

### S.U.B.O.T.A. 2.0 — From Voice Commands to Experiments

A few months later, I returned to S.U.B.O.T.A.. I wanted to get a job in IT, and I felt I needed a serious project — something more substantial than another tutorial.

It was also the beginning of the AI boom. I started reading other people's code, watching tutorials, and exploring ways to make the assistant more capable.

I began with familiar building blocks: a Telegram bot, server time, and weather information. Each feature taught me something different. The system could respond to commands, retrieve information from the internet, and communicate beyond the local console by sending messages through Telegram.

I also began thinking about **protocols**: groups of functions that could be executed together in response to a command. One of the ideas that emerged during this period was using S.U.B.O.T.A. for home security.

But the old problem returned. As I added more commands, the growing collection of `if/else` statements became increasingly difficult to maintain. I first experimented with matching recognized phrases against a dictionary and choosing a command when the match passed a similarity threshold. It wasn't flexible enough: similar phrases could be misinterpreted, while supporting more variations required a lot of manual work.

#### Training My Own NLP Model

I started studying natural language processing and eventually trained my own NLP model on a CPU. I prepared a dataset of more than 10,000 lines by hand.

The model learned to recognize user intents and extract entities. For example, a request such as "Show me the weather in Kyiv" could be represented as a `weather` intent with `Kyiv` as an entity. The model supported around ten intents, but they were my own commands, designed for the system I was building.

Preparing the dataset turned out to be less time-consuming than training the model on a CPU. Despite its limitations, this was an important milestone: **the custom NLP model I trained during this period is still part of S.U.B.O.T.A. 3.0.**

#### Computer Vision Experiments

I also experimented with computer vision using a small YOLO model.

One of my funniest experiments happened when the model identified my long-haired cat as a sheep.

But I was also able to get a basic security scenario working. I tested whether the camera system could detect a person holding a potentially dangerous object, such as a knife or scissors. I walked in front of the camera with empty hands and then with scissors, and the system detected the scenario during my tests.

I connected the detection to notifications: Telegram sent a warning, while email delivered a photo from the camera.

It was an early experiment rather than a finished security product, but it showed me how voice processing, computer vision, and external notifications could work together.

#### When Adding Features Wasn't Enough

As the project grew, I experimented with Python's `threading` to run different tasks alongside one another. I expected it to solve my performance problems, but coordinating data between threads became difficult, and the system grew increasingly fragile.

The more features I added, the harder it became to understand, maintain, and debug the whole application. I eventually stopped trying to restore the entire system as a working product and began treating S.U.B.O.T.A. 2.0 as an experimental playground.

I needed more than additional features. I needed to understand how to structure the system itself.

That realization led me to study software architecture — and eventually to rethink S.U.B.O.T.A. from the ground up.

* ##### 🖼️ **Code Demo**
* [plugin.py](./showcase_code/v2_0/plugin.py)
* [protocol_watch_dog.py](./showcase_code/v2_0/protocol_watch_dog.py)

<p><br></p>

### S.U.B.O.T.A. 3.0 — Asynchronous, Event-Driven Core

<figure align="center">
    <img src="./subota_3.0.png" alt="S.U.B.O.T.A. 3.0 Architecture" width="700" />
    <figcaption style="text-align: center; font-size: 0.9em; color: #555;">
        Made with <a href="http://www.gitvizz.com" target="_blank">GitWizz</a>
    </figcaption>
    <p><br></p>
</figure>

**Key Idea:** Create a fully **asynchronous, modular, and fault-tolerant** core for digital butler tasks, real-time monitoring, and device management. S.U.B.O.T.A. 3.0 addresses the scalability and reliability challenges encountered in versions 1.0 and 2.0.

**Architecture:** An asynchronous, event-driven architecture built around Python `asyncio` and a **Redis Event Bus** for communication between modules. The system uses FastAPI and Uvicorn for its API layer, with Docker-based infrastructure services including Redis and PostgreSQL.

**Technologies (Key Components):**

* **Asynchronous Core:** Python `asyncio`, FastAPI, Uvicorn, `aioredis`, `asyncpg`.
* **State and Storage:** PostgreSQL, Redis, ContextManager, EventBus, Alembic.
* **Monitoring and Reliability:** TaskWatcher, HealthChecker, Graceful Shutdown, MetricsCollector, Backup/Restore.
* **Interaction:** Voice Input/Output (Vosk/SileroTTS), Telegram Bot, WebUI (Bootstrap/Font Awesome), API Gateway, WebSockets.
* **Security and Access Control:** AuthManager (JWT), RBACManager, SecretManager.
* **Testing:** A new Pytest-based test suite is being developed following the core rewrite, with 40+ tests currently in place.

#### Key Achievements in 3.0

1. **Event-Driven Core:** Introduced a Redis Event Bus to decouple modules and establish a common communication mechanism between components such as IntentProcessor, ScriptRunner, and notification senders.
2. **Monitoring and Health Management:** Developed TaskWatcher and MetricsCollector to track process status and resource usage, providing a foundation for the Health Dashboard.
3. **Unified Interaction:** Connected voice input, CLI, Telegram, and WebUI through shared session and notification management, allowing responses to be routed back to the originating interaction channel.
4. **Access Control:** Implemented JWT-based authentication and role-based access control for API and UI operations.
5. **Testability:** Rebuilding the test suite around the rewritten core, with a focus on unit and integration testing using `pytest` and `pytest-asyncio`.

#### Source Code

The source code for S.U.B.O.T.A. 3.0 will be made publicly available after the release of **V3.0.0-alpha.0**.

##### [Roadmap](./WORKPLAN_S.U.B.O.T.A..md)
##### [Architecture overview](./architecture_overview.md)
