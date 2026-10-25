# WORKPLAN — S.U.B.O.T.A.

## ⚙️ Continuous processes (always active)

- Writing tests for code
- Updating `system_map.yaml` with every architectural change

---

## ✅ Stage 1 — Basic Stability `DONE`

- Unified ConfigManager ✅
- Health Check — service availability monitoring ✅
- Watchdog monitoring for internal processes ✅
- Centralized Exception Handler ✅
- Graceful shutdown and restart (SIGTERM, Ctrl+C) ✅
- Configuration validation at startup ✅
- Event logging system (JSON, rotation) ✅
- PostgreSQL — versatile long-term storage ✅
- Secret Manager ✅
- Redis Streams — centralized event queue ✅

---

## ✅ Stage 2 — Basic functionality and communications `DONE`

- Voice input (wake word, streaming processing) ✅
- Network controller (ping, API availability, external IP) ✅
- Telegram bot — two-way communication channel ✅
- JWT authorization for API and web interface + rate limiting ✅
- Notification system (Telegram) ✅
- Basic web interface (logs, device status, script execution) ✅
- Task scheduler with PostgreSQL integration ✅
- API Gateway ✅

---

## ✅ Stage 3 — Expansion and Development Tools `DONE`

- pytest: unit and integration tests ✅
- Notification center (real-time, filtering, history) ✅
- Access control system (roles: admin / user / guest) ✅
- Graceful degradation — functionality maintained during module unavailability ✅
- Database migrations (Alembic) ✅
- Temporal data system (metrics, time series) ✅
- System health dashboard ✅
- Backup and recovery ✅
- Script versioning and dry-run capabilities ✅
- Comprehensive help system (CLI + UI) ✅

---

## ✅ Stage 3.5 — Architectural transformation `PARTIALLY DONE`

### Completed as part of 3.5:

- `SubotaService` → `SubotaInternalService` + `SubotaExternalService` ✅
- `SecureEnvelope` + `SubotaMessage` (Pydantic schemas for two message types) ✅
- HMAC-SHA256 for external services ✅
- `EmergencyService` / `emergency_gate` / `EmergencyBuffer` — adapted for two streams ✅
- `IsolatedRunner` — offloading resource-intensive modules to separate processes ✅
- `ScriptRegistry` — asynchronous management of script documentation ✅
- `system_audit` logger — a "black box" for the system ✅
- `DiscoveryManager` — network device discovery ✅
- Shutdown handled exclusively via `asyncio.Queue` + logged in `audit_logger` ✅
- `ResourceGovernor` — deprecated/removed, replaced by `IsolatedRunner` ✅

### Removed / Canceled in 3.5:

- `scripts/alarm_handler`, `help_script`, `logger_script`, `task_scheduler` — removed as they are unnecessary at this stage
- `Stage 3.6` (mini-OS) — `#CANCELED`, replaced by process isolation

---

## 🟥 Stage 4 — THE FINAL STRETCH TO V3.0.0-alpha.0

### 4.0 — Fixing Known Bugs and Technical Debt `🔴`

- [x] **BUG:** `weather_script/v2.py`, line 148 — `IndexError: list index out of range` when `entities[]` is empty. 
Cause: `city = payload['data']['content']['entities'][0]` — no check for an empty list. 
Fix: Added a guard clause before accessing the index.

- [x] **WORKAROUND:** `core/simple_intent_mapper.py` — temporary intent-mapping logic. 
Action: Explicitly documented and verified that the workaround does not break `intent_processor` in edge cases (empty input, unknown intent).

- [x] **SECURITY:** Strengthen login process via API Gateway (FastAPI). 
Minimum requirements: Rate limiting on `/login`, IP banning after N failed attempts, and logging to `security_audit_log`. 
- Note: Regarding IP banning — the decision to omit this specific feature was a deliberate architectural choice, not an oversight. In a local network environment using dynamic IPs (DHCP), an automatic hard ban would likely cause more issues (self-locking) than it would provide actual security benefits. Instead, we implemented a "soft" time-based rate limit (using `slowapi`), which effectively addresses security concerns without creating operational friction.

- [x] **SCHEDULER:** Added support for recurring tasks in `task_scheduler_script.py`. 
Without this, the `task_scheduler` cannot be considered production-ready.

