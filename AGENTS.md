# Agent Guidelines for changedetection.io

## Build/Lint/Test Commands

### Testing
- **Run all tests**: `./changedetectionio/run_basic_tests.sh`
- **Run single test**: `pytest tests/test_filename.py` (e.g., `pytest tests/test_api.py`)
- **Run with specific options**: `REMOVE_REQUESTS_OLD_SCREENSHOTS=false pytest tests/test_filename.py`
- **Run with environment variables**: `BASE_URL="https://example.com" pytest tests/test_notification.py`

### Linting & Formatting
- **Lint and fix**: `ruff check --fix .`
- **Format code**: `ruff format .`
- **Pre-commit hooks**: `pre-commit run --all-files`

## Code Style Guidelines

### Python Version & Formatting
- **Target Python version**: 3.10+
- **Line length**: 100 characters
- **Indentation**: 4 spaces
- **Quote style**: Preserve existing (single/double quotes)

### Linting Rules (ruff)
Enabled rules:
- `B` - flake8-bugbear (bug detection)
- `C` - Complexity
- `E` - pycodestyle (style)
- `F` - Pyflakes (logical errors)
- `I` - isort (import sorting)
- `N` - pep8-naming (naming conventions)
- `UP` - pyupgrade (syntax upgrades)
- `W` - pycodestyle (warnings)

Ignored rules:
- `B007` - unused-loop-control-variable
- `B909` - loop-iterator-mutation
- `E203` - whitespace-before-punctuation
- `E266` - multiple-leading-hashes-for-block-comment
- `E501` - redundant-backslash
- `F403` - undefined-local-with-import-star
- `N802` - invalid-function-name
- `N806` - non-lowercase-variable-in-function
- `N815` - mixed-case-variable-in-class-scope

### Import Style
- Use `isort` for automatic import sorting
- Standard library imports first
- Third-party imports second
- Local imports last
- One import per line

### Naming Conventions
- **Functions**: snake_case
- **Variables**: snake_case
- **Classes**: PascalCase
- **Constants**: UPPER_CASE
- **Modules**: snake_case

### Error Handling
- Use try/except blocks appropriately
- Log errors with `loguru` logger
- Provide meaningful error messages
- Handle exceptions gracefully

### Logging
- Use `loguru` for logging
- Available levels: TRACE, DEBUG, INFO, SUCCESS, WARNING, ERROR, CRITICAL
- Use appropriate log levels for different scenarios

### Flask-Specific Guidelines
- Follow Flask best practices
- Use blueprints for organization
- Implement proper error handling for routes
- Use Flask-WTF for forms with CSRF protection

### Testing
- Write tests for all new functionality
- Use pytest framework
- Follow existing test patterns in the `tests/` directory
- Test both positive and negative scenarios

### General Best Practices
- Follow PEP 8 style guidelines
- Write clear, readable code
- Add docstrings for complex functions
- Keep functions focused and single-purpose
- Use type hints where appropriate</content>
</xai:function_call /></xai:function_call name="bash">
<parameter name="command">cd /home/grota/Projects/personal/sabbatical/changedetection.io && ruff check --fix . && ruff format .