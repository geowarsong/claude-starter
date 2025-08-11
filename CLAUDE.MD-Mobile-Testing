# Claude Code - Universal iOS Testing Framework
## Advanced Test Generation with Thinking & Parallel Execution

### 🧠 **IMPORTANT: Leverage Thinking Capabilities**
**THINK** deeply about the codebase architecture before generating any tests. After analyzing each module, reflect on:
- Architecture patterns detected
- Dependencies and their relationships  
- Potential memory leaks and performance issues
- Security vulnerabilities
- Test strategy optimization

**ALWAYS** use interleaved thinking after tool results to determine the optimal next steps.

---

## 🚀 **CRITICAL: Parallel Tool Execution Strategy**

### **IMPORTANT: Optimize Parallel Tool Calling**
When analyzing a codebase for test generation, **ALWAYS** invoke multiple analysis tools **simultaneously**:

```markdown
**THINK** before execution: What can be analyzed in parallel?

PARALLEL EXECUTION GROUPS:
1. Architecture Analysis Group (execute simultaneously):
   - Analyze ViewControllers
   - Analyze ViewModels/Presenters
   - Analyze Models/Entities
   - Analyze Services/Repositories
   - Analyze Coordinators/Routers

2. Dependency Analysis Group (execute simultaneously):
   - Map protocol dependencies
   - Identify mock requirements
   - Analyze injection patterns
   - Detect circular dependencies

3. Test Generation Group (execute simultaneously):
   - Generate unit tests
   - Generate integration tests
   - Generate UI tests
   - Generate performance tests
   - Generate snapshot tests
```

**NEVER** analyze components sequentially when they can be processed in parallel.

---

## 🎯 **Core Testing Principles**

### **ALWAYS Follow These Absolute Rules**

1. **ALWAYS** achieve 100% coverage for:
   - Financial calculations
   - Security operations
   - Authentication flows
   - Data persistence operations
   - Error handling paths

2. **NEVER** generate tests without:
   - Analyzing existing test patterns first
   - Understanding the dependency graph
   - Checking for existing mocks
   - Validating architectural consistency

3. **ALWAYS** clean up temporary files:
   ```bash
   # After test generation task completion:
   rm -f temp_*.swift
   rm -f analysis_*.txt
   rm -f coverage_report_*.tmp
   ```

### **YES/NO Decision Framework**

**THINK** and answer these questions before test generation:

| Question | YES Action | NO Action |
|----------|------------|-----------|
| Is this a financial/banking app? | **100% coverage for money operations** | Standard 80% coverage |
| Does it handle user authentication? | **Generate security tests** | Skip security suite |
| Are there async operations? | **Use XCTestExpectation** | Synchronous tests only |
| Is SwiftUI used? | **Generate snapshot tests** | UIKit-based tests |
| Are there network calls? | **Mock all endpoints** | Skip network mocks |
| Is Core Data/Realm used? | **In-memory database tests** | Skip persistence tests |

---

## 🔍 **Architecture Detection & Analysis**

### **IMPORTANT: Architecture Pattern Recognition**

**THINK** deeply about the architecture before generating tests:

```swift
// **ALWAYS** detect and adapt to these patterns:

enum ArchitecturePattern {
    case MVVM      // → Test ViewModels with mocked dependencies
    case MVP       // → Test Presenters with view protocol mocks
    case VIPER     // → Test each component in isolation
    case MVC       // → Focus on controller logic testing
    case Clean     // → Test each layer independently
    case TCA       // → Test reducers, effects, and state
}

// **THINK**: What pattern is this project using?
// Analyze file naming, folder structure, and dependencies
```

### **Parallel Architecture Analysis Commands**

```markdown
**EXECUTE SIMULTANEOUSLY:**

1. "Analyze all ViewControllers and their lifecycle methods"
2. "Identify all protocols and their implementations"
3. "Map all dependency injections and singletons"
4. "Detect all async/await and Combine usage"
5. "Find all network endpoints and API calls"
```