### 4.1 — Secure Bridge (MQTT + sensor network isolation) `🔴`

- [x] Deploy Mosquitto in Docker (port 8883, TLS).
- [x] Develop an MQTT Bridge: a Python service that relays packets from MQTT to Redis. 
- Implement a rate limiter for incoming packets from sensors. 
- Add an HMAC signature before forwarding to `subota:external:events`.
- [x] Configure `Mikrotik_2`: set up ACLs and isolate IoT devices from `Mikrotik_1`.
- [x] Set up a legacy Android device (running Termux): restricted to sending data only; no incoming commands accepted unless signed.

### 4.2 — PowerDaemon (surviving a blackout) `🔴`

- [x] `PowerDaemon` — reads from `/sys/class/power_supply/` and monitors battery charge.
- [x] Thresholds and hysteresis:
  - 25% (stable for 60 sec) → switch to "Medium" mode, unload LLM from memory. 
  - 20% → switch to "Minimum" mode, disable Tier ≥ 4.
  - 5% → emergency shutdown procedure (notification → "Last Will" signal to sensors → SQLite snapshot → `docker stop` → system shutdown).
- [ ] `SQLite Checkpoint Store`: background saving of the system state snapshot to an SQLite database whenever the mode changes.

### 4.3 — GateKeeper (Internal ↔ External gateway) `🟠`

- [x] Implement `GateKeeper` — an intermediary module between `subota:internal:events` and `subota:external:events`: 
  - Validate incoming packets: schema check + HMAC verification + timestamp drift ≤ 2 seconds. 
  - If a packet lacks a valid signature, drop it and log an incident in the `security_audit_log`. 
  - Replay protection: maintain a cache of the most recent N timestamp/HMAC pairs.

### 4.4 — Fixing a known vulnerability and implementing "sudo" for the S.U.B.O.T.A. system. `🔴`

> Blocks execution; without this, the system is at constant risk of having its entire security framework bypassed.
> This mechanism is intended solely for console input (*CLI_input.py*).
> `TOTP` remains in effect; it simply will not be applied to the console.

- [x] Remove the ability to shut down the system directly via Telegram or the WebUI. Shutting down *S.U.B.O.T.A.* is permitted ONLY via the system console. Telegram and WebUI will have their own specific sets of available commands and features, accessible either with or without TOTP authentication.
- [x] Remove the "workaround" in *CLI_input.py* (the line: `if user_command.startswith('//'):`).
- [x] Commands such as `EXIT_SEQ_REQ` or `RELOAD_MODULE` must now be executed exclusively through the `sudo` chain; the legacy path via `emergency_queue` is deprecated for these operations.
- [x] A password prompt is now triggered directly within *CLI_input.py* whenever a command begins with `subota`.
- [x] Implement an *Authenticator* for commands issued via *CLI_input.py* (Tier 1). This relies on *SecretManager* and *SecurityCenter.report_incident* (which logs every `sudo` action to the *security_forensics* and *system_audit* logs). All authentication outcomes are passed as an *AuthResult* to the *PrivilegeFSM*.
- [x] Implement a foundational system-level *PrivilegeFSM* (currently limited to user switching) — Tier 0. This depends on *SystemStateManager* and *SecurityCenter.report_incident* (logging every `sudo` action to *security_forensics* and *system_audit*); it operates entirely in-memory.
- [x] Create a *PrivilegeCommandRegistry*. This could be implemented as a separate YAML file containing authorized commands, featuring its own signature and loaded via the *KMS* (Key Management System). The *PrivilegeFSM* will interface with the *PrivilegeCommandRegistry* by accessing it through the core infrastructure (*infra*).
- [x] Integrate a lightweight internal security component within the *PrivilegeFSM*. Following successful authentication, *PrivilegeFSM* does not prompt for a password every time; instead, it verifies whether the requested command is accessible to the user. If it is not, the system returns **ACCESS DENIED** and imposes a penalty of 1,000 points. Accumulating these penalties results in a 10-minute *sudo* lockout, accompanied by a notification via Telegram (if feasible). A proposed feature for future versions of S.U.B.O.T.A. involves a "pseudo-subservience protocol": upon reaching a certain penalty threshold, S.U.B.O.T.A. will generate output that is carefully sanitized to simulate compliance.
- [x] Authentication expires after 3 minutes of inactivity or upon execution of the **LOGOUT** command.
- [x] Implement an access module within *Kernel_S.U.B.O.T.A.* (Tier 1)—this module depends on *TaskWatcher*, *EventBus*, *PrivilegeFSM*, and *SecurityCenter.report_incident* (which logs every *sudo* attempt to the *security_forensics* and *system_audit* loggers).
- [x] *SUDO_REQUEST*, *SUDO_AUTHORIZED*, and the final result must all include a shared **request_nonce**, generated at the start of the sequence.
- [x] The *Kernel_S.U.B.O.T.A.* module must not be granted access to any other modules, except for those strictly required for its core functionality.
- [x] *Kernel_S.U.B.O.T.A.* transmits commands to the kernel via a dedicated *asyncio.Queue()*—which will, for the most part, remain empty. Commands for shutting down or manually restarting the module are handled through an existing, separate mechanism. - [x] The kernel invokes modules and returns the result directly to _Kernel\_S.U.B.O.T.A._ via a *callback*.
- [x] The kernel monitors a dedicated **asyncio.Queue()**; upon receiving a result, it triggers a *callback* (associated with the original request) within _Kernel\_S.U.B.O.T.A._.
- [x] Results from _Kernel\_S.U.B.O.T.A._ are routed to a specific *Redis* channel (e.g., _subota:internal:events:cli:sudo_). The *SessionManager* then publishes responses—as it currently does—via a *publish_notification* event to the main *subota:internal:events* channel, allowing the *NotificationManager* to relay the message back to the source.
- [x] *CLI_input.py* is the sole owner of *stdin*; no other S.U.B.O.T.A. component reads directly from *stdin*.
- [x] The logic in *CLI_input.py* must be modified to capture passwords using a secure, hidden input mechanism—an implementation that strictly adheres to the requirement that the "event loop must never be blocked."
- [ ] Cover all the logic with tests and lock it down.

