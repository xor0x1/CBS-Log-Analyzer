# CBS Log Analyzer

> Offline analyzer for Windows Servicing logs (`CBS.log` / `DISM.log`). Single HTML file, runs entirely in the browser — nothing is uploaded.
>
> Офлайн-анализатор логов обслуживания Windows (`CBS.log` / `DISM.log`). Один HTML-файл, работает целиком в браузере — ничего никуда не отправляется.

![status](https://img.shields.io/badge/status-stable-green) ![license](https://img.shields.io/badge/license-MIT-blue) ![deps](https://img.shields.io/badge/dependencies-none-lightgrey)

---

## 🇬🇧 English

### What it is

Reading a raw `CBS.log` means scrolling through tens of thousands of lines to find a handful that matter. **CBS Log Analyzer** turns that into a clear verdict: what operation ran, whether there are real problems, and exactly what to do about them.

Drag a log onto the page and read top to bottom: **verdict → causes → commands**. It is a single static HTML file with no dependencies, no build step, and no network calls — the file you open is the whole app.

### Features

- **One-second verdict** — "clean" or "problems found", separating critical issues from noise. Cosmetic errors (timer glitches, isolated deltastore hits, etc.) don't raise a false alarm; they go to a separate *Minor* block.
- **Operation & time range** — detects sfc /scannow, DISM RestoreHealth, update installs or background servicing, and shows the time span.
- **Error grouping** — dozens of identical lines collapse into unique problems (e.g. 56 lines → 26 groups), sorted by frequency.
- **Root cause, not symptom** — recognizes common scenarios and gives *targeted* commands:
  - component store corruption → DISM / sfc
  - corrupt SPP licensing tokens → reset sppsvc + tokens.dat (with a note that DISM won't help here)
  - missing repair source (0x800f081f) → DISM with /Source
  - failed update install → restore + Windows Update queue reset
  - DISM online source missing (0x80071160) → /Source from ISO + /LimitAccess instead of retrying online
- **dism.log support** — parses `dism.log` too (drop it alongside CBS): lists each DISM run (RestoreHealth / ScanHealth …), its time and final HRESULT, with a pass/fail summary so you can see what was already tried.
- **CheckSUR / corrupt packages** — extracts the System Update Readiness block: each corrupt `.mum`/`.cat` package with its type (Catalog / MUM / Manifest) and report note, plus a summary with the corruption breakdown (Manifest / Metadata / Payload), operation result, and any related KB numbers pulled from the package names.
- **OS-aware advice** — detects the Windows version from the log build (7 / 8.x / 10–11) and adapts the suggested commands and observation texts accordingly: on Windows 7 it recommends the System Update Readiness Tool (CheckSUR, KB947821) and manual mum/cat replacement instead of `DISM /RestoreHealth`, which does not exist there.
- **Observations** — a neutral block for third-party / informational moments (offline servicing against a missing drive, VSS shadow-copy activity, component cleanup / ResetBase, pending reboot, store source corrupt, missing mum/cat replacement). Explanations only, no commands.
- **HRESULT decoding** — common codes (0x800f081f, 0x80073712, 0x8007000d, 0x80070003, 0x80071160, …) in plain language; also decodes `\uXXXX` Inner Error text.
- **Context linking** — shows which installer / package / KB / component was failing.
- **KB updates & SR file repairs** — flags failed KBs, tracks SR file repairs and servicing sessions with their HRESULT.
- **Convenience** — text search and level filter in the table, "Copy report" button (text summary), multi-file support (cbs.log + cbs.persist.log).
- **Bilingual UI** — auto-selects RU/EN from the OS/browser language, with a manual RU/EN switch; choice is remembered via `localStorage`.

### Usage

1. Download `cbs-analyzer.html`.
2. Open it in any modern browser (no internet required).
3. Drag your `CBS.log` onto the page — or click to choose a file. You can drop `CBS.log` and `dism.log` together for a combined analysis.
4. Read the verdict, then the causes/observations, then the suggested commands.

Logs are usually at `C:\Windows\Logs\CBS\CBS.log` and `C:\Windows\Logs\DISM\dism.log`.

### Privacy

Everything runs locally in your browser. No file is ever uploaded; there are no analytics and no network requests.

### Limitations

Analysis is heuristic, based on known CSI/CBS markers. It saves time reading the log but does not replace diagnosis — the final repair decision always rests with DISM/sfc and good judgement.

### Extending

New cause profiles and observations are easy to add: one entry in `CAUSE_TESTS` / `OBS_TESTS` (a regex test) plus the matching text in the `I18N` dictionary for both languages.

---

## 🇷🇺 Русский

### Что это

Чтение сырого `CBS.log` — это пролистывание десятков тысяч строк ради нескольких важных. **CBS Log Analyzer** превращает это в понятный вердикт: какая операция выполнялась, есть ли реальные проблемы и что конкретно с ними делать.

Перетащите лог на страницу и читайте сверху вниз: **вердикт → причины → команды**. Это один статический HTML-файл без зависимостей, сборки и сетевых запросов — открытый файл и есть всё приложение.

### Возможности

- **Вердикт за секунду** — «чисто» или «есть проблемы», с разделением критичного и шума. Косметические ошибки (сбой таймера, единичные deltastore и т.п.) не поднимают ложную тревогу, а выносятся в блок *Незначительное*.
- **Тип операции и период** — определяет sfc /scannow, DISM RestoreHealth, установку обновлений или фоновое обслуживание; показывает диапазон времени.
- **Группировка ошибок** — десятки одинаковых строк сворачиваются в уникальные проблемы (например, 56 → 26), отсортированные по частоте.
- **Корневая причина, а не симптом** — распознаёт типовые сценарии и даёт *целевые* команды:
  - повреждение хранилища компонентов → DISM / sfc
  - повреждённые токены лицензирования SPP → сброс sppsvc + tokens.dat (с пометкой, что DISM здесь бесполезен)
  - нет источника восстановления (0x800f081f) → DISM с /Source
  - сбой установки обновления → восстановление + сброс очереди Windows Update
  - DISM: онлайн-источник недоступен (0x80071160) → /Source из ISO + /LimitAccess вместо повтора онлайн
- **Поддержка dism.log** — разбирает и `dism.log` (кидайте его вместе с CBS): показывает каждый запуск DISM (RestoreHealth / ScanHealth …), время и итоговый HRESULT, со сводкой успех/сбой — видно, что уже пробовали.
- **CheckSUR / повреждённые пакеты** — извлекает блок System Update Readiness: каждый повреждённый пакет `.mum`/`.cat` с типом (Catalog / MUM / Manifest) и причиной репорта, плюс сводка с разбивкой повреждений (Manifest / Metadata / Payload), результатом операции и связанными KB-номерами, извлечёнными из имён пакетов.
- **Учёт версии ОС** — определяет версию Windows по сборке из лога (7 / 8.x / 10–11) и подстраивает команды и тексты наблюдений: на Windows 7 рекомендует System Update Readiness Tool (CheckSUR, KB947821) и ручную подстановку mum/cat вместо `DISM /RestoreHealth`, которого там нет.
- **Наблюдения** — нейтральный блок для сторонних / информационных моментов (offline-обслуживание по несуществующему диску, теневые копии VSS, очистка компонентов / ResetBase, ожидание перезагрузки, повреждённый источник в store, отсутствие замены mum/cat). Только пояснения, без команд.
- **Расшифровка HRESULT** — частые коды (0x800f081f, 0x80073712, 0x8007000d, 0x80070003, 0x80071160, …) человеческим языком; декодирует и текст Inner Error из `\uXXXX`.
- **Привязка к контексту** — показывает, какой инсталлер / пакет / KB / компонент падал.
- **Обновления KB и починка файлов SR** — помечает сбойные KB, отслеживает SR-починку файлов и сессии обслуживания с их HRESULT.
- **Удобство** — поиск и фильтр по уровню в таблице, кнопка «Скопировать отчёт» (текстовая сводка), поддержка нескольких файлов сразу (cbs.log + cbs.persist.log).
- **Двуязычный интерфейс** — автоматически выбирает RU/EN по языку ОС/браузера, с ручным переключателем RU/EN; выбор запоминается через `localStorage`.

### Как пользоваться

1. Скачайте `cbs-analyzer.html`.
2. Откройте в любом современном браузере (интернет не нужен).
3. Перетащите свой `CBS.log` на страницу — или нажмите, чтобы выбрать файл. Можно перетащить `CBS.log` и `dism.log` вместе — анализ объединится.
4. Прочитайте вердикт, затем причины/наблюдения, затем предложенные команды.

Логи обычно лежат в `C:\Windows\Logs\CBS\CBS.log` и `C:\Windows\Logs\DISM\dism.log`.

### Приватность

Всё выполняется локально в браузере. Файл никуда не загружается, нет аналитики и сетевых запросов.

### Ограничения

Анализ эвристический, по известным маркерам CSI/CBS. Он экономит время на чтении лога, но не заменяет диагностику — итоговое решение по починке всегда за DISM/sfc и здравым смыслом.

### Расширение

Новые профили причин и наблюдения добавляются легко: одна запись в `CAUSE_TESTS` / `OBS_TESTS` (regex-тест) плюс соответствующий текст в словаре `I18N` для обоих языков.

---

## Author / Автор

**xor0x1 (aka Lunik)** — [github.com/xor0x1](https://github.com/xor0x1)

Contributions, issues and pull requests are welcome. /
Предложения, issue и pull request приветствуются.

## License / Лицензия

[MIT](LICENSE) © 2026 xor0x1 (Lunik)