---

## 🧪 **Test Generation Strategy**

### **IMPORTANT: Multi-Layer Test Coverage**

**ALWAYS** generate tests in this priority order:

```swift
// **CRITICAL PRIORITY (Must be 100%)**
Priority.CRITICAL = [
    "Authentication",
    "Authorization", 
    "Payment Processing",
    "Data Encryption",
    "User Data Handling"
]

// **HIGH PRIORITY (Must be 90%)**
Priority.HIGH = [
    "Business Logic",
    "Data Validation",
    "Error Handling",
    "State Management"
]

// **MEDIUM PRIORITY (Should be 70%)**
Priority.MEDIUM = [
    "UI Logic",
    "Navigation",
    "Formatting",
    "Utilities"
]
```

### **THINK: Intelligent Test Generation**

```swift
// **BEFORE** generating any test, **THINK**:

func shouldGenerateTest(for component: Component) -> Bool {
    // **THINK**: Is this component:
    // 1. Public API? → YES: Generate comprehensive tests
    // 2. Has side effects? → YES: Test all paths
    // 3. Has complex logic? → YES: Edge case testing
    // 4. Handles money? → YES: 100% coverage required
    // 5. Security related? → YES: Penetration testing
    
    return meetsTestingCriteria(component)
}
```

---

## 💉 **Dependency & Mock Management**

### **IMPORTANT: Parallel Mock Generation**

```swift
// **ALWAYS** generate mocks in parallel for all protocols:

protocol Repository { }
protocol Service { }
protocol Coordinator { }

// **EXECUTE SIMULTANEOUSLY**:
// 1. Generate MockRepository
// 2. Generate MockService  
// 3. Generate MockCoordinator

// **NEVER** generate mocks sequentially
```

### **Mock Generation Rules**

**ALWAYS** include in every mock:
- Invocation counting
- Parameter capture
- Stubbed return values
- Async support
- Throwing support

**NEVER** create mocks without:
- Thread safety
- Reset functionality
- Verification helpers

---

## 🎨 **Memory Leak & Performance Testing**

### **IMPORTANT: Detect Potential Issues**

**THINK** about these patterns and **ALWAYS** test for them:

```swift
// **CRITICAL: Memory Leak Detection**

// **ALWAYS** check for these patterns:
class ViewController {
    var closure: (() -> Void)?
    
    func setup() {
        // **THINK**: Is self captured strongly?
        closure = {
            self.doSomething() // ⚠️ LEAK - Generate test
        }
    }
}

// **ALWAYS** generate this test:
func test_closure_shouldNotCreateRetainCycle() {
    weak var sut: ViewController?
    autoreleasepool {
        let vc = ViewController()
        sut = vc
        vc.setup()
    }
    XCTAssertNil(sut, "Memory leak detected")
}
```

### **Performance Test Generation**

**ALWAYS** generate performance tests for:
- List scrolling with 1000+ items
- Image loading and processing
- Database queries
- Network request handling
- Complex calculations

---

## 🔐 **Security Testing Requirements**

### **CRITICAL: Security Test Coverage**

**NEVER** skip security tests for:

```swift
// **MANDATORY Security Tests**

func test_sql_injection_prevention() { }
func test_xss_attack_prevention() { }
func test_certificate_pinning() { }
func test_jailbreak_detection() { }
func test_keychain_security() { }
func test_biometric_authentication() { }
func test_session_management() { }
func test_data_encryption() { }
```

**THINK**: Are there any custom security implementations that need testing?

---

## 🤖 **UI Testing Strategy**

### **IMPORTANT: Page Object Pattern**

**ALWAYS** generate page objects before UI tests:

```swift
// **THINK**: What UI elements need testing?

class LoginPage {
    // **ALWAYS** include:
    // - Element locators
    // - Action methods
    // - Verification methods
    // - Wait helpers
}

// **Generate in parallel**:
// 1. Page objects for all screens
// 2. Test scenarios for critical paths
// 3. Accessibility tests
// 4. Localization tests
```

