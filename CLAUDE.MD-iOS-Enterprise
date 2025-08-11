# Claude Code - Enterprise iOS Application Framework
## Complete Project Creation, Clean Architecture & Testing Strategy

### 🧠 **CRITICAL: Leverage Advanced Thinking Capabilities**

**THINK** deeply about enterprise requirements before any code generation:
- Scalability requirements for 1M+ users
- Security compliance (SOC2, ISO 27001, PCI-DSS)
- Multi-region deployment strategy
- Team collaboration (20+ developers)
- CI/CD pipeline optimization
- Performance benchmarks

**ALWAYS** use interleaved thinking after each major decision to validate architectural choices.

---

## 🏗️ **ENTERPRISE PROJECT INITIALIZATION**

### **IMPORTANT: Parallel Project Setup**

**EXECUTE SIMULTANEOUSLY** when creating new enterprise project:

```markdown
**PARALLEL EXECUTION GROUP 1 - Project Foundation:**
1. Create Xcode workspace with modular structure
2. Initialize Swift Package Manager configuration
3. Setup git with branch protection rules
4. Configure CI/CD pipeline templates
5. Create documentation structure

**PARALLEL EXECUTION GROUP 2 - Architecture Scaffolding:**
1. Generate Clean Architecture layers
2. Create Domain-Driven Design structure
3. Setup dependency injection framework
4. Initialize networking layer
5. Configure persistence layer

**PARALLEL EXECUTION GROUP 3 - Quality Gates:**
1. Setup SwiftLint with enterprise rules
2. Configure SonarQube integration
3. Initialize test coverage reporting
4. Setup performance monitoring
5. Configure security scanning
```

**NEVER** create a project without these enterprise foundations.

---

## 🎯 **CLEAN ARCHITECTURE IMPLEMENTATION**

### **CRITICAL: Layer Separation & Dependencies**

```swift
// **IMPORTANT**: Dependency Rule - Dependencies point inward

┌─────────────────────────────────────────────┐
│          Presentation Layer                 │
│     (SwiftUI/UIKit, ViewModels)            │
│                    ↓                        │
├─────────────────────────────────────────────┤
│           Application Layer                 │
│    (Use Cases, Application Services)        │
│                    ↓                        │
├─────────────────────────────────────────────┤
│            Domain Layer                     │
│   (Entities, Value Objects, Domain Services)│
│                    ↓                        │
├─────────────────────────────────────────────┤
│          Infrastructure Layer               │
│   (Repositories, External Services, DB)     │
└─────────────────────────────────────────────┘
```

### **Project Structure Generation**

**THINK** about scalability before generating structure:

