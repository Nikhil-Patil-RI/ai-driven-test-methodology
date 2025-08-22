# Comprehensive Testing Methodology for AI Agents

## Overview
This document outlines the systematic approach for achieving comprehensive test coverage (90-95% overall, 90%+ per file) in Python microservices, specifically for gRPC-based services with database interactions.

## Phase 0: Initial Discovery & Analysis

### 0.1 Codebase Analysis
1. **Map service architecture** - Identify all services, models, and dependencies
2. **Analyze existing tests** - Check current test coverage and quality
3. **Identify external dependencies** - Database, gRPC, APIs, file systems
4. **Review business logic complexity** - Prioritize high-value testing areas
5. **Check for existing test infrastructure** - Fixtures, mocks, utilities

### 0.2 Test Strategy Planning
1. **Define coverage targets** - Set ambitious coverage goals (90%+ per file, 90-95% overall)
2. **Plan test architecture** - Organize test files and structure
3. **Identify test types needed** - Unit, integration, error handling
4. **Map testing priorities** - Core business logic first, edge cases second
5. **Plan mock strategy** - Identify what needs mocking vs real implementations

## Phase 1: Pre-Testing Setup & Configuration

### 1.1 Coverage Configuration
```toml
# pyproject.toml
[tool.coverage.run]
source = ["app"]
omit = [
    "app/scripts/*",           # Exclude utility scripts
    "app/grpc/*_pb2.py",       # Exclude generated protobuf files
    "app/grpc/*_pb2_grpc.py",  # Exclude generated gRPC files
    "tests/*",                 # Exclude test files themselves
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

### 1.2 Pytest Configuration
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
addopts = "-v --cov=app --cov-report=term-missing"
asyncio_mode = "auto"
asyncio_default_fixture_loop_scope = "function"
norecursedirs = ["app/scripts", "app/grpc", ".git", ".tox", "dist", "build", "*.egg"]
```

## Phase 2: Test Development Methodology

### 2.1 Test-Driven Development (TDD) Approach
1. **Analyze existing functionality** - Understand the codebase structure
2. **Write failing tests first** - Based on expected behavior
3. **Run tests to confirm failures** - Ensure tests are valid
4. **Fix/implement code** - Make tests pass
5. **Refactor and improve** - Maintain code quality
6. **Repeat cycle** - Continue until coverage targets met

### 2.2 Mock Strategy Framework
```python
# Standard mocking pattern for database operations
@patch.object(service, 'get_db')
def test_function(self, mock_get_db, service, mock_context):
    mock_db = Mock()
    mock_get_db.return_value = mock_db
    
    # Configure mock behavior
    mock_db.query.return_value.filter.return_value.first.return_value = mock_data
    mock_db.add = Mock()
    mock_db.commit = Mock()
    
    # Execute test
    response = service.method(request, mock_context)
    
    # Verify behavior
    mock_db.add.assert_called()
    mock_db.commit.assert_called()
    assert response.success is True
```

### 2.3 Test Organization & Directory Structure

**2.3.1 Mirror App Directory Structure**
Organize tests to mirror the app/ directory structure for better maintainability:

```
tests/
├── api/                    # API endpoint tests (if app/api/ exists)
├── app/                    # Main application tests (for app/main.py)
├── core/                   # Core functionality tests (if app/core/ exists)
├── db/                     # Database tests (if app/db/ exists)
├── features/               # Feature-specific tests
├── helpers/                # Helper function tests (if app/helpers/ exists)
├── integration/            # Integration tests
├── migrations/             # Migration tests (if needed)
├── models/                 # Model tests (if app/models/ exists)
├── services/               # Service layer tests (if app/services/ exists)
└── utils/                  # Utility tests (if app/utils/ exists)
    ├── cache/              # Cache utility tests
    ├── constants/          # Constants tests
    ├── helpers/            # Helper utility tests
    └── kafka/              # Kafka utility tests
```

