# AI Agent Testing Prompt: Comprehensive Test Coverage Implementation

## 🎯 OBJECTIVE
Achieve 90-95% overall test coverage with 90%+ coverage per file for a Python microservice codebase using systematic Test-Driven Development approach with comprehensive mocking and error handling.

## 📋 SUCCESS CRITERIA
- ✅ 90-95% overall test coverage
- ✅ 90%+ coverage for each service file
- ✅ 100% test pass rate (zero failures)
- ✅ All external dependencies properly mocked
- ✅ Comprehensive error handling coverage (90%+)
- ✅ All conditional branches tested (95%+)
- ✅ Complete exception path coverage (90%+)
- ✅ Clean, maintainable test code

---

## 🚀 STEP-BY-STEP EXECUTION GUIDE

### STEP 1: Initial Discovery & Analysis
**Execute these commands and analyze results:**

```bash
# 1.1 Check current test status
poetry run pytest --tb=short

# 1.2 Get current coverage baseline
poetry run pytest --cov=app --cov-report=term-missing

# 1.3 Identify codebase structure
find app -name "*.py" | grep -E "(service|function)" | head -10
```

**Analysis Tasks:**
- [ ] Document current coverage percentage
- [ ] Identify failing tests and their causes
- [ ] Map service files that need testing
- [ ] Note external dependencies (database, gRPC, APIs)
- [ ] Check existing test infrastructure

### STEP 2: Configure Testing Environment

**2.1 Set up coverage configuration in `pyproject.toml`:**
```toml
[tool.coverage.run]
source = ["app"]
omit = [
    "app/scripts/*",           # Exclude utility scripts
    "app/grpc/*_pb2.py",       # Exclude generated protobuf files
    "app/grpc/*_pb2_grpc.py",  # Exclude generated gRPC files
    "tests/*",                 # Exclude test files themselves
    "*/migrations/*",          # Exclude migration files
    "*/__pycache__/*",         # Exclude cache files
    "*.pyc",                   # Exclude compiled files
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
    "@(abc\\.)?abstractmethod",
]
```

**2.2 Configure pytest settings:**
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
addopts = "-v --cov=app --cov-report=term-missing"
asyncio_mode = "auto"
asyncio_default_fixture_loop_scope = "function"
norecursedirs = ["app/scripts", "app/grpc", ".git", ".tox", "dist", "build", "*.egg"]
```

**Validation:** Run `poetry run pytest --version` to confirm configuration

### STEP 2.5: Organize Test Directory Structure

**2.5.1 Analyze app/ directory structure:**
```bash
# Check existing app structure
find app -type d -name "*" | head -20
ls -la app/
```

**2.5.2 Create organized test directory structure:**
```bash
# Create test directories mirroring app/ structure (exclude omitted directories)
mkdir -p tests/api tests/core tests/db tests/models tests/services tests/utils
mkdir -p tests/features tests/integration tests/migrations
mkdir -p tests/utils/cache tests/utils/constants tests/utils/helpers tests/utils/kafka

# Add __init__.py files for proper Python package structure
touch tests/api/__init__.py tests/core/__init__.py tests/db/__init__.py
touch tests/models/__init__.py tests/services/__init__.py tests/utils/__init__.py
touch tests/features/__init__.py tests/integration/__init__.py tests/migrations/__init__.py
touch tests/utils/cache/__init__.py tests/utils/constants/__init__.py
touch tests/utils/helpers/__init__.py tests/utils/kafka/__init__.py
```

**2.5.3 Organize existing test files:**
```bash
# Move existing test files to appropriate directories based on functionality
# Example moves (adjust based on actual file content):
# mv tests/test_agent_service.py tests/services/test_agent_service.py
# mv tests/test_agent_model.py tests/models/test_agent.py
# mv tests/test_kafka_client.py tests/utils/kafka/test_kafka_client.py
```

**Directory Organization Guidelines:**
- **tests/services/**: For app/services/ functionality
- **tests/models/**: For app/models/ functionality
- **tests/utils/**: For app/utils/ functionality (with subdirectories)
- **tests/core/**: For app/core/ functionality
- **tests/db/**: For app/db/ functionality
- **tests/api/**: For app/api/ functionality
- **tests/features/**: For feature-specific tests
- **tests/integration/**: For integration tests
- **tests/migrations/**: For migration tests (if needed)

**Validation:** Run `poetry run pytest --collect-only` to verify test discovery

### STEP 3: Fix Existing Failing Tests

**3.1 Identify and categorize failures:**
```bash
poetry run pytest --tb=short | grep FAILED
```

**3.2 Fix each failing test systematically:**
- [ ] Import errors → Fix import paths
- [ ] Mock setup issues → Implement proper mocking
- [ ] Assertion errors → Correct expected values
- [ ] Database errors → Add proper database mocking

**3.3 Validation checkpoint:**
```bash
poetry run pytest --tb=no -q
# Target: All tests passing before proceeding
```

### STEP 4: Implement Comprehensive Test Coverage

**4.1 Create standard fixtures (create/update conftest.py):**
```python
@pytest.fixture
def mock_context():
    context = Mock()
    context.set_code = Mock()
    context.set_details = Mock()
    return context