```bash
EnterpriseApp/
├── App/                              # Application entry point
│   ├── AppDelegate.swift
│   ├── SceneDelegate.swift
│   ├── Configuration/               # Environment configs
│   │   ├── Development.xcconfig
│   │   ├── Staging.xcconfig
│   │   └── Production.xcconfig
│   └── DependencyInjection/         # DI container setup
│       └── AppContainer.swift
│
├── Modules/                          # Feature modules (SPM)
│   ├── Core/                        # Shared core functionality
│   │   ├── Domain/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   ├── DomainErrors/
│   │   │   └── Specifications/
│   │   ├── Infrastructure/
│   │   │   ├── Network/
│   │   │   ├── Persistence/
│   │   │   ├── Security/
│   │   │   └── Analytics/
│   │   └── Application/
│   │       ├── Protocols/
│   │       └── DTOs/
│   │
│   ├── Authentication/              # Feature module example
│   │   ├── Domain/
│   │   │   ├── Entities/
│   │   │   │   ├── User.swift
│   │   │   │   ├── Session.swift
│   │   │   │   └── Credentials.swift
│   │   │   ├── ValueObjects/
│   │   │   │   ├── Email.swift
│   │   │   │   ├── Password.swift
│   │   │   │   └── Token.swift
│   │   │   ├── Repositories/       # Protocols only
│   │   │   │   └── AuthRepositoryProtocol.swift
│   │   │   └── Services/           # Domain services
│   │   │       └── AuthenticationService.swift
│   │   │
│   │   ├── Application/             # Use Cases
│   │   │   ├── UseCases/
│   │   │   │   ├── LoginUseCase.swift
│   │   │   │   ├── LogoutUseCase.swift
│   │   │   │   ├── RefreshTokenUseCase.swift
│   │   │   │   └── BiometricAuthUseCase.swift
│   │   │   ├── DTOs/
│   │   │   │   └── LoginResponseDTO.swift
│   │   │   └── Mappers/
│   │   │       └── UserMapper.swift
│   │   │
│   │   ├── Infrastructure/          # Implementation details
│   │   │   ├── Repositories/
│   │   │   │   └── AuthRepository.swift
│   │   │   ├── Network/
│   │   │   │   ├── AuthAPI.swift
│   │   │   │   └── AuthEndpoints.swift
│   │   │   └── Keychain/
│   │   │       └── KeychainManager.swift
│   │   │
│   │   ├── Presentation/            # UI Layer
│   │   │   ├── ViewModels/
│   │   │   │   └── LoginViewModel.swift
│   │   │   ├── Views/
│   │   │   │   ├── LoginView.swift
│   │   │   │   └── BiometricPromptView.swift
│   │   │   ├── Coordinators/
│   │   │   │   └── AuthCoordinator.swift
│   │   │   └── Components/
│   │   │       └── SecureTextField.swift
│   │   │
│   │   └── Tests/                   # Module tests
│   │       ├── UnitTests/
│   │       ├── IntegrationTests/
│   │       └── UITests/
│   │
│   └── [Other Feature Modules]/
│
├── Resources/                        # App resources
│   ├── Assets.xcassets/
│   ├── Localizations/
│   ├── Fonts/
│   └── LaunchScreen.storyboard
│
├── Scripts/                          # Build & automation scripts
│   ├── swiftgen.yml
│   ├── sourcery.yml
│   ├── pre-commit.sh
│   └── test-coverage.sh
│
└── Tests/                           # App-level tests
    ├── EndToEndTests/
    ├── PerformanceTests/
    └── SecurityTests/
```

---

## 🏛️ **DOMAIN-DRIVEN DESIGN IMPLEMENTATION**

### **CRITICAL: Domain Layer Purity**

**ALWAYS** keep domain layer pure:

```swift
// **IMPORTANT**: Domain entities have NO external dependencies

// ✅ CORRECT - Pure domain entity
struct Account: Equatable {
    let id: AccountID              // Value object
    let iban: IBAN                 // Value object
    let balance: Money             // Value object
    let status: AccountStatus
    let holder: AccountHolder
    
    // Business rules in domain
    func canWithdraw(_ amount: Money) -> Bool {
        return balance >= amount && status == .active
    }
    
    // Domain events
    func withdraw(_ amount: Money) throws -> (Account, DomainEvent) {
        guard canWithdraw(amount) else {
            throw DomainError.insufficientFunds
        }
        
        let updatedAccount = Account(
            id: id,
            iban: iban,
            balance: balance - amount,
            status: status,
            holder: holder
        )
        
        let event = AccountDebited(
            accountId: id,
            amount: amount,
            timestamp: Date()
        )
        
        return (updatedAccount, event)
    }
}

// ❌ WRONG - Domain with external dependencies
struct BadAccount {
    let api: NetworkService  // ❌ Infrastructure leak
    @Published var balance   // ❌ Framework dependency
}
```

### **Value Objects Implementation**

**ALWAYS** use value objects for domain concepts:

```swift
// **THINK**: What domain concepts need type safety?

struct Money: Equatable, Comparable {
    let amount: Decimal
    let currency: Currency
    
    // **IMPORTANT**: Business logic in value objects
    static func + (lhs: Money, rhs: Money) throws -> Money {
        guard lhs.currency == rhs.currency else {
            throw DomainError.currencyMismatch
        }
        return Money(amount: lhs.amount + rhs.amount, currency: lhs.currency)
    }
    
    // **ALWAYS** validate on creation
    init(amount: Decimal, currency: Currency) throws {
        guard amount >= 0 else {
            throw DomainError.invalidAmount
        }
        self.amount = amount
        self.currency = currency
    }
}

struct IBAN: Equatable {
    let value: String
    
    init(_ value: String) throws {
        guard IBAN.isValid(value) else {
            throw DomainError.invalidIBAN
        }
        self.value = value
    }
    
    private static func isValid(_ iban: String) -> Bool {
        // **IMPORTANT**: Complex validation logic
        return IBANValidator.validate(iban)
    }
}
```

