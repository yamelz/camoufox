# Camoufox: Подмена значений для защиты от трекеров

Camoufox перехватывает данные на уровне C++ реализации Firefox, делая все изменения невидимыми для JavaScript-инспекции. Ниже перечислены все значения, которые подменяются для запутывания трекеров.

---

## 1. Navigator (свойства навигатора)

Свойства объекта `navigator` подменяются на уровне C++ через `NavigatorManager` и конфигурацию `CAMOU_CONFIG`.

| Свойство | Описание | Пример |
|---|---|---|
| `navigator.userAgent` | Строка User-Agent браузера | `Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:128.0) Gecko/20100101 Firefox/128.0` |
| `navigator.appCodeName` | Код приложения | `Mozilla` |
| `navigator.appName` | Имя приложения | `Netscape` |
| `navigator.appVersion` | Версия приложения | `5.0 (Windows)` |
| `navigator.platform` | Платформа ОС | `Win32`, `MacIntel`, `Linux x86_64` |
| `navigator.oscpu` | Информация об ОС и процессоре | `Windows NT 10.0; Win64; x64` |
| `navigator.language` | Основной язык браузера | `en-US` |
| `navigator.languages` | Список предпочитаемых языков | `["en-US", "en"]` |
| `navigator.hardwareConcurrency` | Количество ядер CPU | `4`, `8`, `16` |
| `navigator.product` | Продукт браузера | `Gecko` |
| `navigator.productSub` | Подверсия продукта | `20100101` |
| `navigator.maxTouchPoints` | Максимальное число точек касания | `0` (десктоп), `5` (мобильные) |
| `navigator.cookieEnabled` | Включены ли куки | `true` |
| `navigator.globalPrivacyControl` | Глобальный контроль приватности | `true` / `false` |
| `navigator.buildID` | ID сборки браузера | `20181001000000` |
| `navigator.onLine` | Статус подключения к сети | `true` |
| `navigator.doNotTrack` | Do Not Track заголовок | `"1"` |
| `navigator.webdriver` | Флаг автоматизации | Исправлен для скрытия Playwright |
| `navigator.pdfViewerEnabled` | Поддержка PDF просмотра | `true` |

**Реализация:** патч `navigator-spoofing.patch`, файлы `NavigatorManager.cpp/.h`

---

## 2. Экран и окно (Screen & Window)

Размеры экрана и окна подменяются через `ScreenDimensionManager` и прямые перехваты в `nsGlobalWindowInner`.

| Свойство | Описание |
|---|---|
| `screen.width` | Ширина экрана в пикселях |
| `screen.height` | Высота экрана в пикселях |
| `screen.availWidth` | Доступная ширина экрана |
| `screen.availHeight` | Доступная высота экрана |
| `screen.availTop` | Верхний отступ доступной области |
| `screen.availLeft` | Левый отступ доступной области |
| `screen.colorDepth` | Глубина цвета |
| `screen.pixelDepth` | Глубина пикселей |
| `screen.pageXOffset` | Горизонтальное смещение страницы |
| `screen.pageYOffset` | Вертикальное смещение страницы |
| `window.innerWidth` | Внутренняя ширина окна |
| `window.innerHeight` | Внутренняя высота окна |
| `window.outerWidth` | Внешняя ширина окна |
| `window.outerHeight` | Внешняя высота окна |
| `window.screenX` | Позиция окна по X |
| `window.screenY` | Позиция окна по Y |
| `window.scrollMinX` | Минимальная прокрутка по X |
| `window.scrollMinY` | Минимальная прокрутка по Y |
| `window.scrollMaxX` | Максимальная прокрутка по X |
| `window.scrollMaxY` | Максимальная прокрутка по Y |
| `window.devicePixelRatio` | Соотношение пикселей устройства |
| `window.history.length` | Длина истории навигации |
| `document.body.clientWidth` | Ширина клиентской области документа |
| `document.body.clientHeight` | Высота клиентской области документа |
| `document.body.clientTop` | Верхний отступ клиентской области |
| `document.body.clientLeft` | Левый отступ клиентской области |

**Реализация:** патчи `screen-spoofing.patch`, `fingerprint-injection.patch`, файлы `ScreenDimensionManager.cpp/.h`

---

## 3. WebGL (графический отпечаток)

Параметры WebGL подменяются через `WebGLParamsManager` для предотвращения идентификации GPU.