@pytest.fixture  
def sample_model_instance():
    return ModelClass(
        id="test-id",
        name="Test Name",
        # All required fields
    )
```

**4.2 Implement systematic testing for each service:**

**For each service file in `app/services/`:**

```python
class TestServiceNameService:
    # 4.2.1 Happy path tests
    def test_method_success(self, service, mock_context):
        """Test successful execution."""
        with patch.object(service, 'get_db') as mock_get_db:
            mock_db = Mock()
            mock_get_db.return_value = mock_db
            # Configure success scenario
            response = service.method(request, mock_context)
            assert response.success is True

    # 4.2.2 Error handling tests
    def test_method_database_error(self, service, mock_context):
        """Test database exception handling."""
        with patch.object(service, 'get_db') as mock_get_db:
            mock_db = Mock()
            mock_db.commit.side_effect = Exception("DB Error")
            mock_get_db.return_value = mock_db
            
            response = service.method(request, mock_context)
            mock_context.set_code.assert_called_with(grpc.StatusCode.INTERNAL)

    # 4.2.3 Edge case tests
    def test_method_empty_input(self, service, mock_context):
        """Test with empty/null inputs."""
        # Test edge cases and validation

    # 4.2.4 Permission tests  
    def test_method_unauthorized(self, service, mock_context):
        """Test authorization failures."""
        # Test permission scenarios
```

**4.3 Aggressive coverage-driven test addition:**
```bash
# After each test batch, check coverage improvement
poetry run pytest tests/services/test_specific_service.py --cov=app/services/specific_service.py --cov-report=term-missing

# Target: Each file must reach 90%+ coverage
# Strategy:
# 1. Identify every uncovered line
# 2. Create targeted tests for each uncovered branch
# 3. Test all conditional statements (if/else/elif)
# 4. Cover all exception handling paths
# 5. Test all input validation scenarios
# 6. Include boundary condition testing
```

### STEP 5: Achieving 90%+ Coverage - Advanced Testing Patterns

**5.0 Comprehensive Coverage Strategy:**
```bash
# Analyze each file individually for 90%+ target
poetry run pytest tests/services/test_agent_functions.py --cov=app/services/agent_functions.py --cov-report=term-missing

# Identify every uncovered line and create targeted tests:
# 1. Conditional branches (if/else/elif statements)
# 2. Exception handling (try/except blocks)
# 3. Input validation scenarios
# 4. Boundary conditions
# 5. Error recovery paths
# 6. Edge cases and null handling
```

### STEP 5.1: Advanced Testing Patterns

**5.1 Complex mock setups for multiple database queries:**
```python
def test_complex_database_operations(self, service, mock_context):
    def mock_query_side_effect(model_class):
        mock_query = Mock()
        if model_class.__name__ == 'ModelA':
            mock_query.filter.return_value.first.return_value = mock_data_a
        elif model_class.__name__ == 'ModelB':
            mock_query.filter.return_value.all.return_value = mock_data_b_list
        return mock_query
    
    mock_db.query.side_effect = mock_query_side_effect
```

**5.2 Comprehensive conditional branch testing:**
```python
def test_all_conditional_branches(self, service, mock_context):
    # Test every if/else/elif branch

    # Test condition A = True
    request.condition_a = True
    response = service.method(request, mock_context)
    assert response.result == "condition_a_result"

    # Test condition A = False, condition B = True
    request.condition_a = False
    request.condition_b = True
    response = service.method(request, mock_context)
    assert response.result == "condition_b_result"

    # Test all conditions False (default path)
    request.condition_a = False
    request.condition_b = False
    response = service.method(request, mock_context)
    assert response.result == "default_result"
```

**5.3 Complete exception path coverage:**
```python
def test_all_exception_scenarios(self, service, mock_context):
    # Test each specific exception type

    # Database connection error
    with patch.object(service, 'get_db') as mock_get_db:
        mock_get_db.side_effect = ConnectionError("DB connection failed")
        response = service.method(request, mock_context)
        mock_context.set_code.assert_called_with(grpc.StatusCode.UNAVAILABLE)

    # Validation error
    with patch('app.services.service_name.validate_input') as mock_validate:
        mock_validate.side_effect = ValueError("Invalid input")
        response = service.method(request, mock_context)
        mock_context.set_code.assert_called_with(grpc.StatusCode.INVALID_ARGUMENT)

    # Generic exception
    with patch.object(service, 'process_data') as mock_process:
        mock_process.side_effect = Exception("Unexpected error")
        response = service.method(request, mock_context)
        mock_context.set_code.assert_called_with(grpc.StatusCode.INTERNAL)