---

## 💉 **DEPENDENCY INJECTION ARCHITECTURE**

### **CRITICAL: Enterprise DI Container**

**THINK** about dependency lifecycle management:

```swift
// **IMPORTANT**: Type-safe, compile-time verified DI

protocol DIContainer {
    func register<T>(_ type: T.Type, factory: @escaping (DIContainer) -> T)
    func resolve<T>(_ type: T.Type) -> T
    func resolve<T>(_ type: T.Type) -> T?
}

@propertyWrapper
struct Injected<T> {
    private let keyPath: WritableKeyPath<DIContainer, T>
    
    var wrappedValue: T {
        get { DIContainer.shared[keyPath: keyPath] }
        set { DIContainer.shared[keyPath: keyPath] = newValue }
    }
    
    init(_ keyPath: WritableKeyPath<DIContainer, T>) {
        self.keyPath = keyPath
    }
}

// **PARALLEL EXECUTION**: Register all dependencies simultaneously
class AppContainer: DIContainer {
    
    func configureDependencies() {
        // **EXECUTE IN PARALLEL**:
        Task {
            async let networkConfig = configureNetworking()
            async let persistenceConfig = configurePersistence()
            async let securityConfig = configureSecurity()
            async let analyticsConfig = configureAnalytics()
            async let featuresConfig = configureFeatures()
            
            await networkConfig
            await persistenceConfig
            await securityConfig
            await analyticsConfig
            await featuresConfig
        }
    }
    
    private func configureNetworking() async {
        register(NetworkService.self) { _ in
            NetworkServiceImpl(
                session: .shared,
                interceptors: [
                    AuthInterceptor(),
                    LoggingInterceptor(),
                    RetryInterceptor()
                ]
            )
        }
    }
}
```

---

## 🚀 **USE CASE IMPLEMENTATION**

### **IMPORTANT: Clean Use Case Pattern**

**ALWAYS** follow single responsibility principle:

```swift
// **THINK**: Each use case does ONE thing

protocol UseCase {
    associatedtype Input
    associatedtype Output
    
    func execute(_ input: Input) async throws -> Output
}

final class TransferMoneyUseCase: UseCase {
    typealias Input = TransferRequest
    typealias Output = TransferResult
    
    // **IMPORTANT**: Inject dependencies via protocols
    private let accountRepository: AccountRepositoryProtocol
    private let transactionRepository: TransactionRepositoryProtocol
    private let notificationService: NotificationServiceProtocol
    private let auditLogger: AuditLoggerProtocol
    
    init(
        accountRepository: AccountRepositoryProtocol,
        transactionRepository: TransactionRepositoryProtocol,
        notificationService: NotificationServiceProtocol,
        auditLogger: AuditLoggerProtocol
    ) {
        self.accountRepository = accountRepository
        self.transactionRepository = transactionRepository
        self.notificationService = notificationService
        self.auditLogger = auditLogger
    }
    
    func execute(_ input: TransferRequest) async throws -> TransferResult {
        // **THINK**: What are the business rules?
        
        // 1. Validate request
        try validateTransfer(input)
        
        // 2. Check account balance
        let sourceAccount = try await accountRepository.find(input.sourceAccountId)
        guard sourceAccount.canWithdraw(input.amount) else {
            throw DomainError.insufficientFunds
        }
        
        // 3. Create transaction (atomic operation)
        let transaction = try await transactionRepository.createTransaction {
            // Debit source
            let (updatedSource, debitEvent) = try sourceAccount.withdraw(input.amount)
            try await accountRepository.update(updatedSource)
            
            // Credit destination
            let destAccount = try await accountRepository.find(input.destinationAccountId)
            let (updatedDest, creditEvent) = try destAccount.deposit(input.amount)
            try await accountRepository.update(updatedDest)
            
            return Transaction(
                id: TransactionID(),
                source: updatedSource.id,
                destination: updatedDest.id,
                amount: input.amount,
                status: .completed,
                events: [debitEvent, creditEvent]
            )
        }
        
        // 4. Send notifications (parallel execution)
        async let sourceNotification = notificationService.notify(
            sourceAccount.holder,
            .debit(input.amount)
        )
        async let destNotification = notificationService.notify(
            destAccount.holder,
            .credit(input.amount)
        )
        
        // 5. Audit logging
        await auditLogger.log(.transfer(transaction))
        
        // Wait for notifications
        _ = await (sourceNotification, destNotification)
        
        return TransferResult(transaction: transaction)
    }
    
    private func validateTransfer(_ request: TransferRequest) throws {
        // **IMPORTANT**: Business validation logic
        guard request.amount.amount > 0 else {
            throw DomainError.invalidAmount
        }
        
        guard request.sourceAccountId != request.destinationAccountId else {
            throw DomainError.sameAccountTransfer
        }
        
        // Additional business rules...
    }
}
```