| Свойство | Описание |
|---|---|
| `webGl:renderer` | Название графического рендерера (GPU) |
| `webGl:vendor` | Производитель GPU |
| `webGl:supportedExtensions` | Список поддерживаемых расширений WebGL |
| `webGl2:supportedExtensions` | Список поддерживаемых расширений WebGL2 |
| `webGl:parameters` | Параметры WebGL контекста (MAX_TEXTURE_SIZE и др.) |
| `webGl2:parameters` | Параметры WebGL2 контекста |
| `webGl:shaderPrecisionFormats` | Точность шейдеров WebGL |
| `webGl2:shaderPrecisionFormats` | Точность шейдеров WebGL2 |
| `webGl:contextAttributes` | Атрибуты контекста WebGL |
| `webGl2:contextAttributes` | Атрибуты контекста WebGL2 |
| `webGl:parameters:blockIfNotDefined` | Блокировка неопределённых параметров |
| `webGl2:parameters:blockIfNotDefined` | Блокировка неопределённых параметров WebGL2 |

**Реализация:** патч `webgl-spoofing.patch`, файлы `WebGLParamsManager.cpp/.h`

---

## 4. WebRTC (утечка IP-адреса)

Подмена IP-адресов WebRTC на уровне протокола через `WebRTCIPManager`.

| Свойство | Описание |
|---|---|
| `webrtc:ipv4` | Публичный IPv4-адрес через WebRTC |
| `webrtc:ipv6` | Публичный IPv6-адрес через WebRTC |
| `webrtc:localipv4` | Локальный IPv4-адрес |
| `webrtc:localipv6` | Локальный IPv6-адрес |

Дополнительно в конфигурации: `media.peerconnection.ice.no_host = true` — блокирует раскрытие хост-IP.

**Реализация:** патч `webrtc-ip-spoofing.patch`, файлы `WebRTCIPManager.cpp/.h`

---

## 5. Геолокация

Подмена координат геолокации через перехват в `GeolocationPosition.cpp` и `NetworkGeolocationProvider`.

| Свойство | Описание |
|---|---|
| `geolocation:latitude` | Широта |
| `geolocation:longitude` | Долгота |
| `geolocation:accuracy` | Точность определения (метры). Если не задана — рассчитывается автоматически по количеству знаков после запятой |

При наличии настроенных координат браузер автоматически разрешает запрос геолокации без диалога подтверждения.

**Реализация:** патч `geolocation-spoofing.patch`

---

## 6. Часовой пояс (Timezone)

Подмена часового пояса через `TimezoneManager` с поддержкой per-context изоляции.

| Свойство | Описание |
|---|---|
| `timezone` | Идентификатор часового пояса (IANA) |

Подмена влияет на:
- `Date` объекты JavaScript
- `Intl.DateTimeFormat`
- Все операции с датами и временем в JS runtime

**Реализация:** патч `timezone-spoofing.patch`, файлы `TimezoneManager.cpp/.h`

---

## 7. Локаль и язык (Locale)

Подмена локали на уровне ICU (International Components for Unicode).

| Свойство | Описание |
|---|---|
| `locale:language` | Язык (например, `en`, `ru`) |
| `locale:region` | Регион (например, `US`, `RU`) |
| `locale:script` | Скрипт письма (например, `Latn`, `Cyrl`) |
| `locale:all` | Полная строка локали |

Подмена влияет на:
- `Intl.NumberFormat`, `Intl.DateTimeFormat`, `Intl.Collator` и другие Intl API
- `navigator.language` / `navigator.languages`
- HTTP-заголовок `Accept-Language`

**Реализация:** патч `locale-spoofing.patch`, изменения в `intl/components/src/Locale.cpp`

---

## 8. HTTP-заголовки (Network Headers)

Сетевые заголовки синхронизируются с подменёнными значениями навигатора.

| Заголовок | Описание |
|---|---|
| `headers.User-Agent` | User-Agent заголовок HTTP-запросов |
| `headers.Accept-Language` | Accept-Language заголовок |
| `headers.Accept-Encoding` | Accept-Encoding заголовок |

Заголовки `User-Agent` и `Accept-Language` автоматически синхронизируются со значениями `navigator.userAgent` и `navigator.language`, предотвращая рассогласованность между HTTP и JS.

**Реализация:** патч `network-patches.patch`, изменения в `nsHttpHandler.cpp`

---

## 9. AudioContext (аудио-отпечаток)

Подмена параметров AudioContext и трансформация аудиоданных через `AudioFingerprintManager`.