**2.3.2 Naming Conventions**
```python
# File structure: tests/{app_directory}/test_{module_name}.py
# Class structure: Test{ModuleName}Service or Test{ModuleName}
# Method naming: test_{method_name}_{scenario}

# Example for app/services/agent_functions.py:
# tests/services/test_agent_functions.py
class TestAgentFunctionsService:
    def test_create_agent_success(self):           # Happy path
    def test_create_agent_database_error(self):   # Error handling
    def test_create_agent_invalid_input(self):    # Validation
    def test_create_agent_empty_fields(self):     # Edge cases
    def test_create_agent_unauthorized(self):     # Permission tests

# Example for app/models/agent.py:
# tests/models/test_agent.py
class TestAgentModel:
    def test_agent_creation(self):
    def test_agent_validation(self):
    def test_agent_relationships(self):

# Example for app/utils/kafka/kafka_service.py:
# tests/utils/kafka/test_kafka_service.py
class TestKafkaService:
    def test_send_message_success(self):
    def test_send_message_connection_error(self):
```

**2.3.3 Directory Creation Guidelines**
- Only create test directories that correspond to existing app/ directories
- For projects with excluded directories (scripts, grpc, migrations), skip creating corresponding test directories
- Use `__init__.py` files in all test directories for proper Python package structure
- Group related functionality in feature-specific directories when appropriate

### 2.4 Fixture Design Patterns
```python
@pytest.fixture
def sample_model_instance():
    """Create reusable test data fixtures."""
    return ModelClass(
        id="test-id",
        name="Test Name",
        # Include all required fields
        # Use realistic test data
    )

@pytest.fixture
def mock_context():
    """Standard gRPC context mock."""
    context = Mock()
    context.set_code = Mock()
    context.set_details = Mock()
    return context
```

## Phase 3: Systematic Coverage Strategy

### 3.1 Coverage Analysis Process
1. **Run initial coverage** - `poetry run pytest --cov=app --cov-report=term-missing`
2. **Identify uncovered lines** - Focus on missing line ranges
3. **Categorize missing coverage**:
   - Core business logic (Priority 1) - Target: 95%+
   - Error handling (Priority 2) - Target: 90%+
   - Edge cases (Priority 3) - Target: 90%+
   - Defensive code (Priority 4) - Target: 85%+
4. **File-by-file analysis** - Each service file must achieve 90%+ coverage
5. **Line-by-line coverage review** - Identify every uncovered line and justify or test

### 3.2 Test Categorization
```python
class TestServiceName:
    # 1. Happy path tests
    def test_method_success(self):
        """Test successful execution path."""
        
    # 2. Error handling tests  
    def test_method_database_error(self):
        """Test database exception handling."""
        
    # 3. Edge case tests
    def test_method_empty_input(self):
        """Test with empty/null inputs."""
        
    # 4. Validation tests
    def test_method_invalid_input(self):
        """Test input validation."""
        
    # 5. Permission tests
    def test_method_unauthorized(self):
        """Test authorization failures."""
```

### 3.3 Iterative Improvement Process
1. **Run coverage after each test batch**
2. **Identify newly covered lines**
3. **Focus on remaining high-value areas**
4. **Fix failing tests before adding new ones**
5. **Maintain test quality over quantity**
6. **Aggressive coverage targeting**:
   - Start with files <50% coverage
   - Bring each file to 70%+ before moving to next
   - Then push all files to 90%+
   - Finally achieve 90-95% overall coverage
7. **Comprehensive line coverage**:
   - Test every conditional branch
   - Cover all exception paths
   - Test all input validation scenarios
   - Include boundary condition testing

## Phase 4: Testing Patterns & Best Practices

### 4.1 Database Testing Pattern
```python
def test_database_operation(self, service, mock_context):
    with patch.object(service, 'get_db') as mock_get_db:
        mock_db = Mock()
        mock_get_db.return_value = mock_db
        
        # NEVER touch real database
        # ALWAYS use mocked sessions
        # ONLY delete mock data if required
        
        response = service.method(request, mock_context)
        
        # Verify database interactions
        mock_db.add.assert_called()
        mock_db.commit.assert_called()
```

### 4.2 Error Handling Testing
```python
def test_exception_handling(self, service, mock_context):
    with patch.object(service, 'get_db') as mock_get_db:
        mock_db = Mock()
        mock_get_db.return_value = mock_db
        
        # Simulate database exception
        mock_db.commit.side_effect = Exception("Database error")
        
        response = service.method(request, mock_context)
        
        # Verify graceful error handling
        mock_context.set_code.assert_called_with(grpc.StatusCode.INTERNAL)
```