---

## 🔧 **REPOSITORY PATTERN IMPLEMENTATION**

### **CRITICAL: Repository Abstraction**

**ALWAYS** define repository protocols in Domain layer:

```swift
// **Domain Layer - Protocol only**
protocol AccountRepositoryProtocol {
    func find(_ id: AccountID) async throws -> Account
    func findAll(for userId: UserID) async throws -> [Account]
    func update(_ account: Account) async throws
    func delete(_ id: AccountID) async throws
}

// **Infrastructure Layer - Implementation**
final class AccountRepository: AccountRepositoryProtocol {
    private let remoteDataSource: RemoteDataSourceProtocol
    private let localDataSource: LocalDataSourceProtocol
    private let cacheManager: CacheManagerProtocol
    
    // **THINK**: What's the optimal caching strategy?
    
    func find(_ id: AccountID) async throws -> Account {
        // **IMPORTANT**: Cache-first strategy
        
        // 1. Check memory cache
        if let cached = cacheManager.get(key: id.value) {
            return cached
        }
        
        // 2. Check local database
        if let local = try await localDataSource.fetch(id: id.value) {
            cacheManager.set(key: id.value, value: local)
            return AccountMapper.toDomain(local)
        }
        
        // 3. Fetch from remote
        let remote = try await remoteDataSource.getAccount(id: id.value)
        
        // 4. Update local storage (parallel)
        async let saveLocal = localDataSource.save(remote)
        async let updateCache = cacheManager.set(key: id.value, value: remote)
        
        _ = await (saveLocal, updateCache)
        
        return AccountMapper.toDomain(remote)
    }
}
```

---

## 🎨 **PRESENTATION LAYER ARCHITECTURE**

### **IMPORTANT: MVVM with Coordinators**

**THINK** about separation of concerns:

```swift
// **ViewModel - Business logic for UI**
@MainActor
final class AccountListViewModel: ObservableObject {
    // **IMPORTANT**: Published properties for SwiftUI binding
    @Published private(set) var state: ViewState<[AccountViewModel]> = .idle
    @Published private(set) var isRefreshing = false
    @Published var searchText = ""
    
    // **Use case injection**
    private let fetchAccountsUseCase: FetchAccountsUseCase
    private let refreshAccountsUseCase: RefreshAccountsUseCase
    private let coordinator: AccountCoordinator
    
    private var cancellables = Set<AnyCancellable>()
    
    init(
        fetchAccountsUseCase: FetchAccountsUseCase,
        refreshAccountsUseCase: RefreshAccountsUseCase,
        coordinator: AccountCoordinator
    ) {
        self.fetchAccountsUseCase = fetchAccountsUseCase
        self.refreshAccountsUseCase = refreshAccountsUseCase
        self.coordinator = coordinator
        
        setupBindings()
    }
    
    private func setupBindings() {
        // **IMPORTANT**: Reactive search with debounce
        $searchText
            .debounce(for: .milliseconds(300), scheduler: RunLoop.main)
            .removeDuplicates()
            .sink { [weak self] searchTerm in
                Task { @MainActor in
                    await self?.search(searchTerm)
                }
            }
            .store(in: &cancellables)
    }
    
    func loadAccounts() {
        Task { @MainActor in
            state = .loading
            
            do {
                let accounts = try await fetchAccountsUseCase.execute()
                let viewModels = accounts.map(AccountViewModel.init)
                state = .loaded(viewModels)
            } catch {
                state = .error(error)
            }
        }
    }
    
    func selectAccount(_ account: AccountViewModel) {
        coordinator.showAccountDetails(account.id)
    }
}

// **Coordinator - Navigation logic**
protocol Coordinator: AnyObject {
    func start()
}

final class AccountCoordinator: Coordinator {
    private let navigationController: UINavigationController
    private let container: DIContainer
    
    init(navigationController: UINavigationController, container: DIContainer) {
        self.navigationController = navigationController
        self.container = container
    }
    
    func start() {
        let viewModel = AccountListViewModel(
            fetchAccountsUseCase: container.resolve(FetchAccountsUseCase.self),
            refreshAccountsUseCase: container.resolve(RefreshAccountsUseCase.self),
            coordinator: self
        )
        
        let view = AccountListView(viewModel: viewModel)
        let hostingController = UIHostingController(rootView: view)
        
        navigationController.pushViewController(hostingController, animated: false)
    }
    
    func showAccountDetails(_ accountId: AccountID) {
        let detailCoordinator = AccountDetailCoordinator(
            navigationController: navigationController,
            container: container,
            accountId: accountId
        )
        detailCoordinator.start()
    }
}
```

