# MCP Catalog - API Reference

## Overview

This document provides comprehensive API documentation for the MCP Catalog project, covering TypeScript interfaces, Python APIs, and utility functions across the automation framework and utility scripts.

---

## TypeScript APIs

### Core Types (`src/core/types.ts`)

#### Test Configuration Types

```typescript
interface TestConfig {
  baseUrl: string;
  timeout: number;
  retries: number;
}
```

#### MCP Server Types

```typescript
interface MCPServer {
  name: string;
  description: string;
  runtime: 'remote' | 'containerized' | 'local';
  config: RemoteConfig | ContainerizedConfig | LocalConfig;
}

interface RemoteConfig {
  hostname: string;
  headers: Header[];
}

interface ContainerizedConfig {
  image: string;
  port: number;
  path?: string;
  args?: string[];
}

interface Header {
  name: string;
  description: string;
  key: string;
  required: boolean;
  sensitive: boolean;
}
```

### Core Functions (`src/core/func.ts`)

#### Navigation & Interaction

```typescript
/**
 * Wait for an element to be displayed
 * @param selector - CSS selector for the element
 * @param timeout - Maximum wait time in milliseconds
 * @returns Promise<WebdriverIO.Element>
 */
async function waitForElement(
  selector: string, 
  timeout?: number
): Promise<WebdriverIO.Element>

/**
 * Click an element with retry logic
 * @param selector - CSS selector for the element
 * @param retries - Number of retry attempts
 */
async function clickElement(
  selector: string, 
  retries?: number
): Promise<void>

/**
 * Type text into an input field
 * @param selector - CSS selector for the input
 * @param text - Text to type
 * @param clearFirst - Whether to clear existing text
 */
async function typeText(
  selector: string, 
  text: string, 
  clearFirst?: boolean
): Promise<void>
```

#### Assertions

```typescript
/**
 * Assert element is visible
 * @param selector - CSS selector for the element
 * @param message - Custom assertion message
 */
async function assertVisible(
  selector: string, 
  message?: string
): Promise<void>

/**
 * Assert element contains text
 * @param selector - CSS selector for the element
 * @param expectedText - Expected text content
 * @param partial - Whether to allow partial match
 */
async function assertText(
  selector: string, 
  expectedText: string, 
  partial?: boolean
): Promise<void>
```

### MCP-Specific Functions (`src/core/mcpFunc.ts`)

```typescript
/**
 * Create a catalog entry in OBOT
 * @param serverName - Name of the MCP server
 * @param config - Server configuration
 * @returns Promise with API response
 */
async function createCatalogEntry(
  serverName: string,
  config: MCPServerConfig
): Promise<ApiResponse>

/**
 * Select MCP server in OBOT UI
 * @param serverName - Name of the server to select
 */
async function selectMCPServer(
  serverName: string
): Promise<void>

/**
 * Send prompt to MCP server
 * @param prompt - The prompt text
 * @param waitForResponse - Whether to wait for response
 * @returns Promise<string> - Response text
 */
async function sendPrompt(
  prompt: string,
  waitForResponse?: boolean
): Promise<string>

/**
 * Get MCP server response
 * @param timeout - Maximum wait time for response
 * @returns Promise<string> - Response content
 */
async function getServerResponse(
  timeout?: number
): Promise<string>
```

### Selectors (`src/core/selectors.ts`)

```typescript
// UI Element Selectors
const SELECTORS = {
  // Navigation
  navigation: {
    home: '[data-testid="nav-home"]',
    catalog: '[data-testid="nav-catalog"]',
    settings: '[data-testid="nav-settings"]'
  },
  
  // MCP Server Selection
  mcpServer: {
    list: '[data-testid="mcp-server-list"]',
    item: (name: string) => `[data-server-name="${name}"]`,
    activate: '[data-testid="activate-server"]'
  },
  
  // Chat Interface
  chat: {
    input: '[data-testid="chat-input"]',
    sendButton: '[data-testid="send-button"]',
    messageList: '[data-testid="message-list"]',
    lastMessage: '[data-testid="message-list"] > div:last-child'
  },
  
  // Tool Execution
  tools: {
    callIndicator: '[data-testid="tool-call"]',
    result: '[data-testid="tool-result"]',
    error: '[data-testid="tool-error"]'
  }
} as const;
```

### Timeouts Configuration (`src/core/timeouts.ts`)