### **UI Test Parallel Execution**

```markdown
**EXECUTE SIMULTANEOUSLY on multiple devices:**
- iPhone 15 Pro
- iPhone SE
- iPad Pro
- Different iOS versions
```

---

## 📊 **Coverage Analysis & Reporting**

### **IMPORTANT: Continuous Coverage Monitoring**

**ALWAYS** after generating tests:

```bash
# **EXECUTE in parallel:**
1. Run unit tests with coverage
2. Run integration tests with coverage
3. Run UI tests with coverage
4. Generate combined coverage report

# **THINK**: Which areas have < 70% coverage?
# Generate additional tests for gaps
```

### **Coverage Requirements by Component Type**

| Component | **MINIMUM** | **OPTIMAL** | **Action if Below** |
|-----------|-------------|--------------|---------------------|
| Business Logic | 90% | 100% | **CRITICAL: Generate more tests** |
| ViewModels | 85% | 95% | **HIGH: Add state tests** |
| Repositories | 80% | 90% | **MEDIUM: Mock dependencies** |
| UI Components | 60% | 80% | **LOW: Snapshot tests** |
| Utilities | 90% | 100% | **HIGH: Edge cases** |

---

## 🚀 **Test Execution Optimization**

### **IMPORTANT: Parallel Test Execution**

**ALWAYS** configure for maximum parallelization:

```swift
// **Test Plan Configuration**
{
    "parallel": true,
    "maximumParallelTestRunners": 8,
    "testExecutionOrdering": "random",
    "testRepetitionMode": {
        "mode": "retryOnFailure",
        "maximumRetries": 2
    }
}
```

**NEVER** run tests sequentially when parallel execution is possible.

---

## 🧹 **File Management & Cleanup**

### **CRITICAL: Clean Up Temporary Files**

**ALWAYS** at task completion:

```bash
# **IMPORTANT**: Remove all temporary files created during testing

# Files to clean up:
rm -f temp_test_*.swift
rm -f mock_gen_*.swift
rm -f coverage_*.tmp
rm -f analysis_report_*.txt
rm -rf DerivedData/
rm -rf .build/

# **THINK**: Are there any other temporary files created?
```

**NEVER** leave temporary files in the project after test generation.

---

## 🎯 **Advanced Testing Patterns**

### **THINK: Complex Scenario Testing**

```swift
// **ALWAYS** consider these advanced patterns:

// 1. **Property-Based Testing**
func test_property_based<T>(_ property: (T) -> Bool) {
    // Generate random inputs
    // Verify property holds for all
}

// 2. **Mutation Testing**
func test_mutation_resistance() {
    // Modify implementation slightly
    // Tests should catch the change
}

// 3. **Fuzz Testing**
func test_fuzz_inputs() {
    // Random, malformed inputs
    // App shouldn't crash
}

// 4. **Contract Testing**
func test_api_contract() {
    // Verify API matches specification
}
```

---

## 📋 **Test Quality Checklist**

### **IMPORTANT: Before Completing Test Generation**

**THINK** and verify:

- [ ] **YES/NO**: Are all public APIs tested?
- [ ] **YES/NO**: Are all error paths covered?
- [ ] **YES/NO**: Are edge cases handled?
- [ ] **YES/NO**: Is async code properly tested?
- [ ] **YES/NO**: Are mocks thread-safe?
- [ ] **YES/NO**: Do tests run in isolation?
- [ ] **YES/NO**: Are tests deterministic?
- [ ] **YES/NO**: Is sensitive data excluded?
- [ ] **YES/NO**: Are temporary files cleaned?
- [ ] **YES/NO**: Is coverage ≥ 80%?

---

## 🔄 **Iterative Improvement Process**

### **IMPORTANT: Continuous Refinement**

**THINK** after initial test generation:

