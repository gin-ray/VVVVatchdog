# VVVVatchdog — Tasks and Progress

Current plan of work: what’s done and what’s next. Specification and architecture: see `docs/TECHNICAL_SPEC.md` and `docs/ARCHITECTURE.md` (in repo, but `docs/` is gitignored).

---

## Done

- [x] **Records:** `AppConfig`, `AppState`, `WatchdogModel`, `GlobalConfig` defined in VL.
- [x] **Model → Runtime:** Model passed to Runtime via Public Channel; Runtime can access model fields.
- [x] **Runtime:** Working runtime from MVP (in `VVVVatchdog_client.vl`); currently controlled from UI — needs to be driven by model.
- [x] **UI sketch:** Main ImGUI elements outlined (dashboard, log, settings, etc.).
- [ ] **Enum ProcessStatus:** To be done (Stopped, Running, Frozen, Restarting, Error) — see VL docs for how to define enums.

---

## To do (priority order)

### 1. XML file create/open and dialogs (current focus)

- [ ] **Open file dialog:** User can choose an existing XML config file; path is stored (e.g. in `WatchdogModel.ConfigFilePath` or GlobalConfig) and passed to load logic.
- [ ] **Create / Save As dialog:** User can choose path for a new config or save current config under a new path.
- [ ] **Wire dialogs to UI:** Buttons/menu items in ImGUI that trigger “Open…”, “Save”, “Save As…” and show the dialogs.

*After this, the app has a clear “current config file” and a way to create or pick it.*

### 2. Serialization (save/load config)

- [ ] **Load from XML:** Read XML from `ConfigFilePath` (or chosen path) and deserialize into `WatchdogModel` (Apps, GlobalSettings). Handle errors (file missing, invalid XML) and optionally show message in UI.
- [ ] **Save to XML:** Serialize current `WatchdogModel` to XML and write to `ConfigFilePath` (or path from Save As). Handle errors and optionally show message in UI.
- [ ] **Format:** Define XML schema / element names so serialization is stable (see TECHNICAL_SPEC for model fields).

*After this, config is persisted and restorable.*

### 3. Connect model to Runtime

- [ ] **Runtime driven by model:** Runtime uses `WatchdogModel` (Apps, GlobalSettings) as source of truth: which apps to monitor, timeouts, paths, etc. No duplicate state; UI only edits model (or sends commands that result in model updates).
- [ ] **Runtime updates model:** When processes start/stop/restart or errors occur, Runtime updates `AppState` (or relevant parts of the model) so UI always reflects current state.
- [ ] **Config load/save in Runtime:** After implementing serialization, Runtime (or a dedicated helper) performs load/save; UI only triggers “load”, “save”, “save as” and shows errors if Runtime reports them.

### 4. Later (after config and Runtime are wired)

- [ ] Basic data validation when loading/editing (paths, timeouts, port ranges).
- [ ] ProcessHelper: start process, IsResponding, graceful shutdown (Ctrl+C), Kill.
- [ ] Watchdog logic in Runtime: timer, restart limits (boot loop protection).
- [ ] OSC (optional) and system tray (optional).

---

## Notes

- **Public Channels:** Passing the whole `WatchdogModel` via Public Channel to Runtime and to UI is the right approach; Runtime is the only place that *writes* to the model; UI only reads and sends commands.
- **Enum in VL:** ProcessStatus (Stopped, Running, Frozen, Restarting, Error) — to be added when the correct way to define enums in your VL version is clear; keep using string or int for status in the meantime if needed.