### 4.3 Complex Mock Setup Pattern
```python
def test_complex_query_chains(self, service, mock_context):
    # Handle multiple database queries
    def mock_query_side_effect(model_class):
        mock_query = Mock()
        if model_class.__name__ == 'ModelA':
            mock_query.filter.return_value.first.return_value = mock_data_a
        elif model_class.__name__ == 'ModelB':
            mock_query.filter.return_value.all.return_value = mock_data_b_list
        return mock_query
    
    mock_db.query.side_effect = mock_query_side_effect
```

## Phase 5: Quality Assurance & Maintenance

### 5.1 Test Quality Metrics
- **Overall Coverage Target**: 90-95%
- **Per-File Coverage Target**: 90%+ for all service files
- **Core Business Logic**: 95%+ coverage
- **Test Pass Rate**: 100% (no failing tests)
- **Mock Usage**: All external dependencies mocked
- **Test Independence**: Each test runs in isolation
- **Line Coverage Analysis**: Every uncovered line documented and justified

### 5.2 Continuous Improvement
1. **Regular coverage audits** - Weekly coverage reports
2. **Test maintenance** - Update tests when code changes
3. **Performance monitoring** - Keep test execution time reasonable
4. **Documentation updates** - Maintain test documentation

### 5.3 Coverage Commands
```bash
# Basic coverage run
poetry run pytest

# Detailed coverage report
poetry run pytest --cov=app --cov-report=term-missing

# HTML coverage report
poetry run pytest --cov=app --cov-report=html

# Focus on specific service
poetry run pytest tests/services/test_specific_service.py --cov=app/services/specific_service.py
```

## Success Metrics
- **✅ 90-95% overall coverage achieved**
- **✅ 90%+ coverage for each service file**
- **✅ All tests passing (0 failures)**
- **✅ Core business logic 95%+ covered**
- **✅ Comprehensive error handling scenarios tested**
- **✅ All conditional branches covered**
- **✅ All exception paths tested**
- **✅ Clean, maintainable test code**

## Key Principles
1. **Test-Driven Development** - Write tests first
2. **Mock External Dependencies** - Never touch real databases/APIs
3. **Comprehensive Error Testing** - Cover exception scenarios
4. **Iterative Improvement** - Continuous coverage enhancement
5. **Quality Over Quantity** - Focus on meaningful tests
6. **Systematic Approach** - Follow structured methodology

## Implementation Checklist for AI Agents

### ✅ Phase 1: Setup
- [ ] Configure coverage exclusions in pyproject.toml (exclude scripts, grpc, migrations if present)
- [ ] Set up pytest configuration with proper flags
- [ ] Verify test discovery paths
- [ ] Configure async testing support

### ✅ Phase 1.5: Test Directory Organization
- [ ] Analyze app/ directory structure
- [ ] Create tests/ directory structure mirroring app/ (excluding excluded directories)
- [ ] Create appropriate subdirectories: api/, core/, db/, models/, services/, utils/, etc.
- [ ] Add `__init__.py` files to all test directories
- [ ] Create feature-specific directories: features/, integration/, migrations/ (if needed)
- [ ] Organize existing test files into appropriate directories

### ✅ Phase 2: Test Foundation
- [ ] Create comprehensive fixtures for all models
- [ ] Implement standard mocking patterns
- [ ] Set up gRPC context mocks
- [ ] Create reusable test utilities

### ✅ Phase 3: Core Testing
- [ ] Test all happy path scenarios
- [ ] Implement error handling tests
- [ ] Cover edge cases and validation
- [ ] Test database operations with mocks

### ✅ Phase 4: Coverage Optimization
- [ ] Run coverage analysis after each test batch
- [ ] Fix failing tests before adding new ones
- [ ] Focus on high-value uncovered lines
- [ ] Achieve 90%+ coverage per file
- [ ] Target 90-95% overall coverage
- [ ] Test every conditional branch
- [ ] Cover all exception handling paths
- [ ] Include boundary condition testing

### ✅ Phase 5: Quality Assurance
- [ ] Ensure 100% test pass rate
- [ ] Verify no real database interactions
- [ ] Validate proper mock usage
- [ ] Document testing approach

## Expected Outcomes
- **90-95% overall test coverage** across entire codebase
- **90%+ coverage per service file** without exceptions
- **Zero failing tests** in final suite
- **Comprehensive error handling** coverage (90%+)
- **All conditional branches tested** (95%+)
- **Complete exception path coverage** (90%+)
- **Clean, maintainable** test codebase
- **Systematic testing approach** for future development