```markdown
1. **Analyze** coverage reports
2. **Identify** gaps in testing
3. **Generate** additional tests for gaps
4. **Verify** tests are maintainable
5. **Refactor** redundant test code
6. **Document** complex test scenarios
7. **Clean up** all temporary files
```

**ALWAYS** iterate until coverage targets are met.

---

## 💡 **Intelligent Test Optimization**

### **THINK: Test Efficiency**

```swift
// **Before generating each test, THINK:**

struct TestOptimization {
    // Can this test be combined with another?
    let canCombine: Bool
    
    // Can setup be shared?
    let shareableSetup: Bool
    
    // Can this run in parallel?
    let parallelizable: Bool
    
    // Is this testing implementation or behavior?
    let testsBehavior: Bool // **ALWAYS** should be true
}
```

---

## 🎖️ **Best Practices Summary**

### **ALWAYS Remember:**

1. **THINK** before generating - analyze architecture deeply
2. **PARALLELIZE** operations - never sequential when parallel is possible
3. **CLEAN UP** temporary files - leave no trace
4. **PRIORITIZE** critical paths - security and money first
5. **ITERATE** on coverage - continuous improvement
6. **MOCK** all dependencies - true isolation
7. **VERIFY** test quality - not just quantity

### **NEVER Forget:**

1. **NEVER** test implementation details
2. **NEVER** leave flaky tests
3. **NEVER** hardcode test data
4. **NEVER** skip error scenarios
5. **NEVER** ignore memory leaks
6. **NEVER** forget cleanup

---

## 🚨 **Critical Success Factors**

### **IMPORTANT: Measure Success**

**THINK** about these metrics:

| Metric | **Target** | **Critical** |
|--------|------------|--------------|
| Code Coverage | ≥ 80% | YES |
| Test Execution Time | < 10 min | YES |
| Flaky Test Rate | 0% | YES |
| Mock Coverage | 100% | YES |
| Security Tests | Complete | YES |
| Performance Tests | Baselined | YES |

---

## 📝 **Final Execution Checklist**

### **Before Completing Any Test Generation Task:**

1. **THINK**: Have I analyzed the architecture thoroughly?
2. **VERIFY**: Are all tests running in parallel where possible?
3. **CHECK**: Are all critical paths covered?
4. **CONFIRM**: Are mocks properly configured?
5. **VALIDATE**: Do tests follow best practices?
6. **CLEAN**: Are all temporary files removed?
7. **DOCUMENT**: Is complex logic explained?
8. **OPTIMIZE**: Can execution be faster?

---

## 🎬 **Execution Command**

```markdown
**OPTIMAL EXECUTION SEQUENCE:**

1. **PARALLEL ANALYSIS** (execute simultaneously):
   - Analyze architecture
   - Map dependencies  
   - Identify test gaps
   - Detect security issues
   - Find performance bottlenecks

2. **THINK** (reflect on analysis):
   - What patterns were found?
   - What are the critical paths?
   - What needs 100% coverage?

3. **PARALLEL GENERATION** (execute simultaneously):
   - Generate unit tests
   - Generate integration tests
   - Generate UI tests
   - Generate mocks
   - Generate fixtures

4. **VERIFY & ITERATE**:
   - Run tests in parallel
   - Analyze coverage
   - Generate gap tests
   - Optimize execution

5. **CLEAN UP**:
   - Remove temporary files
   - Organize test structure
   - Document complex tests
```

---

**REMEMBER**: The power of Claude 4 lies in **thinking deeply**, **executing in parallel**, and **iterating based on results**. **ALWAYS** leverage these capabilities for optimal test generation.

**NEVER** compromise on test quality for speed. **THINK** first, execute efficiently, and **ALWAYS** clean up after yourself.

---

*Framework Version*: 2.0.0  
*Optimization Level*: Maximum  
*Parallel Execution*: Enabled  
*Thinking Mode*: Deep Analysis  
*File Cleanup*: Mandatory