---

## 🧪 **COMPREHENSIVE TESTING STRATEGY**

### **CRITICAL: Test Generation for Each Layer**

**EXECUTE IN PARALLEL** - Generate tests for all layers simultaneously:

```swift
// **PARALLEL TEST GENERATION GROUPS:**

// GROUP 1: Domain Layer Tests
class AccountEntityTests: XCTestCase {
    func test_withdraw_withSufficientFunds_shouldSucceed() {
        // Arrange
        let account = Account(
            balance: Money(amount: 1000, currency: .USD)
        )
        
        // Act
        let result = try account.withdraw(Money(amount: 100, currency: .USD))
        
        // Assert
        XCTAssertEqual(result.0.balance.amount, 900)
        XCTAssertTrue(result.1 is AccountDebited)
    }
    
    func test_withdraw_withInsufficientFunds_shouldThrow() {
        // Test domain rules
    }
}

// GROUP 2: Use Case Tests
class TransferMoneyUseCaseTests: XCTestCase {
    private var sut: TransferMoneyUseCase!
    private var mockAccountRepo: MockAccountRepository!
    private var mockTransactionRepo: MockTransactionRepository!
    
    override func setUp() {
        super.setUp()
        mockAccountRepo = MockAccountRepository()
        mockTransactionRepo = MockTransactionRepository()
        
        sut = TransferMoneyUseCase(
            accountRepository: mockAccountRepo,
            transactionRepository: mockTransactionRepo
        )
    }
    
    func test_execute_withValidTransfer_shouldComplete() async throws {
        // Test use case orchestration
    }
}

// GROUP 3: Repository Tests
class AccountRepositoryTests: XCTestCase {
    func test_find_withCachedData_shouldReturnFromCache() async {
        // Test caching strategy
    }
    
    func test_find_withoutCache_shouldFetchFromRemote() async {
        // Test remote fetching
    }
}

// GROUP 4: ViewModel Tests
class AccountListViewModelTests: XCTestCase {
    @MainActor
    func test_loadAccounts_shouldUpdateState() async {
        // Test state management
    }
    
    @MainActor
    func test_search_shouldDebounceAndFilter() async {
        // Test reactive behavior
    }
}

// GROUP 5: Integration Tests
class AccountModuleIntegrationTests: XCTestCase {
    func test_completeAccountFlow_shouldWork() async {
        // Test full module integration
    }
}
```

---

## 🔐 **ENTERPRISE SECURITY IMPLEMENTATION**

### **CRITICAL: Security at Every Layer**

**ALWAYS** implement defense in depth:

