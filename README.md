# Agentic AI in Software Testing

A comprehensive guide to using Agentic AI in software testing with practical examples and detailed documentation.

## Table of Contents

- [Introduction](#introduction)
- [What is Agentic AI?](#what-is-agentic-ai)
- [Why Use Agentic AI in Testing?](#why-use-agentic-ai-in-testing)
- [Getting Started](#getting-started)
- [Examples](#examples)
  - [Example 1: Automated Test Case Generation](#example-1-automated-test-case-generation)
  - [Example 2: Intelligent Bug Detection](#example-2-intelligent-bug-detection)
  - [Example 3: Self-Healing Tests](#example-3-self-healing-tests)
- [Best Practices](#best-practices)
- [Contributing](#contributing)
- [License](#license)

## Introduction

Agentic AI refers to AI systems that can autonomously perform tasks, make decisions, and adapt to changing environments. In software testing, agentic AI can revolutionize how we approach quality assurance by automating complex testing scenarios, generating test cases, and identifying bugs more intelligently.

## What is Agentic AI?

Agentic AI systems possess several key characteristics:

- **Autonomy**: Can operate independently without constant human intervention
- **Goal-oriented**: Works towards specific objectives
- **Adaptive**: Learns from experience and adjusts behavior
- **Proactive**: Anticipates problems and takes preventive action
- **Context-aware**: Understands the environment and makes informed decisions

## Why Use Agentic AI in Testing?

1. **Increased Test Coverage**: AI agents can explore edge cases that humans might miss
2. **Faster Testing Cycles**: Automated test generation and execution
3. **Reduced Maintenance**: Self-healing tests adapt to UI changes
4. **Better Bug Detection**: Pattern recognition identifies subtle issues
5. **Cost Efficiency**: Reduces manual testing effort and time

## Getting Started

### Prerequisites

```bash
python >= 3.8
pip install openai pytest selenium
```

### Installation

```bash
git clone https://github.com/gauresh-bane/agentic-ai-software-testing.git
cd agentic-ai-software-testing
pip install -r requirements.txt
```

## Examples

### Example 1: Automated Test Case Generation

This example demonstrates how an AI agent can automatically generate test cases for a given function.

```python
# test_generator.py
import openai
import os

class TestCaseGenerator:
    def __init__(self, api_key):
        openai.api_key = api_key
    
    def generate_test_cases(self, function_code, function_description):
        """
        Generate test cases for a given function using AI
        """
        prompt = f"""
        Given the following function:
        
        {function_code}
        
        Description: {function_description}
        
        Generate comprehensive test cases including:
        1. Happy path tests
        2. Edge cases
        3. Error handling tests
        4. Boundary value tests
        
        Return test cases in pytest format.
        """
        
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.7
        )
        
        return response.choices[0].message.content

# Example usage
if __name__ == "__main__":
    generator = TestCaseGenerator(os.getenv("OPENAI_API_KEY"))
    
    function_code = """
    def calculate_discount(price, discount_percentage):
        if discount_percentage < 0 or discount_percentage > 100:
            raise ValueError("Discount must be between 0 and 100")
        return price * (1 - discount_percentage / 100)
    """
    
    description = "Calculates final price after applying discount percentage"
    test_cases = generator.generate_test_cases(function_code, description)
    print(test_cases)
```

### Example 2: Intelligent Bug Detection

An AI agent that analyzes code and test results to identify potential bugs.

```python
# bug_detector.py
import openai
from typing import List, Dict

class IntelligentBugDetector:
    def __init__(self, api_key):
        openai.api_key = api_key
    
    def analyze_test_failures(self, test_results: List[Dict], source_code: str):
        """
        Analyze test failures and provide intelligent insights
        """
        failures_summary = "\n".join([
            f"Test: {r['name']}\nError: {r['error']}\nStack: {r['stack']}"
            for r in test_results if r['status'] == 'failed'
        ])
        
        prompt = f"""
        Analyze the following test failures and source code to identify:
        1. Root cause of failures
        2. Potential bugs in the code
        3. Suggested fixes
        4. Whether failures are related
        
        Test Failures:
        {failures_summary}
        
        Source Code:
        {source_code}
        
        Provide actionable insights and recommendations.
        """
        
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3
        )
        
        return response.choices[0].message.content
    
    def suggest_test_improvements(self, test_code: str, coverage_report: Dict):
        """
        Suggest improvements to test suite based on coverage
        """
        prompt = f"""
        Based on the test code and coverage report:
        - Coverage: {coverage_report['percentage']}%
        - Uncovered lines: {coverage_report['uncovered_lines']}
        
        Test Code:
        {test_code}
        
        Suggest:
        1. Missing test scenarios
        2. Areas to improve test coverage
        3. Potential edge cases not covered
        """
        
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}]
        )
        
        return response.choices[0].message.content
```

### Example 3: Self-Healing Tests

AI-powered tests that can adapt to UI changes automatically.

```python
# self_healing_test.py
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.common.exceptions import NoSuchElementException
import openai
import json

class SelfHealingTest:
    def __init__(self, driver, api_key):
        self.driver = driver
        openai.api_key = api_key
        self.locator_history = {}
    
    def find_element_with_healing(self, locator_type, locator_value, context=""):
        """
        Find element with self-healing capability
        """
        try:
            # Try original locator
            element = self.driver.find_element(locator_type, locator_value)
            return element
        except NoSuchElementException:
            # Element not found, try to heal
            print(f"Element not found: {locator_value}. Attempting to heal...")
            new_locator = self._heal_locator(locator_type, locator_value, context)
            
            if new_locator:
                element = self.driver.find_element(
                    new_locator['type'], 
                    new_locator['value']
                )
                # Update locator history
                self.locator_history[locator_value] = new_locator
                return element
            else:
                raise NoSuchElementException(f"Could not heal locator: {locator_value}")
    
    def _heal_locator(self, locator_type, locator_value, context):
        """
        Use AI to find alternative locator
        """
        page_source = self.driver.page_source
        
        prompt = f"""
        The following locator is not working:
        Type: {locator_type}
        Value: {locator_value}
        Context: {context}
        
        Analyze the page source and suggest an alternative locator:
        {page_source[:2000]}  # First 2000 chars
        
        Return JSON with: {{"type": "BY_XPATH/BY_ID/BY_CSS", "value": "new_locator"}}
        """
        
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.2
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except:
            return None

# Usage example
if __name__ == "__main__":
    driver = webdriver.Chrome()
    healer = SelfHealingTest(driver, os.getenv("OPENAI_API_KEY"))
    
    driver.get("https://example.com")
    
    # This will automatically heal if the button ID changes
    submit_button = healer.find_element_with_healing(
        By.ID, 
        "submit-btn",
        context="Submit button for login form"
    )
    submit_button.click()
```

## Best Practices

### 1. Start Small
- Begin with simple use cases like test case generation
- Gradually expand to more complex scenarios
- Monitor AI agent behavior closely initially

### 2. Human Oversight
- Always review AI-generated test cases
- Validate AI suggestions before implementation
- Keep humans in the loop for critical decisions

### 3. Clear Context
- Provide detailed context to AI agents
- Use descriptive variable names and comments
- Document the purpose of each test

### 4. Iterative Improvement
- Collect feedback on AI performance
- Fine-tune prompts based on results
- Continuously update your AI models

### 5. Security & Privacy
- Never expose sensitive data to AI models
- Use environment variables for API keys
- Follow your organization's data governance policies

### 6. Cost Management
- Monitor API usage and costs
- Cache AI responses when appropriate
- Use appropriate model tiers for different tasks

### 7. Version Control
- Track AI-generated code changes
- Document AI assistance in commit messages
- Review AI contributions like any other code

## Architecture

```
agentic-ai-software-testing/
├── examples/
│   ├── test_generator.py       # Automated test case generation
│   ├── bug_detector.py          # Intelligent bug detection
│   └── self_healing_test.py     # Self-healing test framework
├── utils/
│   ├── ai_client.py             # OpenAI client wrapper
│   └── test_helpers.py          # Testing utility functions
├── tests/
│   ├── test_examples.py         # Tests for examples
│   └── integration_tests.py     # Integration tests
├── docs/
│   ├── setup.md                 # Detailed setup guide
│   ├── api_reference.md         # API documentation
│   └── best_practices.md        # Extended best practices
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment variables template
└── README.md                    # This file
```

## Use Cases

### 1. Regression Testing
- Automatically detect breaking changes
- Generate regression test suites
- Prioritize tests based on risk

### 2. Exploratory Testing
- AI agents explore application flows
- Discover edge cases automatically
- Report unexpected behaviors

### 3. Performance Testing
- Analyze performance bottlenecks
- Generate load test scenarios
- Suggest optimization strategies

### 4. API Testing
- Auto-generate API test cases
- Validate response schemas
- Test error handling scenarios

## Contributing

We welcome contributions! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Make your changes** and add tests
4. **Run tests**: `pytest tests/`
5. **Commit your changes**: `git commit -m 'Add amazing feature'`
6. **Push to branch**: `git push origin feature/amazing-feature`
7. **Open a Pull Request**

### Guidelines
- Follow PEP 8 style guidelines
- Add docstrings to all functions
- Include unit tests for new features
- Update documentation as needed

## Resources

- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Selenium Documentation](https://www.selenium.dev/documentation/)
- [Pytest Documentation](https://docs.pytest.org/)
- [Software Testing Best Practices](https://martinfowler.com/testing/)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

If you have questions or need help:
- Open an issue on GitHub
- Check existing issues for solutions
- Review the documentation in `/docs`

## Acknowledgments

- Thanks to the OpenAI team for their powerful API
- Inspired by the testing community's innovation
- Built with contributions from developers worldwide

---

**Note**: This project is for educational purposes. Always review AI-generated code before using it in production environments.