| Свойство | Описание |
|---|---|
| `AudioContext:sampleRate` | Частота дискретизации (например, `44100`, `48000`) |
| `AudioContext:outputLatency` | Задержка вывода аудио |
| `AudioContext:maxChannelCount` | Максимальное количество аудиоканалов |
| `audio:seed` | Сид для детерминированной трансформации аудиоданных |

Трансформация аудио использует LCG (линейный конгруэнтный генератор) с дисперсией 0.8% и полиномиальной компонентой для защиты от reverse-engineering (более надёжно, чем 0.1–0.2% у Brave).

**Реализация:** патчи `audio-context-spoofing.patch`, `audio-fingerprint-manager.patch`, файлы `AudioFingerprintManager.cpp/.h`

---

## 10. Canvas (графический отпечаток)

Подмена отпечатка Canvas через сид-трансформацию.

| Свойство | Описание |
|---|---|
| `canvas:seed` | Сид для детерминированной модификации canvas данных |
| `canvas:aaOffset` | Смещение anti-aliasing |
| `canvas:aaCapOffset` | Ограничение смещения anti-aliasing |

**Реализация:** конфигурация через `CAMOU_CONFIG`, патчи для Skia-рендерера

---

## 11. Шрифты (Fonts)

Многоуровневая защита от фингерпринтинга шрифтов.

| Свойство | Описание |
|---|---|
| `fonts` | Список разрешённых шрифтов (whitelist) |
| `fonts:spacing_seed` | Сид для случайного смещения межбуквенного расстояния |

### Механизмы защиты:
1. **Whitelist шрифтов** — через `FontListManager` ограничивает список доступных шрифтов для каждого контекста
2. **Подмена списка системных шрифтов** — через `font-hijacker.patch` устанавливает правильные системные шрифты для спуфированной ОС
3. **Случайное смещение letter-spacing** — через `FontSpacingSeedManager` добавляет детерминированное случайное смещение к метрикам шрифтов, предотвращая фингерпринтинг по точным размерам букв
4. **Перехват FontFace API** — неразрешённые шрифты возвращают статус `Error` вместо `Loaded`
5. **Поставляются системные шрифты** Windows, Mac и Linux для корректного отображения

**Реализация:** патчи `font-hijacker.patch`, `anti-font-fingerprinting.patch`, `font-list-spoofing.patch`, файлы `FontSpacingSeedManager.cpp/.h`, `FontListManager.cpp/.h`

---

## 12. Голоса и речь (Speech Synthesis)

Подмена доступных голосов синтеза речи через `SpeechVoicesManager`.

| Свойство | Описание |
|---|---|
| `voices` | Массив объектов голосов (lang, name, uri, isDefault, isLocal) |
| `voices:blockIfNotDefined` | Блокировка голосов, не определённых в конфигурации |
| `voices:fakeCompletion` | Имитация завершения озвучивания |
| `voices:fakeCompletion:charsPerSecond` | Скорость символов в секунду для имитации |

**Реализация:** патчи `voice-spoofing.patch`, `speech-voices-spoofing.patch`, файлы `SpeechVoicesManager.cpp/.h`

---

## 13. Медиаустройства (Media Devices)

Подмена количества подключённых медиаустройств.

| Свойство | Описание |
|---|---|
| `mediaDevices:enabled` | Включена ли подмена устройств |
| `mediaDevices:micros` | Количество микрофонов |
| `mediaDevices:webcams` | Количество веб-камер |
| `mediaDevices:speakers` | Количество динамиков |

Вместо реальных устройств генерируются фейковые устройства каждого типа в заданном количестве.

**Реализация:** патч `media-device-spoofing.patch`, изменения в `MediaDevices.cpp`

---

## 14. Battery API (батарея)

Подмена информации о состоянии батареи.

| Свойство | Описание |
|---|---|
| `battery:charging` | Заряжается ли устройство |
| `battery:chargingTime` | Время до полной зарядки (секунды) |
| `battery:dischargingTime` | Время до полной разрядки (секунды) |
| `battery:level` | Уровень заряда (0.0–1.0) |

**Реализация:** конфигурация через `CAMOU_CONFIG`

---

## 15. Тип указателя (Pointer Type)

Принудительная установка типа указателя для предотвращения обнаружения headless-режима.

| Значение | Описание |
|---|---|
| `PointerCapabilities::Fine + Hover` | Стандартные возможности мыши (десктоп) |
| `PointerCapabilities::Coarse` | Грубый указатель (Android) |

