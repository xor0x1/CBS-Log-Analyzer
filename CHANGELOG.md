# Changelog

All notable changes to this project are documented here.
The format loosely follows [Keep a Changelog](https://keepachangelog.com/).
Все заметные изменения проекта собраны здесь.

---

## [Unreleased]

### Added / Добавлено
- **OS-aware advice / Учёт версии ОС** — detects the Windows version from the log build (7 / 8.x / 10–11) and swaps the suggested commands, cause fixes and observation texts accordingly. On Windows 7 it points to the System Update Readiness Tool (CheckSUR, KB947821) and manual mum/cat replacement instead of `DISM /RestoreHealth`. / Определение версии Windows по сборке и подмена команд, починок и текстов наблюдений; на Win7 — CheckSUR вместо несуществующего DISM /RestoreHealth.
- **CheckSUR parsing / Разбор CheckSUR** — extracts corrupt `.mum`/`.cat` packages (Catalog / MUM / Manifest), the summary (detected/repaired breakdown, operation result) and related KB numbers pulled from package names. / Извлечение повреждённых пакетов, сводки и связанных KB-номеров из имён пакетов.
- **dism.log support / Поддержка dism.log** — parses DISM runs (RestoreHealth / ScanHealth), their HRESULT and a pass/fail summary; drop it alongside `CBS.log`. / Разбор запусков DISM с кодами и сводкой; можно подгружать вместе с CBS.log.
- **Observations block / Блок «Наблюдения»** — neutral, explanation-only notes for third-party/informational moments: offline servicing against a missing drive, VSS shadow copies, ResetBase cleanup, pending reboot, corrupt store source, missing mum/cat replacement. / Нейтральный блок пояснений без команд для сторонних/информационных моментов.
- **App icon / Иконка приложения** — magnifier-over-log SVG plus PNG sizes (16–512) and an apple-touch 180. / SVG-иконка и PNG-размеры.
- **More cause profiles / Новые профили причин** — SPP licensing tokens, missing repair source (0x800f081f), update install failure, DISM online source missing (0x80071160). / Токены SPP, отсутствие источника, сбой обновления, недоступный онлайн-источник DISM.
- **HRESULT dictionary entries / Коды HRESULT** — 0x80070003, 0x80071160, 0x800f0906 and others, with `\uXXXX` Inner Error decoding. / Новые коды и декодирование Inner Error.

### Changed / Изменено
- More precise SR file status, including a dedicated `store_corrupt` ("source in store is also corrupt") that takes priority over a plain hash mismatch. / Более точный статус файлов SR, включая приоритетный «источник в хранилище тоже повреждён».
- Smarter verdict: cosmetic/noise errors are separated into a *Minor* block; duplicate errors are grouped into unique problems. / Умный вердикт: шум в отдельный блок, дедупликация ошибок в уникальные проблемы.

### Fixed / Исправлено
- Unified the visual style of command rows between the *Causes* cards and the *Repair commands* block. / Единый вид строк команд в блоках «Причины» и «Команды для починки».
- Repair-source observation no longer suggests `DISM /RestoreHealth` on Windows 7. / Наблюдение про источник больше не советует DISM /RestoreHealth на Win7.

---

## [1.0.0]

### Added / Добавлено
- Initial release: single-file, offline `CBS.log` analyzer with verdict, error grouping, root-cause detection with targeted commands, KB tracking, SR file repairs and servicing sessions. / Первый выпуск: однофайловый офлайн-анализатор CBS.log с вердиктом, группировкой ошибок, поиском причин и целевыми командами.
- Bilingual UI (RU/EN) with auto-selection by OS language, a manual switch and `localStorage` persistence. / Двуязычный интерфейс с автовыбором по языку ОС, переключателем и запоминанием.