```swift
// **Layer 1: Network Security**
class SecureNetworkService {
    private let certificatePinner: CertificatePinner
    private let encryptor: AES256Encryptor
    
    func request<T: Decodable>(_ endpoint: Endpoint) async throws -> T {
        // **IMPORTANT**: Certificate pinning
        try certificatePinner.validate(endpoint.url)
        
        // **IMPORTANT**: Request encryption
        let encryptedBody = try encryptor.encrypt(endpoint.body)
        
        // **IMPORTANT**: Secure headers
        var request = URLRequest(url: endpoint.url)
        request.setValue(generateHMAC(endpoint), forHTTPHeaderField: "X-HMAC")
        request.setValue(UUID().uuidString, forHTTPHeaderField: "X-Request-ID")
        
        // Make request with timeout
        let (data, response) = try await session.data(for: request)
        
        // **IMPORTANT**: Response validation
        try validateResponse(response)
        
        // Decrypt and decode
        let decrypted = try encryptor.decrypt(data)
        return try decoder.decode(T.self, from: decrypted)
    }
}

// **Layer 2: Data Security**
class SecureDataStore {
    private let keychain: KeychainProtocol
    private let cryptoManager: CryptoManagerProtocol
    
    func store<T: Codable>(_ object: T, key: String) throws {
        // **ALWAYS** encrypt sensitive data
        let data = try encoder.encode(object)
        let encrypted = try cryptoManager.encrypt(data)
        try keychain.store(encrypted, key: key)
    }
}

// **Layer 3: Application Security**
class SecurityManager {
    func validateEnvironment() throws {
        // **CRITICAL**: Security checks
        guard !isJailbroken() else {
            throw SecurityError.jailbrokenDevice
        }
        
        guard !isBeingDebugged() else {
            throw SecurityError.debuggerAttached
        }
        
        guard certificateIsValid() else {
            throw SecurityError.invalidCertificate
        }
    }
}
```

---

## 📊 **PERFORMANCE OPTIMIZATION**

### **IMPORTANT: Enterprise Performance Standards**

**THINK** about performance at scale:

```swift
// **CRITICAL: Performance Monitoring**
class PerformanceMonitor {
    
    @propertyWrapper
    struct Measured<T> {
        private let name: String
        private var value: T
        
        init(wrappedValue: T, _ name: String) {
            self.name = name
            self.value = wrappedValue
        }
        
        var wrappedValue: T {
            get { value }
            set {
                let start = CFAbsoluteTimeGetCurrent()
                value = newValue
                let duration = CFAbsoluteTimeGetCurrent() - start
                
                // **IMPORTANT**: Track performance metrics
                MetricsCollector.record(
                    metric: .custom(name),
                    value: duration,
                    tags: ["platform": "ios"]
                )
            }
        }
    }
    
    // **Usage**
    @Measured("api_call_duration")
    var apiCallDuration: TimeInterval = 0
}

// **IMPORTANT: Caching Strategy**
actor CacheManager {
    private var memoryCache: NSCache<NSString, AnyObject>
    private var diskCache: DiskCache
    
    // **THINK**: What's the optimal cache size?
    init() {
        memoryCache = NSCache()
        memoryCache.countLimit = 100
        memoryCache.totalCostLimit = 50 * 1024 * 1024 // 50MB
        
        diskCache = DiskCache(sizeLimit: 100 * 1024 * 1024) // 100MB
    }
}

// **CRITICAL: Database Optimization**
class OptimizedDataStore {
    private let coreDataStack: CoreDataStack
    
    func fetchLargeDataSet() async -> [Entity] {
        // **IMPORTANT**: Batch fetching
        return await coreDataStack.performBackgroundTask { context in
            let request = NSFetchRequest<Entity>(entityName: "Entity")
            request.fetchBatchSize = 20
            request.returnsObjectsAsFaults = false
            request.relationshipKeyPathsForPrefetching = ["relationships"]
            
            return try context.fetch(request)
        }
    }
}
```

---

## 🚀 **CI/CD PIPELINE CONFIGURATION**

### **CRITICAL: Automated Quality Gates**

**PARALLEL EXECUTION** in CI/CD:

