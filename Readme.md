# CI/CD Pipeline Extension: Coverage, Security Scan, Deployment

## Objective

Extend the existing Jenkins pipeline to:
- run code coverage analysis using `coverage.py`
- run a security scan using `bandit`
- perform a simple deployment step after a successful build

The goal is to have a Jenkins build that passes all stages, including the new **Coverage** and **Security Scan** stages.

---

## Files Modified

### requirements.txt

I updated `requirements.txt` so that the tools used in the pipeline are installed into the virtual environment:

- `flake8` – style and linting
- `pytest` – unit testing
- `coverage` – code coverage analysis
- `bandit` – static security analysis for Python

### Jenkinsfile_Kareem_Hassani

I extended the Jenkins pipeline (Declarative Pipeline) to add new stages and a basic deployment step, keeping the original stages required in Part 2.

The pipeline now contains these stages:

1. **Setup**
   - Creates a Python virtual environment (`venv`) if it does not already exist.
   - Activates the virtual environment.
   - Installs all dependencies listed in `requirements.txt` (including `flake8`, `pytest`, `coverage`, `bandit`).

2. **Lint**
   - Activates the virtual environment.
   - Runs `flake8 app.py` to check code style and basic static issues.

3. **Test**
   - Activates the virtual environment.
   - Runs `pytest` to execute the unit tests for the project.

4. **Coverage**  *(new stage)*
   - Activates the virtual environment.
   - Runs the test suite with coverage enabled using:
     - `coverage run -m pytest`
     - `coverage report -m` to display a line coverage summary in the Jenkins console.
   - This satisfies the requirement to “add code coverage analysis using coverage.py and generate a report”.

5. **Security Scan**  *(new stage)*
   - Activates the virtual environment.
   - Runs `bandit -r .` to recursively scan the repository for common security issues in Python code.
   - This satisfies the requirement to “integrate a security scanning tool like bandit and add a Security Scan stage”.

6. **Deploy**
   - Implements a simple local deployment.
   - Creates a `deploy/` directory (if it does not exist).
   - Copies `app.py` and `requirements.txt` into `deploy/`.
   - Prints a message indicating the deploy location (e.g. `deploy` folder inside the workspace).
   - This fulfills the requirement of adding a basic deployment step (simple local deployment).

The `post { always { cleanWs() } }` block is kept so that the Jenkins workspace is cleaned up after each run.

---

## Jenkins Pipeline Output (Summary)

After committing and pushing the modified `requirements.txt` and `Jenkinsfile_Kareem_Hassani` to GitHub and triggering the Jenkins job:

- **Setup**  
  - Virtual environment is created (if missing) and all dependencies are installed successfully.

- **Lint**  
  - `flake8` completes without blocking errors.

- **Test**  
  - `pytest` runs and the tests pass.

- **Coverage**  
  - `coverage run -m pytest` executes the tests under coverage.
  - `coverage report -m` prints a coverage summary in the Jenkins console log.
  - (Here you can optionally note the final coverage percentage, e.g. `app.py` covered at ~100%.)

- **Security Scan**  
  - `bandit -r .` runs on the repository.
  - The scan completes without reporting any high-severity issues that break the build.

- **Deploy**  
  - The pipeline creates/updates the local `deploy/` directory.
  - `app.py` and `requirements.txt` are copied into `deploy/`.
  - A message confirming the deployment path is printed in the Jenkins log.

Overall, the Jenkins build now passes all stages: **Setup → Lint → Test → Coverage → Security Scan → Deploy**, matching the assignment’s expected output.
