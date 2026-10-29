# Skills

[![skills.sh](https://skills.sh/b/stalexsm/skills)](https://skills.sh/stalexsm/skills)

Agent skills, которыми я пользуюсь каждый день.

Большинство наборов «процессных» скиллов пытаются управлять вами. Эти устроены иначе: маленькие, легко адаптируемые и композируемые. Они работают с любой моделью и любым агентом, понимающим стандарт [Agent Skills](https://agentskills.io). Лежат в репозитории как раз для того, чтобы вы их правили под себя.

## Установка

Требуется Node.js 22.20+. Выполняется из корня проекта одной командой:

```bash
npx skills@latest add stalexsm/skills
```

CLI спросит, какие скиллы взять и куда ставить. По умолчанию скиллы копируются в текущий проект; флаг `-g` делает их доступными во всех проектах. Выберите один способ на каждого агента, иначе получите каждый скилл дважды.

### 1. Установите скиллы

<details open>
<summary><strong>pi</strong></summary>

```bash
npx skills add stalexsm/skills -a pi
```

</details>

<details>
<summary><strong>Claude Code</strong></summary>

```bash
npx skills add stalexsm/skills -a claude-code
```

</details>

<details>
<summary><strong>Codex</strong></summary>

```bash
npx skills add stalexsm/skills -a codex
```

</details>

<details>
<summary><strong>Cursor</strong></summary>

```bash
npx skills add stalexsm/skills -a cursor
```

</details>

<details>
<summary><strong>Любой другой агент</strong></summary>

```bash
npx skills@latest add stalexsm/skills -a <agent>  # opencode, windsurf, amp, goose...; без -a — интерактивный выбор из 75+
```

</details>

### 2. Обновляйте по мере выхода новых версий

```bash
npx skills@latest update
```

Новые скиллы, добавленные в репозиторий, подхватываются повторным запуском `add`.

### 3. Готово, пользуйтесь

В pi скилл вызывается явно через `/skill:rsql`, либо просто опишите задачу словами «SQL», «база», «запрос» — описание скилла само выведет агента на него.

## Скиллы

| Скилл | Описание | Установка |
| --- | --- | --- |
| [`rsql`](./skills/rsql/SKILL.md) | SQL через rsql CLI: 25+ источников, от PostgreSQL и ClickHouse до CSV и Parquet на S3 | `npx skills add stalexsm/skills --skill rsql` |

## Лицензия

[MIT](LICENSE)