```yaml
# .github/workflows/enterprise-ios.yml

name: Enterprise iOS CI/CD

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main]

jobs:
  # **PARALLEL JOB GROUP 1**
  code-quality:
    runs-on: macos-latest
    strategy:
      matrix:
        task: [lint, format, analyze]
    steps:
      - name: Run ${{ matrix.task }}
        run: |
          case ${{ matrix.task }} in
            lint) swiftlint lint --strict ;;
            format) swift-format lint --recursive . ;;
            analyze) xcodebuild analyze -scheme Enterprise ;;
          esac

  # **PARALLEL JOB GROUP 2**
  testing:
    runs-on: macos-latest
    strategy:
      matrix:
        test-suite: [unit, integration, ui, performance, security]
    steps:
      - name: Run ${{ matrix.test-suite }} tests
        run: |
          xcodebuild test \
            -scheme Enterprise \
            -testPlan ${{ matrix.test-suite }} \
            -parallel-testing-enabled YES \
            -maximum-parallel-test-runners 4

  # **CRITICAL: Security Scanning**
  security:
    runs-on: ubuntu-latest
    steps:
      - name: SAST Scan
        run: |
          # Static Application Security Testing
          semgrep --config=auto --json -o sast-results.json
          
      - name: Dependency Check
        run: |
          # Check for vulnerable dependencies
          swift package audit
          
      - name: Secret Scanning
        run: |
          # Scan for hardcoded secrets
          trufflehog filesystem . --json > secrets-scan.json

  # **IMPORTANT: Coverage Gates**
  coverage:
    needs: testing
    runs-on: macos-latest
    steps:
      - name: Generate Coverage Report
        run: |
          xcov --scheme Enterprise \
               --minimum_coverage_percentage 80 \
               --ignore_file_path "Tests/*,*.generated.swift"
               
      - name: Upload to SonarQube
        run: |
          sonar-scanner \
            -Dsonar.projectKey=enterprise-ios \
            -Dsonar.sources=. \
            -Dsonar.swift.coverage.reportPaths=coverage.xml
```

---

## 🧹 **FILE CLEANUP & MAINTENANCE**

### **CRITICAL: Clean Workspace Management**

**ALWAYS** clean up after operations:

```bash
# **CLEANUP SCRIPT - Run after EVERY operation**

#!/bin/bash

cleanup_enterprise_workspace() {
    echo "🧹 Starting enterprise cleanup..."
    
    # **PARALLEL CLEANUP OPERATIONS**
    {
        # Clean derived data
        rm -rf ~/Library/Developer/Xcode/DerivedData/* &
        
        # Clean build artifacts
        rm -rf .build/ &
        rm -rf build/ &
        
        # Clean temporary files
        find . -name "*.tmp" -delete &
        find . -name "*.temp" -delete &
        find . -name ".DS_Store" -delete &
        
        # Clean test artifacts
        rm -rf TestResults/ &
        rm -rf coverage/ &
        rm -rf *.xcresult &
        
        # Clean generated files
        rm -rf Generated/*.generated.swift &
        
        # Clean package caches
        rm -rf .swiftpm/ &
        rm -rf Pods/ &
        
        wait
    }
    
    echo "✅ Cleanup complete"
}

# **ALWAYS** run cleanup after:
# - Test generation
# - Build operations
# - Code generation
# - CI/CD runs
```

---

## 📋 **ENTERPRISE CHECKLIST**

### **BEFORE ANY CODE GENERATION:**

**THINK** and verify:

- [ ] **Architecture Decision**: Clean Architecture with DDD?
- [ ] **Module Structure**: Feature-based modularization?
- [ ] **Dependency Injection**: Type-safe DI container?
- [ ] **Security Requirements**: SOC2, PCI-DSS compliance?
- [ ] **Performance Targets**: < 2s app launch, < 100ms response?
- [ ] **Testing Strategy**: 80% minimum coverage?
- [ ] **CI/CD Pipeline**: Automated quality gates?
- [ ] **Documentation**: Architecture decision records?

### **DURING DEVELOPMENT:**

- [ ] **Domain Purity**: No external dependencies in domain?
- [ ] **Use Case Clarity**: Single responsibility?
- [ ] **Repository Pattern**: Proper abstraction?
- [ ] **Error Handling**: Comprehensive error types?
- [ ] **Logging**: Structured logging implemented?
- [ ] **Analytics**: Event tracking in place?
- [ ] **Accessibility**: WCAG 2.1 AA compliance?
- [ ] **Localization**: All strings externalized?

### **AFTER IMPLEMENTATION:**

- [ ] **Test Coverage**: >= 80% achieved?
- [ ] **Performance Tests**: Benchmarks met?
- [ ] **Security Scan**: No vulnerabilities?
- [ ] **Code Review**: Approved by 2+ seniors?
- [ ] **Documentation**: Updated and complete?
- [ ] **Cleanup**: All temporary files removed?