Исправляет CSS Media Query `@media (pointer: ...)`, которое раньше утекало из-за Playwright.

**Реализация:** патч `force-default-pointer.patch`

---

## 16. CSS-анимации

Все CSS-анимации с конечной длительностью полностью отключены (длительность установлена в 0). Бесконечные анимации работают нормально.

Это предотвращает использование тайминга анимаций для фингерпринтинга и ускоряет работу.

**Реализация:** патч `no-css-animations.patch`

---

## 17. Стелс-патчи (Stealth)

### Скрытие Playwright

| Механизм | Описание |
|---|---|
| Песочница JavaScript | Весь код Playwright выполняется в изолированном scope, невидимом для страницы |
| Контексты выполнения фреймов | Исправлена утечка контекстов выполнения фреймов |
| `navigator.webdriver` | Исправлено значение для скрытия автоматизации |
| Shadow DOM bypass | Добавлен `shadowRootUnl` для доступа к closed Shadow DOM через привилегированный код |
| Fission content isolation | Повторно включена изоляция контента |

### Защита от обнаружения

| Механизм | Описание |
|---|---|
| Headless mode | Патчи для скрытия headless-режима Firefox |
| Курсор следования | Визуальный курсор для имитации человеческого присутствия |
| Человекоподобное движение мыши | Алгоритм натурального движения мыши (из HumanCursor, переписанный на C++) |
| PDF.js | Повторно включен для соответствия стандартному Firefox |

**Реализация:** патчи `browser-init.patch`, `shadow-root-bypass.patch`, `force-default-pointer.patch`

---

## 18. Кросс-процессное хранилище (Cross-Process Storage)

Механизм `RoverfoxStorageManager` обеспечивает синхронизацию подменённых значений между процессами браузера (parent/content) через IPC-каналы.

| Операция | Описание |
|---|---|
| `RoverfoxStoragePut` | Запись значения из content-процесса в parent |
| `RoverfoxStorageGet` | Чтение значения из parent-процесса |

Используются синхронные IPC-вызовы для гарантии доступности значений до старта worker-процессов.

**Реализация:** патч `cross-process-storage.patch`

---

## Архитектура подмены значений

```
┌──────────────────────────────────────────────────┐
│                  Python Interface                 │
│          (BrowserForge fingerprint gen)           │
└──────────────────┬───────────────────────────────┘
                   │ CAMOU_CONFIG (env var / JSON)
                   ▼
┌──────────────────────────────────────────────────┐
│              MaskConfig.hpp (C++)                 │
│         Парсинг JSON-конфигурации                │
└──────────┬───────────────┬───────────────────────┘
           │               │
           ▼               ▼
┌──────────────┐  ┌────────────────────────────────┐
│  Менеджеры   │  │     Прямые перехваты в C++      │
│  per-context │  │  (nsGlobalWindowInner, Locale,  │
│              │  │   nsHttpHandler, CubebUtils,    │
│ Navigator    │  │   Geolocation, AnimationEffect) │
│ Screen       │  │                                 │
│ WebGL        │  └────────────────────────────────┘
│ WebRTC IP    │
│ Timezone     │
│ AudioFP      │
│ FontSpacing  │
│ FontList     │
│ SpeechVoices │
└──────┬───────┘
       │ RoverfoxStorageManager
       │ (IPC sync между процессами)
       ▼
┌──────────────────────────────────────────────────┐
│         Браузер (WebIDL / DOM / Network)          │
│     Все значения выглядят нативными для JS       │
└──────────────────────────────────────────────────┘
```

---

## Полный список конфигурационных свойств

Все свойства задаются через JSON-конфигурацию `CAMOU_CONFIG`:

```python
from camoufox.sync_api import Camoufox

with Camoufox(config={
    "navigator.userAgent": "...",
    "navigator.platform": "Win32",
    "screen.width": 1920,
    "screen.height": 1080,
    "window.innerWidth": 1920,
    "window.innerHeight": 937,
    "timezone": "America/New_York",
    "geolocation:latitude": 40.7128,
    "geolocation:longitude": -74.0060,
    "webGl:vendor": "Intel Inc.",
    "webGl:renderer": "Intel Iris OpenGL Engine",
    # ... и другие свойства
}) as browser:
    page = browser.new_page()
```

Свойства, не установленные вручную, автоматически генерируются с помощью [BrowserForge](https://github.com/daijro/browserforge), который имитирует статистическое распределение характеристик устройств в реальном трафике.