```

**5.4 Boundary condition and input validation testing:**
```python
def test_boundary_conditions(self, service, mock_context):
    # Test empty inputs
    request.name = ""
    response = service.method(request, mock_context)
    assert response.success is False

    # Test null/None inputs
    request.name = None
    response = service.method(request, mock_context)
    assert response.success is False

    # Test maximum length inputs
    request.name = "x" * 1000
    response = service.method(request, mock_context)
    # Test appropriate handling

    # Test special characters
    request.name = "test@#$%^&*()"
    response = service.method(request, mock_context)
    # Test sanitization/validation
```

**5.5 Test external service integrations:**
```python
@patch('app.services.service_name.external_api_call')
def test_external_service_integration(self, mock_api, service):
    mock_api.return_value = expected_response
    # Test integration logic
```

### STEP 6: Quality Assurance & Validation

**6.1 Run comprehensive test suite:**
```bash
poetry run pytest --cov=app --cov-report=term-missing --cov-report=html
```

**6.2 Quality checkpoints:**
- [ ] Coverage ≥ 90-95% overall
- [ ] Each service file ≥ 90% coverage
- [ ] All tests passing (0 failures)
- [ ] No real database interactions in tests
- [ ] All external dependencies mocked
- [ ] Error scenarios comprehensively covered (90%+)
- [ ] All conditional branches tested (95%+)
- [ ] Complete exception path coverage (90%+)
- [ ] Boundary conditions tested
- [ ] Input validation scenarios covered

**6.3 Generate coverage report:**
```bash
poetry run pytest --cov=app --cov-report=html
open htmlcov/index.html  # Review detailed coverage
```

---

## 🔧 TROUBLESHOOTING GUIDE

### Common Issues & Solutions:

**Import Errors:**
```python
# Fix: Use proper import paths
from app.services.service_name import ServiceClass
```

**Mock Setup Issues:**
```python
# Fix: Ensure proper mock configuration
with patch.object(service, 'get_db') as mock_get_db:
    mock_db = Mock()
    mock_get_db.return_value = mock_db
```

**Assertion Failures:**
```python
# Fix: Use proper assertion patterns
assert response.success is True  # Not == True
mock_db.commit.assert_called()   # Verify mock calls
```

**Database Test Issues:**
```python
# Fix: Never touch real database
# ALWAYS use mocked database sessions
# ONLY delete mock data, never real data
```

---

## 📊 PROGRESS TRACKING

### Coverage Milestones:
- [ ] **Baseline**: Document starting coverage %
- [ ] **30%**: Basic happy path tests implemented
- [ ] **50%**: Error handling tests added
- [ ] **70%**: Edge cases and validation covered
- [ ] **80%**: All conditional branches tested
- [ ] **85%**: Exception paths covered
- [ ] **90%**: Per-file targets achieved
- [ ] **90-95%**: Overall target achieved with comprehensive coverage

### Quality Gates:
- [ ] All existing tests fixed and passing
- [ ] New tests follow naming conventions
- [ ] All database operations mocked
- [ ] Error scenarios comprehensively tested
- [ ] Code quality maintained

---

## 🎯 FINAL VALIDATION

**Run this command to confirm success:**
```bash
poetry run pytest --cov=app --cov-report=term-missing | grep "TOTAL"
```

**Expected output:** `TOTAL ... 90%` or higher with 0 test failures.

**Success criteria met when:**
✅ Coverage ≥ 90-95% overall
✅ Each service file ≥ 90% coverage
✅ All tests passing
✅ Clean, maintainable test code
✅ Comprehensive error handling (90%+)
✅ All conditional branches tested (95%+)
✅ Complete exception path coverage (90%+)
✅ No real external dependencies in tests

---

## 🤖 COPY-PASTE PROMPT FOR AI AGENTS

**Use this exact prompt to start the testing process:**

```
I need you to implement comprehensive test coverage for this Python microservice codebase following Test-Driven Development principles.

OBJECTIVE: Achieve 90-95% overall test coverage with 90%+ coverage per file and 100% test pass rate.

APPROACH:
1. First, analyze the current codebase and test status
2. Fix any existing failing tests
3. Configure proper coverage exclusions
4. Systematically add tests for each service using mocking
5. Focus on: happy paths, error handling, edge cases, validation
6. Never touch real databases - always use mocks
7. Follow the step-by-step guide in AI_AGENT_TESTING_PROMPT.md

START with: `poetry run pytest --cov=app --cov-report=term-missing` to get baseline coverage.

VALIDATION: Each step should improve coverage while maintaining 100% test pass rate.

PRIORITY: Core business logic first, then error handling, then edge cases.

Follow the systematic approach and provide coverage reports after each major milestone.
```

This prompt ensures any AI agent can replicate the exact methodology we used to achieve 60% coverage!
