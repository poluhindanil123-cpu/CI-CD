# 🚀 Rust / Go / Python / .NET / Go GUI — CI/CD на GitHub

Коллекция лабораторных проектов по настройке **CI/CD через GitHub Actions** с автоматической сборкой и публикацией артефактов. В каждом проекте — свой язык, свой подход к сборке и свой способ публикации.

---

## 📚 Проекты

| # | Проект | Стек | Публикация | Ссылка |
|---|--------|------|------------|--------|
| 1 | **hello-rust** | Rust + Docker | 🐳 GHCR (Docker-образ) | [→ Открыть](https://github.com/poluhindanil123-cpu/hello-rust) |
| 2 | **hello-go** | Go CLI | 📦 GitHub Releases | [→ Открыть](https://github.com/poluhindanil123-cpu/hello-go) |
| 3 | **hello-python** | Python CLI + PyInstaller | 📦 GitHub Releases | [→ Открыть](https://github.com/poluhindanil123-cpu/hello-python) |
| 4 | **hello-dotnet** | C#/.NET CLI | 📦 GitHub Releases | [→ Открыть](https://github.com/poluhindanil123-cpu/hello-dotnet) |
| 5 | **hello-gui** | Go + Fyne (GUI) | 📦 GitHub Releases | [→ Открыть](https://github.com/poluhindanil123-cpu/hello-gui) |
| 6 | **hello-gui** | Go + Fyne (GUI) | 📦 GitHub Releases | [→ Открыть](https://github.com/poluhindanil123-cpu/hello-gui) |
---

## 🦀 1. hello-rust — CI/CD с публикацией в GHCR

Готовый шаблон проекта на Rust с настроенным CI/CD через GitHub Actions и **автоматической публикацией Docker-образа в GitHub Container Registry (GHCR)**.

**Что внутри:**
- Multi-stage `Dockerfile` (сборка в `rust:1-slim`, запуск в `debian:stable-slim`).
- `cargo fmt`, `cargo clippy`, `cargo test`, `cargo build --release`.
- Публикация образа в GHCR с тегами `latest` и short-SHA.

**Структура:**

```
hello-rust/
├── .github/workflows/ci.yml
├── src/
│   ├── main.rs
│   └── lib.rs
├── tests/integration.rs
├── .gitignore
├── .dockerignore
├── Dockerfile
└── Cargo.toml
```

**Ключевые шаги CI/CD:**

| Шаг | Действие |
|-----|----------|
| Format check | Проверка форматирования через `rustfmt` |
| Lint with Clippy | Статический анализ, предупреждения как ошибки |
| Run tests | Юнит- и интеграционные тесты |
| Build release | Сборка релизного бинарника |
| Log in to GHCR | Авторизация в GitHub Container Registry |
| Build and push | Сборка Docker-образа и пуш в GHCR (только для `main`) |

**Результат:** образ доступен по адресу `ghcr.io/<username>/hello-rust:latest`.

🔗 [Открыть репозиторий](https://github.com/poluhindanil123-cpu/hello-rust)

---

## 🐹 2. hello-go — CI/CD на Go CLI

Проект на Go с CI/CD, который автоматически собирает **статический бинарник** и публикует его в **GitHub Releases**.

**Что внутри:**
- `gofmt`, `go vet`, `go test`, `go build`.
- Multi-stage `Dockerfile` (сборка в `golang:alpine`, запуск в `alpine`).
- Публикация бинарников в GitHub Releases.

**Структура:**

```
hello-go/
├── .github/workflows/ci.yml
├── greeting/
│   ├── greeting.go
│   └── greeting_test.go
├── .gitignore
├── .dockerignore
├── Dockerfile
├── go.mod
└── main.go
```

**Особенности:**
- Go компилируется в **один статический бинарник** — JVM/интерпретатор не нужен.
- `CGO_ENABLED=0` для полной статичности.
- Кэш модулей через `actions/setup-go@v5` с `cache: true`.

🔗 [Открыть репозиторий](https://github.com/poluhindanil123-cpu/hello-go)

---

## 🐍 3. hello-python — CI/CD с PyInstaller

Python CLI, упакованный в **один исполняемый файл** через **PyInstaller**, с публикацией бинарников под Linux, macOS и Windows.

**Что внутри:**
- `ruff` для линтинга и форматирования.
- `pytest` для тестов.
- **Матрица сборки** на 3 ОС — потому что PyInstaller **не умеет cross-compilation**.
- Публикация артефактов через `softprops/action-gh-release@v2`.

**Структура:**

```
hello-python/
├── .github/workflows/ci.yml
├── hello/
│   ├── __init__.py
│   └── greeting.py
├── tests/test_greeting.py
├── .gitignore
├── main.py
├── pyproject.toml
└── requirements.txt
```

**Ключевое отличие от Go/Rust:**
- Python **не умеет** cross-compilation — нужен **свой runner** под каждую ОС.
- Бинарник, собранный в Linux-контейнере, **не запустится** на Windows или macOS.

🔗 [Открыть репозиторий](https://github.com/poluhindanil123-cpu/hello-python)

---

## 🚀 4. hello-dotnet — CI/CD на C#/.NET

.NET CLI, собираемый в **один self-contained исполняемый файл** с публикацией под **5 платформ** через `dotnet publish -r <RID>`.

**Что внутри:**
- `dotnet format`, `dotnet build`, `dotnet test`.
- **Cross-compilation через Runtime Identifiers (RID)**: `linux-x64`, `win-x64`, `osx-arm64` и др.
- `PublishSingleFile=true` + `SelfContained=true` — один файл без установки .NET.
- Матрица на 5 RID.

**Структура:**

```
hello-dotnet/
├── .github/workflows/ci.yml
├── src/
│   ├── HelloDotnet.csproj
│   ├── Greeting.cs
│   └── Program.cs
├── tests/
│   ├── HelloDotnet.Tests.csproj
│   └── GreetingTests.cs
└── .gitignore
```

**Ключевое преимущество:**
- В .NET **не нужно** запускать сборку под каждую ОС.
- Один и тот же `dotnet publish -r <RID>` собирает под любую платформу — как `GOOS=windows go build` в Go.

**Матрица релиза:**

| RID | Артефакт |
|-----|----------|
| `linux-x64` | `hello-dotnet-linux-x64` |
| `linux-arm64` | `hello-dotnet-linux-arm64` |
| `win-x64` | `hello-dotnet-windows-x64.exe` |
| `osx-x64` | `hello-dotnet-macos-x64` |
| `osx-arm64` | `hello-dotnet-macos-arm64` |

🔗 [Открыть репозиторий](https://github.com/poluhindanil123-cpu/hello-dotnet)

---

## 🎨 5. hello-gui — CI/CD на Go GUI (Fyne)

GUI-приложение на **Go + Fyne** — самое сложное в плане CI/CD из-за **CGO** и нативных зависимостей.

**Что внутри:**
- GUI-приложение на Fyne с кнопками, полями и диалогами.
- **3 runner'а параллельно** (Linux, macOS, Windows) — потому что CGO требует нативный компилятор под каждую ОС.
- `Dockerfile.test` с GUI-зависимостями (X11, Wayland, OpenGL).
- `MSYS2` на Windows для GCC.
- `-H windowsgui` — скрытие консольного окна.

**Структура:**

```
hello-gui/
├── .github/workflows/ci.yml
├── Dockerfile.test
├── logic.go
├── logic_test.go
├── main.go
├── go.mod
├── go.sum
└── .gitignore
```

**Почему так сложно:**
- Fyne использует **GLFW** — библиотеку для окон, компилируемую из C через CGO.
- GLFW поддерживает **два графических протокола Linux**: X11 и Wayland.
- Для каждого нужны свои заголовки (`xorg-dev`, `libwayland-dev`, `libxkbcommon-dev`).
- `CGO_ENABLED=1` **ломает cross-compilation** → нужен отдельный runner под каждую ОС.

**Главный урок:**
> Как только приложению нужны нативные библиотеки (OpenGL, X11, Wayland, WebKit), один runner уже не справится.

🔗 [Открыть репозиторий](https://github.com/poluhindanil123-cpu/hello-gui)

---

## 📊 Сравнение подходов

| Платформа | Cross-compilation | Runner'ов в CI | Публикация |
|-----------|-------------------|----------------|------------|
| 🦀 **Rust** | ✅ Да | 1 | Docker-образ в **GHCR** |
| 🐹 **Go CLI** | ✅ Да | 1 | Бинарник в **Releases** |
| 🐍 **Python** | ❌ Нет | 3 (по одному на ОС) | Бинарник в **Releases** |
| 🚀 **.NET** | ✅ Да (через RID) | 1 | 5 бинарников в **Releases** |
| 🎨 **Go GUI** | ❌ Нет (CGO) | 3 (по одному на ОС) | 3 бинарника в **Releases** |

---

## 🔑 Что общего во всех проектах

- **GitHub Actions** как CI/CD-платформа.
- **Матричные сборки** (`strategy.matrix`) для параллельной сборки под разные ОС / архитектуры.
- **Кэширование зависимостей** (`actions/cache`, `cache: true` в `setup-*`).
- **Публикация артефактов** через `softprops/action-gh-release@v2` или `docker/build-push-action`.
- **Семантическое версионирование** — релизы запускаются по тегам `v*`.
- **Docker** для изоляции сборки и тестов (где применимо).
- **`.gitattributes`** с `* text=auto eol=lf` для нормализации переводов строк.

---

## 🛠️ Требования

Для работы всех проектов нужно:

1. **Docker Desktop** (Windows / macOS / Linux) — для локальной сборки и тестов.
2. **Git** + **Git Bash** (Windows) или терминал Linux/macOS.
3. **Аккаунт GitHub** + **Personal Access Token** для push.
4. Для GUI-проектов на Windows — **MSYS2** с GCC (в CI ставится автоматически).

---

## 📋 Чек-лист по каждому проекту

- [ ] Репозиторий создан на GitHub (пустой, без README).
- [ ] `go.sum` / `Cargo.lock` / `package-lock.json` закоммичены (где применимо).
- [ ] `.gitattributes` с `* text=auto eol=lf` создан.
- [ ] Push в `main` прошёл, CI — зелёный.
- [ ] Тег `v0.1.0` создан и запушен.
- [ ] Workflow (Job `release`) завершился успешно.
- [ ] Артефакты опубликованы (GHCR или Releases).
- [ ] Бинарник скачан и запущен локально.

---

## 💡 Полезные замечания

| Ситуация | Что использовать |
|----------|------------------|
| `C:/Program Files/Git/...` в Git Bash | `MSYS_NO_PATHCONV=1` перед `docker run` |
| `missing go.sum entry` | `go.sum` не закоммичен |
| Fyne не собирается на Alpine | Используйте `golang:1.23` (Debian-based) |
| `remote origin already exists` | `git remote remove origin` + добавить заново |
| `LF will be replaced by CRLF` | `.gitattributes` с `* text=auto eol=lf` |
| Релиз не создаётся при push в `main` | Релиз только по тегу: `git tag v0.1.0 && git push origin v0.1.0` |
| SmartScreen блокирует `.exe` | «Подробнее» → «Выполнить в любом случае» |
| `gcc.exe not found` на Windows | Action `msys2/setup-msys2@v2` + динамический поиск |

---

## 📄 Лицензия

Проекты созданы в учебных целях. Свободно используйте, модифицируйте и распространяйте.

---

## 👤 Автор

**poluhindanil123-cpu**

- GitHub: [@poluhindanil123-cpu](https://github.com/poluhindanil123-cpu)
- Проекты: [все репозитории](https://github.com/poluhindanil123-cpu?tab=repositories)

---

> ⭐ Если проекты оказались полезны — поставьте звёздочку на GitHub!
