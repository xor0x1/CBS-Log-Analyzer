# Changelog

All notable changes to this project are documented here.
The format loosely follows [Keep a Changelog](https://keepachangelog.com/).
Все заметные изменения проекта собраны здесь.

---

## [Unreleased]
## [1.1.0] — 2026-07-28
### Added / Добавлено
- **Registry hive check / Проверка кустов реестра** — hive files (`COMPONENTS`, `SCHEMA.DAT`, `DRIVERS`, `SYSTEM`) are recognised by their `regf` signature and checked via the header: clean, *dirty* (sequence counters out of sync — the usual cause of `ERROR_BADDB`, data normally intact) or structurally damaged. For a dirty hive the exact correction and counter value are stated. Diagnostic only — files are opened read-only and never modified; only the first 4 KB are read, so size does not matter. / Кусты распознаются по сигнатуре и проверяются по заголовку: чистый, грязный или структурно повреждённый; для грязного указывается конкретное значение счётчика. Только чтение, файл не изменяется.
- **Registry hive corruption profile / Профиль «Повреждён куст реестра»** — recognizes `0x800703f1` / `ERROR_BADDB` / `0xc000014c` and the `MountSMISchemaHive` + `RegLoadKey failed [1009]` pattern, identifies which hive is affected (schema.dat / COMPONENTS / DRIVERS / SYSTEM) and substitutes its path into the diagnostic command. States plainly that DISM and sfc cannot work until the hive is repaired. / Распознаёт ERROR_BADDB и связанные сигнатуры, определяет конкретный куст и подставляет его путь; честно сообщает, что DISM и sfc бесполезны до восстановления куста.
- **Parse completion indicator / Индикация завершения** — the drop zone now reports "done in N s — results below" instead of leaving "reading…" on screen, plus a read-error handler. / Вместо бесконечного «читаю…» показывается время разбора; добавлен обработчик ошибки чтения.
- **Text-only fix steps / Текстовые шаги** — warnings and explanations render without a copy button. / Шаги без команды отрисовываются как пояснения, без кнопки копирования.
- **New HRESULT entries / Новые коды** — `0x800703f1` (ERROR_BADDB), `0xc000014c` (STATUS_REGISTRY_CORRUPT), `0x8007371c` (ERROR_SMI_PRIMITIVE_INSTALLER_FAILED).
- **OS-aware advice / Учёт версии ОС** — detects the Windows version from the log build (7 / 8.x / 10–11) and swaps the suggested commands, cause fixes and observation texts accordingly. On Windows 7 it points to the System Update Readiness Tool (CheckSUR, KB947821) and manual mum/cat replacement instead of `DISM /RestoreHealth`. / Определение версии Windows и подмена команд, починок и текстов наблюдений; на Win7 — CheckSUR вместо несуществующего DISM /RestoreHealth.
- **CheckSUR parsing / Разбор CheckSUR** — extracts corrupt `.mum`/`.cat` packages (Catalog / MUM / Manifest), the summary (detected/repaired breakdown, operation result) and related KB numbers pulled from package names. / Извлечение повреждённых пакетов, сводки и связанных KB-номеров из имён пакетов.
- **dism.log support / Поддержка dism.log** — parses DISM runs (RestoreHealth / ScanHealth), their HRESULT and a pass/fail summary; drop it alongside `CBS.log`. / Разбор запусков DISM с кодами и сводкой; можно подгружать вместе с CBS.log.
- **Observations block / Блок «Наблюдения»** — neutral, explanation-only notes for third-party/informational moments: offline servicing against a missing drive, VSS shadow copies, ResetBase cleanup, pending reboot, corrupt store source, missing mum/cat replacement. / Нейтральный блок пояснений без команд для сторонних/информационных моментов.
- **App icon / Иконка приложения** — magnifier-over-log SVG plus PNG sizes (16–512) and an apple-touch 180. / SVG-иконка и PNG-размеры.
- **More cause profiles / Новые профили причин** — SPP licensing tokens, missing repair source (0x800f081f), update install failure, DISM online source missing (0x80071160). / Токены SPP, отсутствие источника, сбой обновления, недоступный онлайн-источник DISM.

### Changed / Изменено
- **Generic commands no longer contradict the cause / Общий блок команд не спорит с причиной** — when a cause profile is recognized, the generic block defers to the targeted commands instead of offering DISM that the same card calls useless. / При распознанной причине общий блок отсылает к целевым командам вместо DISM, который в той же карточке назван бесполезным.
- More precise SR file status, including a dedicated `store_corrupt` ("source in store is also corrupt") that takes priority over a plain hash mismatch. / Более точный статус файлов SR, включая приоритетный «источник в хранилище тоже повреждён».
- Smarter verdict: cosmetic/noise errors are separated into a *Minor* block; duplicate errors are grouped into unique problems. / Умный вердикт: шум в отдельный блок, дедупликация ошибок в уникальные проблемы.

### Fixed / Исправлено
- **Noise filter / Фильтр шума** — lines like `Current tick count: 800 lower than last tick count: 1479` were not filtered because the regex did not allow a number mid-phrase. / Строки с числом в середине фразы не отсеивались из-за регулярного выражения.
- Unified the visual style of command rows between the *Causes* cards and the *Repair commands* block. / Единый вид строк команд в блоках «Причины» и «Команды для починки».
- Repair-source observation no longer suggests `DISM /RestoreHealth` on Windows 7. / Наблюдение про источник больше не советует DISM /RestoreHealth на Win7.

### Performance / Производительность
- A 40.6 MB CbsPersist log (169k lines) parses in about 6 seconds; earlier reports of long processing were caused by the stale "reading…" label, not by parsing speed. / Лог на 40.6 МБ разбирается за ~6 секунд; жалобы на долгую обработку объяснялись незакрывающейся надписью «читаю…».

---

## [1.0.0]

### Added / Добавлено
- Initial release: single-file, offline `CBS.log` analyzer with verdict, error grouping, root-cause detection with targeted commands, KB tracking, SR file repairs and servicing sessions. / Первый выпуск: однофайловый офлайн-анализатор CBS.log с вердиктом, группировкой ошибок, поиском причин и целевыми командами.
- Bilingual UI (RU/EN) with auto-selection by OS language, a manual switch and `localStorage` persistence. / Двуязычный интерфейс с автовыбором по языку ОС, переключателем и запоминанием.