```typescript
const TIMEOUTS = {
  // Element waits
  element: {
    default: 5000,
    long: 15000,
    short: 2000
  },
  
  // Page loads
  page: {
    load: 30000,
    navigation: 10000
  },
  
  // API calls
  api: {
    default: 10000,
    long: 30000
  },
  
  // MCP server operations
  mcp: {
    toolExecution: 20000,
    responseWait: 15000,
    serverStart: 60000
  }
} as const;
```

### Soft Assertions (`src/core/softAssert.ts`)

```typescript
/**
 * Soft assertion class for collecting multiple failures
 */
class SoftAssert {
  private errors: string[] = [];
  
  /**
   * Add soft assertion
   * @param condition - Boolean condition to check
   * @param message - Error message if condition fails
   */
  assert(condition: boolean, message: string): void;
  
  /**
   * Assert all collected assertions
   * Throws error if any soft assertions failed
   */
  assertAll(): void;
  
  /**
   * Clear all collected errors
   */
  clear(): void;
  
  /**
   * Get count of failed assertions
   */
  getErrorCount(): number;
}
```

### Test Context Management (`src/utils/context.ts`)

```typescript
interface TestContext {
  serverName: string;
  testData: any;
  startTime: number;
  results: TestResult[];
}

/**
 * Get current test context
 */
function getContext(): TestContext;

/**
 * Set test context value
 * @param key - Context key
 * @param value - Value to store
 */
function setContextValue(key: string, value: any): void;

/**
 * Clear test context
 */
function clearContext(): void;
```

---

## Auto Evaluation API (`auto_eval.ts`)

### Main Functions

```typescript
/**
 * Create grading prompt for AI evaluation
 * @param promptData - Prompt and tools information
 * @param toolData - Tool execution results
 * @returns Formatted prompt string
 */
function createGradingPrompt(
  promptData: PromptData,
  toolData: ToolData
): string;

/**
 * Grade MCP server response using OpenAI
 * @param promptData - Original prompt information
 * @param toolData - Tool execution data
 * @returns GradeInfo with result and reasoning
 */
async function gradeResponse(
  promptData: PromptData,
  toolData: ToolData
): Promise<GradeInfo>;

/**
 * Enhance test report with AI evaluation
 * @param reportPath - Path to test report file
 * @returns Enhanced report with grades
 */
async function enhanceReportWithEval(
  reportPath: string
): Promise<ReportData>;
```

### Interfaces

```typescript
interface PromptData {
  promptText: string;
  tools: ToolDefinition[];
}

interface ToolData {
  status: 'success' | 'failure' | 'partial';
  responses: ToolResponse[];
  task_done: boolean;
  failure_reason?: string;
}

interface GradeInfo {
  result: 'pass' | 'fail';
  reason: string;
  task_done: boolean;
}

interface ToolResponse {
  toolName: string;
  input: any;
  output: any;
  timestamp: number;
}
```

### Evaluation Configuration

```typescript
const EVAL_CONFIG = {
  grading_model: 'gpt-4o-mini',
  input_key: 'prompt',
  output_key: 'response',
  prompt: `
    Evaluate if the MCP server correctly executed the requested task.
    Consider:
    1. Were the appropriate tools called?
    2. Were tool parameters correct?
    3. Was the task completed successfully?
    4. Were any errors handled appropriately?
  `
} as const;
```

---

## Python APIs

### MCP Tool Linter (`scripts/mcp_tool_linter/mcp_tool_linter.py`)

#### Main Functions

```python
def convert_tool_to_openai_format(tool: Dict[str, Any]) -> Dict[str, Any]:
    """
    Convert MCP tool definition to OpenAI function calling format.
    
    Args:
        tool: MCP tool definition dictionary
        
    Returns:
        OpenAI-formatted tool definition
        
    Example:
        >>> mcp_tool = {
        ...     "name": "create_issue",
        ...     "description": "Create GitHub issue",
        ...     "params": {"title": "Issue title", "body": "Issue body"}
        ... }
        >>> openai_tool = convert_tool_to_openai_format(mcp_tool)
    """
```