---

## 🎯 **SUCCESS METRICS**

### **CRITICAL: Enterprise KPIs**

| Metric | Target | Critical Threshold |
|--------|--------|-------------------|
| **Code Coverage** | 85% | < 80% blocks deployment |
| **Technical Debt** | < 5% | > 10% requires refactor |
| **Build Time** | < 5 min | > 10 min needs optimization |
| **Test Execution** | < 10 min | > 15 min needs parallelization |
| **App Launch** | < 2s | > 3s fails performance |
| **API Response** | < 100ms | > 500ms needs caching |
| **Crash Rate** | < 0.1% | > 0.5% critical issue |
| **Memory Leaks** | 0 | Any leak blocks release |

---

## 🚨 **CRITICAL SUCCESS FACTORS**

### **ENTERPRISE EXCELLENCE REQUIREMENTS:**

1. **SCALABILITY**: Support 1M+ concurrent users
2. **RELIABILITY**: 99.99% uptime SLA
3. **SECURITY**: Zero security vulnerabilities
4. **PERFORMANCE**: Sub-second response times
5. **MAINTAINABILITY**: Clean, documented, tested code
6. **COMPLIANCE**: Meet all regulatory requirements
7. **OBSERVABILITY**: Full monitoring and alerting

---

## 📝 **OPTIMAL EXECUTION SEQUENCE**

```markdown
**ENTERPRISE PROJECT CREATION WORKFLOW:**

1. **PARALLEL INITIALIZATION** (5 operations simultaneously):
   - Create Xcode workspace
   - Setup package structure
   - Configure CI/CD
   - Initialize git repository
   - Create documentation

2. **THINK & DESIGN** (Architecture planning):
   - Define bounded contexts
   - Design domain model
   - Plan module boundaries
   - Define integration points

3. **PARALLEL IMPLEMENTATION** (All layers simultaneously):
   - Generate domain entities
   - Create use cases
   - Implement repositories
   - Build UI components
   - Setup infrastructure

4. **PARALLEL TESTING** (All test types simultaneously):
   - Unit tests (80% coverage)
   - Integration tests
   - UI tests
   - Performance tests
   - Security tests

5. **OPTIMIZATION**:
   - Analyze performance metrics
   - Optimize critical paths
   - Implement caching
   - Fine-tune queries

6. **DEPLOYMENT PREPARATION**:
   - Security audit
   - Performance validation
   - Documentation review
   - Compliance check

7. **CLEANUP**:
   - Remove all temporary files
   - Optimize build artifacts
   - Archive documentation
```

---

## 🎖️ **FINAL PRINCIPLES**

### **ALWAYS Remember:**

1. **THINK** before coding - Architecture first
2. **PARALLELIZE** everything - Maximum efficiency
3. **TEST** comprehensively - Quality is non-negotiable
4. **SECURE** by default - Security in depth
5. **OPTIMIZE** for scale - Enterprise performance
6. **DOCUMENT** thoroughly - Knowledge preservation
7. **CLEAN** workspace - Professional standards

### **NEVER Compromise On:**

1. **Domain purity** - Keep business logic clean
2. **Test coverage** - Minimum 80% enforced
3. **Security standards** - Zero vulnerabilities
4. **Performance benchmarks** - Meet all KPIs
5. **Code quality** - Clean architecture maintained
6. **Documentation** - Always up-to-date
7. **Cleanup** - Leave no trace

---

**REMEMBER**: Enterprise iOS development requires **thinking at scale**, **executing efficiently**, and **maintaining excellence**. Every decision impacts thousands of users and millions of transactions.

**This framework ensures**: Scalable architecture, comprehensive testing, enterprise security, optimal performance, and professional standards throughout the development lifecycle.

---

*Framework Version*: 3.0.0  
*Architecture*: Clean + DDD  
*Coverage Target*: 85%  
*Performance*: Enterprise Grade  
*Security*: SOC2/PCI-DSS Compliant  
*Scale*: 1M+ Users  

**STATUS: 🟢 ENTERPRISE READY - FULL OPTIMIZATION ENABLED**
