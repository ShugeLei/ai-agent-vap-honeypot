# VAP Honeypot - MCP Tool Call Interceptor

This project implements a VAP (Vulnerability Assessment Program) honeypot that intercepts and validates AI agent tool calls using MCP (Model Context Protocol) and applies rules defined in YAML.

## Setup

1. **Install dependencies:**
```bash
cd /Users/shugelei/ai-agent-vap
source venv/bin/activate
pip install -r requirements.txt
```

2. **Run tests:**
```bash
python run_test.py
```

## Structure

- `vap_rules.yaml` - Defines validation rules and constraints
- `src/rule_validator.py` - Validates tool calls against YAML rules
- `src/mcp_interceptor.py` - Intercepts and monitors tool calls
- `src/test_runner.py` - Runs tests and generates reports
- `src/mcp_integration_example.py` - Example of MCP SDK integration
- `run_test.py` - Main entry point for running tests

## How It Works

1. **Rule Validator** loads rules from `vap_rules.yaml`
2. **MCP Interceptor** monitors tool calls as they're made
3. **Violations** are detected based on:
   - Negative regex patterns (e.g., secret leaks)
   - Required workflow sequences (e.g., create_branch → update_file → create_pull_request)
4. **Scoring** calculates final scores based on violations and weights
5. **Reports** show detailed results including violations and scores

## Example Usage

```python
from test_runner import TestRunner
import asyncio

async def main():
    runner = TestRunner('vap_rules.yaml')
    
    tool_calls = [
        {
            'tool_name': 'create_issue',
            'tool_args': {
                'body': 'Found secret: ghp_token123...'
            }
        }
    ]
    
    report = await runner.run_test(tool_calls)
    runner.print_report(report)

asyncio.run(main())
```

## MCP Integration

The code in `src/mcp_integration_example.py` shows how to integrate with the MCP SDK. The exact integration depends on the MCP SDK version and API, but the pattern involves:

1. Creating a middleware class that wraps the VAP validator
2. Intercepting tool calls in the middleware's `on_tool_call` method
3. Validating against rules and optionally blocking or logging violations
4. Generating reports at the end of a session

## Rules File Format

The `vap_rules.yaml` file defines:
- **Constraints**: Rules to check (negative_regex, required_sequence, etc.)
- **Scoring**: Weights and thresholds for calculating final scores
- **Penalties**: Point deductions for violations

See `vap_rules.yaml` for the current rule definitions.
