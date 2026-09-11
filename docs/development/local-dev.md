# Local development

Guardian components use sibling checkouts so each repository can be built and
released independently:

```text
guardian/
  platform/
  agent-common/
  agent-linux/
  agent-windows/
  agent-android/
  docs/
  translations/
  versions/
```

Clone only the components you are changing. Agent builds expect
`../agent-common`; UI builds expect `../translations`. CI uses the same layout.

## Local setup

The platform repository owns server setup:

```bash
cd platform
./scripts/setup-dev.sh
```

The server uses a project virtual environment at `server/.venv` with dependencies from `server/requirements-dev.txt`.

## Translations

Open new or changed UI strings in the translations repository first. Validate
the product checkout against those catalogs:

```bash
cd translations
GUARDIAN_PRODUCT_ROOT=../platform python scripts/i18n/manage.py check-usage
```

Product pull requests link the translations pull request using
`Translations: #<number>`.

## Server

After setup:

```bash
source .env
cd server
./.venv/bin/python app.py
```

Second terminal:

```bash
source .env
cd server
./.venv/bin/python task_worker.py
```

Default login: **admin** / **admin**. Approve devices at `/admin/devices`.

## Tests

Run the server test suite in parallel:

```bash
source .env
cd server
TESTING=True ./.venv/bin/python -m pytest -q -n auto
```

## Debug agent

```bash
source .env
cd server
./.venv/bin/python debug_agent.py \
  --server-url "ws://127.0.0.1:5000/ws" \
  --agent-version "v1.0.0-dev"
```

Delete `debug-agent.json` to simulate a new device.

## Rust agents

```bash
cd agent-common && cargo test
cd ../agent-linux && cargo check
# Run agent-windows checks on Windows.
```

Full Linux enforcement requires TimeKpr-nExT D-Bus on the target machine.

## Android

Build the native Rust library and generate the UniFFI Kotlin bindings first:

```bash
cd agent-android
./scripts/android-native-build.sh
```

Then build the debug APK:

```bash
./gradlew assembleDebug --no-daemon
```

## Docs site

```bash
cd docs
python3 -m venv .venv-docs
.venv-docs/bin/python -m pip install -r requirements-docs.txt
.venv-docs/bin/mkdocs serve
```

Validate documentation changes with:

```bash
.venv-docs/bin/mkdocs build --strict
```

## Related

* [Contributing](contributing.md)
* [Debug agent](../platforms/debug-agent.md)