### 4.5 — IsolatedRunner: switching to Redis IPC `🟠`

> Currently, isolated module, NLP, communicate directly via pipes. For the V3.0.0-alpha.0 release, it is sufficient to ensure a stable operational state, but the issue of transferring audio files must be resolved.

- [ ] Resolve the mechanism for transferring audio files between `IsolatedRunner` and the main process. 
Options: a temporary file on disk with the path communicated via Redis, or a Unix Domain Socket. 
Select one approach, document it, and implement it.
- [ ] Ensure that `MetricsCollector` does more than just log metrics from isolated processes—it must also store them in a database.
- [ ] Enable TTS and VOSK (currently disabled) via IsolatedRunner and test their functionality.

### 4.6 — Kernel protection from OOM and swap `🟠`

- [ ] Add a check during Tier 0 initialization: if swap is enabled, abort startup and output an explicit error message to the console.
- [ ] Configure `cgroups` limits for Docker containers in accordance with the RAM budget specified in the Technical Specification.

### 4.7 — Final DoD (Definition of Done V3.0.0-alpha.0) check `🔴 FINAL STEP`

> All 5 criteria must be met on actual hardware (a Lenovo device + at least one physical sensor).

- [ ] **DoD 0 — SSD Drive Check:** Verify the health of the installed Crucial MX500 SSD according to the criteria specified in *SUBOTA_TECH_SPEC.md*.
- [ ] **DoD 1 — Autonomous Bootstrapping:** System startup without manual intervention, from ConfigManager through to Module Loading.
- [ ] **DoD 2 — Infrastructure Stability:** Uptime > 99% over 7 consecutive days of operation.
- [ ] **DoD 3 — Secure Bridge:** The MQTT Bridge must utilize HMAC authentication, operate within an isolated container, and ensure zero packet loss.
- [ ] **DoD 4 — Policy Enforcement:** Correct switching between "Maximum" and "Minimum" modes during a software-simulated loss of internet connectivity or when CPU/RAM usage thresholds are exceeded.
- [ ] **DoD 5 — Minimal Functional Loop:** Sensor → Event Bus → Policy Engine → Actuator, with a latency of < 200 ms on a single physical device (Android sensor).
---

## ─────────────── V3.0.0-alpha.0 RELEASE ───────────────

---