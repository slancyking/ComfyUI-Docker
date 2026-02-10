# AGENTS.md

Guidance for agentic coding agents working in this repository. Contains build/lint/test commands and code style guidelines.

## Build Commands

### Docker Build System
Uses Docker Buildx for multi-architecture builds with multiple variants and CUDA versions.

**Build specific variants:**
```bash
docker buildx bake base-12-6      # Base variant with CUDA 12.6
docker buildx bake slim-12-8      # Slim variant without custom nodes
docker buildx bake production-12-6  # Production optimized
docker buildx bake ultra-slim-12-8  # Minimal footprint
docker buildx bake --push         # Build and push all variants
```

**Image variants:**
- **base**: Full installation with custom nodes (~8-12GB)
- **slim**: No custom nodes, includes dev tools (~6-8GB)
- **minimal**: No custom nodes, no code-server (~4-6GB)
- **production**: Optimized for serving, no dev tools (~4-5GB)
- **ultra-slim**: ComfyUI only, minimal dependencies (~2-3GB)

**CUDA versions:** 12.4, 12.5, 12.6, 12.8, 12.9, 13.0 (note: base-12-8 requires manual build due to size constraints).  
**Optimized builds:** Use `docker-bake-optimized.hcl` for production‑optimized variants.

**Manual build examples:**
```bash
docker buildx bake production-12-8
docker buildx bake ultra-slim-12-6
```

**CI/CD:** Automated builds via `.github/workflows/build.yml`.

## Test Commands

**Run individual test suites:**
```bash
python scripts/test_preset_manager.py
python scripts/test_preset_system.py
python scripts/test_theme_system.py
python scripts/test_login_update.py
```

**Validation scripts:**
```bash
python scripts/preset_validator.py
python scripts/validate_custom_nodes.py
python scripts/test_preset_system.py
```

**Running a single test:** Tests are functions within each file; modify the file or run entire suite.

## Lint Commands

No formal linting configuration. Consider adding `black`, `isort`, `ruff`, `mypy`. Follow existing code style.

## Code Style Guidelines

### Imports
- Group: stdlib, third-party, local modules
- Use absolute imports for local modules
- Include type hints from `typing` (Dict, List, Optional, Tuple)

### Formatting
- Double quotes for strings (consistent in codebase)
- f-strings for interpolation
- Max line length ~100 chars
- 4-space indentation, no tabs
- Blank lines separate logical sections

### Type Annotations
- Type hints for function args and returns
- Use `Optional[T]` for nullable values
- Use `List[T]`, `Dict[K, V]`, `Tuple[T, ...]`

### Naming Conventions
- **Classes**: `CamelCase` (e.g., `ModelManager`)
- **Functions/Methods**: `snake_case` (e.g., `parse_all_presets`)
- **Variables**: `snake_case` (e.g., `storage_info`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `DEFAULT_CATEGORIES`)
- **Private methods**: Prefix `_` (e.g., `_initialize_directories`)

### Error Handling
- Explicit `try/except` blocks
- Log errors with context using `print()` or logging
- Raise specific exceptions, not generic `Exception`
- Use assertions for internal checks in tests

### Documentation
- Docstrings for all public classes/functions (triple double quotes)
- Google-style docstring format
- Include type info when not obvious
- Comments sparingly; prefer self-documenting code

### File Organization
- Keep files under ~500 lines
- Separate concerns: config, business logic, web interface
- Use `__init__.py` for package structure
- Test files alongside code in `scripts/`

## Project-Specific Conventions

### Preset System
- Presets defined in `config/presets.yaml` (schema v1.0)
- Three model categories: Video, Image, Audio Generation
- Each preset: model file definitions, install scripts, README integration

### Docker Build Structure
- Multi-stage builds (`Dockerfile`, `Dockerfile.optimized`)
- Uses UV package manager
- Builder stage compiles PyTorch and dependencies
- Runtime stage production-optimized

### Environment Variables
- `PRESET_DOWNLOAD`: Video models
- `IMAGE_PRESET_DOWNLOAD`: Image models
- `AUDIO_PRESET_DOWNLOAD`: Audio models
- `ENABLE_PRESET_MANAGER`: Enable web UI (default true)
- `ACCESS_PASSWORD`: Web interface password
- `ENABLE_CODE_SERVER`: Enable VS Code server (default true)

## Cursor/Copilot Rules

No Cursor rules (`.cursor/rules/` or `.cursorrules`) or Copilot instructions (`.github/copilot-instructions.md`) found.

## Important Files

- `Dockerfile`: Multi-stage build definition
- `docker-bake.hcl`: Build matrix configuration
- `scripts/start.sh`: Container entrypoint
- `scripts/preset_manager/core.py`: Main preset management logic
- `config/presets.yaml`: Central preset configuration
- `.github/workflows/build.yml`: Automated CI/CD pipeline
- `CLAUDE.md`: Detailed project documentation

## Notes for Agents

- Check `CLAUDE.md` for up‑to‑date guidance
- Follow existing patterns in codebase
- Test changes with existing test scripts
- Consider Docker image size when adding dependencies
- Preserve backward compatibility in preset system

---

*Generated from codebase analysis. Update as conventions evolve.*