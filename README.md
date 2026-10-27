# LibreCapsula Wiki

Исходники вики [librecapsula.github.io](https://librecapsula.github.io/) -
открытой документации по колонкам VK Capsula: железо, прошивки, джейлбрейк
и инструменты сообщества [LibreCapsula](https://github.com/librecapsula).

Движок - [MkDocs Material](https://squidfunk.github.io/mkdocs-material/):
контент в `docs/**/*.md`, пуш в `main` собирается GitHub Actions'ом и
деплоится на GitHub Pages.

## Локальная сборка

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

mkdocs serve            # превью: http://127.0.0.1:8000
mkdocs build --strict   # проверочная сборка (должна проходить без warnings)
```

Пайплайн CI собирает с `--strict`: битые ссылки, страницы вне навигации
и прочие warnings валят сборку - прогоняйте `mkdocs build --strict` локально
перед коммитом.

## Как добавить страницу

1. Создайте `docs/<раздел>/<страница>.md`.
2. Добавьте её в `nav` в `mkdocs.yml` (страницы вне `nav` дают warning).
3. Проверьте `mkdocs build --strict`, закоммитьте, откройте PR.

Стиль и правила контента - в [docs/contributing.md](docs/contributing.md).

## Пайплайны

- `.github/workflows/deploy.yml` - пуш в `main`: сборка + деплой на
  GitHub Pages (источник Pages: «GitHub Actions»).
- `.github/workflows/ci.yml` - PR: strict-сборка + проверка внешних ссылок
  (lychee).

## Первичная публикация (один раз)

1. Создать репозиторий `librecapsula/librecapsula.github.io` (public).
2. Запушить `main`.
3. В настройках репозитория Settings → Pages выставить Source:
   **GitHub Actions** (или `gh api -X POST
   repos/librecapsula/librecapsula.github.io/pages -f build_type=workflow`).
4. Дождаться прогона workflow `deploy` - сайт появится на
   <https://librecapsula.github.io/>.