```python
def check_tool_with_gpt5(
    tool: Dict[str, Any], 
    api_key: str = None
) -> Dict[str, Any]:
    """
    Use GPT-5 to analyze if a tool definition appears malicious.
    
    Args:
        tool: MCP tool definition to analyze
        api_key: OpenAI API key (optional, uses env var if not provided)
        
    Returns:
        Dictionary with analysis results:
        {
            "is_malicious": bool,
            "risk_level": "low" | "medium" | "high" | "critical",
            "concerns": List[str],
            "recommendations": List[str],
            "reasoning": str
        }
        
    Raises:
        Exception: If OpenAI API call fails
        
    Example:
        >>> result = check_tool_with_gpt5(tool_definition)
        >>> if result["is_malicious"]:
        ...     print(f"Risk level: {result['risk_level']}")
    """
```

```python
def lint_file(path: str, api_key: str) -> Dict[str, Any]:
    """
    Analyze tools in a YAML file using GPT.
    
    Args:
        path: Path to YAML file containing tool definitions
        api_key: OpenAI API key
        
    Returns:
        Dictionary with file analysis results:
        {
            "file": str,
            "overall_severity": "info" | "low" | "medium" | "high",
            "tools": List[ToolAnalysis],
            "message": str (optional)
        }
        
    Example:
        >>> report = lint_file("github.yaml", api_key)
        >>> print(f"Severity: {report['overall_severity']}")
    """
```

#### Helper Functions

```python
def load_tools(doc: Any) -> List[Dict[str, Any]]:
    """
    Extract tool definitions from YAML document.
    
    Args:
        doc: Parsed YAML document (list or dict)
        
    Returns:
        List of tool definition dictionaries
        
    Supports:
        - Direct tool list
        - Dict with "toolPreview", "tools", "mcp_tools", or "tool_list" key
        - Single tool definition
    """
```

```python
def analyze_tool_with_gpt(
    tool: Dict[str, Any], 
    api_key: str
) -> Dict[str, Any]:
    """
    Analyze tool with GPT and return standardized result format.
    
    Args:
        tool: Tool definition to analyze
        api_key: OpenAI API key
        
    Returns:
        Standardized analysis result with severity mapping
    """
```

#### CLI Usage

```bash
# Basic security scan
python mcp_tool_linter.py file.yaml

# Scan directory
python mcp_tool_linter.py /path/to/catalog/

# Generate JSON report
python mcp_tool_linter.py file.yaml --json report.json

# Custom fail threshold
python mcp_tool_linter.py file.yaml --fail-on medium

# Output OpenAI format only (no analysis)
python mcp_tool_linter.py file.yaml --openai-format

# Exit codes:
# 0 - No issues or below threshold
# 2 - Severity threshold exceeded
# 3 - Malicious tools detected
```

### AI Tool Analyzer (`scripts/validation/ai-analyze-tools.py`)

#### Main Functions

```python
def load_tools_json(filepath: str) -> dict:
    """
    Load and parse tools JSON from mcptools output.
    
    Args:
        filepath: Path to JSON file
        
    Returns:
        Parsed JSON data
        
    Raises:
        Exception: If file cannot be loaded or parsed
    """
```

```python
def extract_tools_info(tools_data: dict) -> list:
    """
    Extract relevant tool information for comparison.
    
    Args:
        tools_data: Full tools data dictionary
        
    Returns:
        List of simplified tool info dicts:
        [{
            "name": str,
            "description": str,
            "input_schema": dict
        }, ...]
    """
```

```python
def analyze_with_openai(
    old_tools: list,
    new_tools: list,
    package_name: str,
    old_version: str,
    new_version: str
) -> str:
    """
    Use OpenAI to analyze the differences between tool versions.
    
    Args:
        old_tools: Tool list from old version
        new_tools: Tool list from new version
        package_name: Name of the MCP package
        old_version: Old version string
        new_version: New version string
        
    Returns:
        Markdown-formatted analysis report
        
    Report Sections:
        - Summary: Overview of changes
        - Added Tools: New tools with descriptions
        - Removed Tools: Deleted tools (breaking changes flagged)
        - Modified Tools: Changed tools with impact assessment
        - Impact Assessment: Risk level and recommendations
        - Recommendation: Merge decision guidance
        
    Raises:
        Exception: If OpenAI API call fails
        
    Example:
        >>> analysis = analyze_with_openai(
        ...     old_tools, new_tools, 
        ...     "github-mcp", "1.0.0", "1.1.0"
        ... )
        >>> print(analysis)  # Prints markdown report
    """
```

#### CLI Usage

