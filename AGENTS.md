# jobber — agent guide

## project overview

jobber is an ai agent that searches and applies for jobs by controlling your browser (see `README.md`).

the repo contains two implementations:

- `jobber/`: a “vanilla” multi-agent approach.
- `jobber_fsm/`: a finite state machine (fsm) based approach (see README notes about structured output dependency).

both implementations are designed to drive a real chrome session via remote debugging.

## repo layout

- `jobber/__main__.py`: entrypoint for the vanilla orchestrator (`python -m jobber`).
- `jobber/config.py`: defines project paths and creates local folders (`temp/`, `jobber/log_files/`, `jobber/user_preferences/`).
- `jobber/core/`: core orchestration and browser control (uses playwright).

- `jobber_fsm/__main__.py`: entrypoint for the fsm orchestrator (`python -m jobber_fsm`).
- `jobber_fsm/config/config.py`: config paths for the fsm variant.
- `jobber_fsm/core/`: fsm agents, models, orchestrator.

- `test/`: evaluation harness.
  - `test/tests_processor.py`: runs task files, executes the orchestrator, and evaluates results.
  - `test/tasks/`: json task definitions (loaded via `load_config`).
  - `test/logs/`, `test/results/`: created/used by the eval harness.

## tech stack

- python (poetry project; see `pyproject.toml`)
- browser automation: playwright (`pytest-playwright`, `playwright-stealth`)
- llm api wrapper: `litellm`
- llm providers: `openai` (plus tracing via `langsmith`, optional `agentops`)

## setup

from `README.md`:

1. install dependencies

```bash
poetry install
```

2. start chrome with remote debugging enabled (do this in a separate terminal)

mac:

```bash
sudo /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
```

linux:

```bash
google-chrome --remote-debugging-port=9222
```

windows:

```bash
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222
```

3. set up env

- copy `.env.example` to `.env` and add keys (openai + langsmith)
- README note: langsmith is currently required unless you comment the callback in `jobber_fsm/core/agent/base.py`.

4. set user prefs

- update `user_preferences.txt` in the implementation you are running:
  - `jobber/user_preferences/user_preferences.txt`
  - `jobber_fsm/user_preferences/user_preferences.txt`
- include the local file path to your resume so the agent can upload it.

## running

- run vanilla:

```bash
python -u -m jobber
```

- run fsm:

```bash
python -u -m jobber_fsm
```

then type a task prompt, e.g.

```text
apply for a backend engineer role based in helsinki on linkedin
```

## evals / testing

the README suggests running evals via the `test/tests_processor.py` harness:

- jobber:

```bash
python -m test.tests_processor --orchestrator_type vanilla
```

- jobber fsm:

```bash
python -m test.tests_processor --orchestrator_type fsm
```

notes:

- the harness writes logs/results under `test/logs/` and `test/results/`.
- it uses playwright, so it requires a working browser setup and (in practice) credentials/logins in your debug chrome session.

## security considerations

- `.env` contains api keys. do not commit it.
- this agent automates real browser sessions and can submit forms; only run it on accounts you control.
- the tool references a local resume file path; avoid storing sensitive docs in the repo.

## Task Implementation
1. **Analyze Requirements**: Refer to `requirements.txt` for detailed feature specifications and system design.
2. **Implementation**: Modify source code in the respective directories (e.g., `src/`, `internal/`).
3. **Verification**: Run provided build and test commands (see above) to ensure correctness.
4. **Push Changes**:
   - Commit changes: `git commit -m "feat: implement <feature>"`
   - Push to remote: `git push origin <branch-name>`