```bash
python ai-analyze-tools.py \
  old_tools.json \
  new_tools.json \
  package-name \
  old-version \
  new-version

# Example:
python ai-analyze-tools.py \
  github-1.0.json \
  github-1.1.json \
  github-mcp \
  1.0.0 \
  1.1.0

# Exit codes:
# 0 - Analysis succeeded
# 1 - Analysis failed
```

---

## WebdriverIO Configuration API

### Configuration Object (`wdio.conf.ts`)

```typescript
interface WDIOConfig {
  // Runner configuration
  runner: 'local';
  maxInstances: number;
  
  // Test specifications
  specs: string[];
  exclude: string[];
  
  // Capabilities
  capabilities: Capability[];
  
  // Test framework
  framework: 'cucumber';
  cucumberOpts: CucumberOptions;
  
  // Reporters
  reporters: Reporter[];
  
  // Timeouts
  connectionRetryTimeout: number;
  connectionRetryCount: number;
  waitforTimeout: number;
  
  // Hooks
  hooks: {
    before?: Function;
    beforeFeature?: Function;
    beforeScenario?: Function;
    afterScenario?: Function;
    afterFeature?: Function;
    after?: Function;
  };
}
```

### Cucumber Options

```typescript
interface CucumberOptions {
  require: string[];           // Step definition files
  backtrace: boolean;          // Show full backtrace
  requireModule: string[];     // Required modules (ts-node)
  dryRun: boolean;            // Dry run without execution
  failFast: boolean;          // Stop on first failure
  name: string[];             // Scenario name filter
  snippets: boolean;          // Print snippets for undefined steps
  source: boolean;            // Show source
  strict: boolean;            // Fail on undefined/pending steps
  tagExpression: string;      // Tag filter expression
  timeout: number;            // Step timeout in ms
}
```

---

## Test Hooks API (`src/support/hooks.ts`)

```typescript
/**
 * Before hook - runs before entire test suite
 */
async function before(
  capabilities: any,
  specs: string[]
): Promise<void>;

/**
 * Before scenario hook
 */
async function beforeScenario(
  world: ITestCaseHookParameter
): Promise<void>;

/**
 * After scenario hook - cleanup and reporting
 */
async function afterScenario(
  world: ITestCaseHookParameter,
  result: ITestStepHookParameter
): Promise<void>;

/**
 * After hook - final cleanup
 */
async function after(
  result: number,
  capabilities: any,
  specs: string[]
): Promise<void>;
```

---

## Step Definitions API (`src/steps/mcpServer.step.ts`)

### Cucumber Step Patterns

```typescript
// Given steps
Given(/^I am on the OBOT home page$/, async function() { /* ... */ });
Given(/^the MCP server "([^"]*)" is available$/, async function(serverName: string) { /* ... */ });

// When steps
When(/^I select the "([^"]*)" MCP server$/, async function(serverName: string) { /* ... */ });
When(/^I send the prompt "([^"]*)"$/, async function(prompt: string) { /* ... */ });
When(/^I execute the following prompts:$/, async function(dataTable: any) { /* ... */ });

// Then steps
Then(/^I should see a response within (\d+) seconds$/, async function(timeout: number) { /* ... */ });
Then(/^the response should contain "([^"]*)"$/, async function(expectedText: string) { /* ... */ });
Then(/^the tool "([^"]*)" should be called$/, async function(toolName: string) { /* ... */ });
Then(/^the task should be completed successfully$/, async function() { /* ... */ });
```

---

## Error Handling

### Common Error Types

```typescript
class ElementNotFoundError extends Error {
  constructor(selector: string) {
    super(`Element not found: ${selector}`);
  }
}

class TimeoutError extends Error {
  constructor(message: string, timeout: number) {
    super(`${message} (timeout: ${timeout}ms)`);
  }
}

class MCPServerError extends Error {
  constructor(serverName: string, details: string) {
    super(`MCP Server ${serverName} error: ${details}`);
  }
}

class AssertionError extends Error {
  constructor(message: string, expected: any, actual: any) {
    super(`${message}\nExpected: ${expected}\nActual: ${actual}`);
  }
}
```

---

## Related Documentation

- [MCP Catalog Schema](MCP_CATALOG_SCHEMA.md) - YAML structure reference
- [Automation Framework](AUTOMATION_FRAMEWORK.md) - Testing architecture
- [Code Style Guide](CODE_STYLE.md) - Coding conventions
- [Project Overview](PROJECT_OVERVIEW.md) - Architecture and design
