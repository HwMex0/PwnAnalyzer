# PwnAnalyzer

PwnAnalyzer is a Python-based exploitation detection tool designed to scan log files for patterns indicative of known vulnerabilities or exploitation attempts. It uses a configurable template system to define patterns, severity levels, and actions to take upon detecting matches.

## Features

- **Pattern Matching:** Uses regular expressions to detect patterns in log files.
- **Contextual Output:** Optionally provides contextual lines around the matched patterns.
- **Alerts:** Supports customizable alert messages for detected patterns.
- **Logging:** Logs detailed information about detections and actions taken.
- **File Hashing:** Computes file hashes before and after scanning to ensure file integrity.

## Requirements

- Python 3.6 or higher
- `colorama` package for colored terminal output

Install the required package using:

```bash
pip install colorama
```

## Usage

To run PwnAnalyzer, you need to provide a path to a JSON template file or a directory containing multiple JSON template files. Each JSON file defines templates and search tasks.

### Command Line Arguments

- `-t`, `--template`: Path to the JSON template file or directory containing JSON files (required).
- `-c`, `--context`: Enable context printing around matched patterns (optional).

### Example Commands

Run with a single template file:

```bash
python PwnAnalyzer.py -t path/to/template.json
```

Run with a directory of template files:

```bash
python PwnAnalyzer.py -t path/to/templates_directory
```

Enable context printing:

```bash
python PwnAnalyzer.py -t path/to/template.json -c
```

### Example template File

Here is an example of a template file (`template.json`):

```json
{
  "templates": [
    {
      "name": "CVE-2023-33246 Detection",
      "description": "Detects exploitation attempts of CVE-2023-33246 in broker.log.",
      "tags": ["CVE-2023-33246", "broker", "exploitation"],
      "search_tasks": [
        {
          "file_path": "broker.log",
          "patterns": [
            {
              "pattern": "Broker receive request to update config, caller address=.*",
              "case_sensitive": false,
              "severity": "high",
              "context_lines": 2,
              "actions": [
                {
                  "type": "alert",
                  "message": "Suspicious configuration update detected."
                }
              ]
            },
            {
              "pattern": "updateBrokerConfig, new config: .*rocketmqHome=.*",
              "case_sensitive": false,
              "severity": "medium",
              "context_lines": 3,
              "actions": [
                {
                  "type": "alert",
                  "message": "Potential exploitation of CVE-2023-33246 detected."
                }
              ]
            },
            {
              "pattern": "rocketmqHome=.*(\\$@\\|sh|curl|wget).*",
              "case_sensitive": false,
              "severity": "critical",
              "context_lines": 5,
              "actions": [
                {
                  "type": "alert",
                  "message": "Command injection attempt detected."
                }
              ]
            },
            {
              "pattern": "Replace, key: listenPort, value: .* -> .*",
              "case_sensitive": false,
              "severity": "low",
              "context_lines": 1,
              "actions": []
            }
          ]
        }
      ],
      "log_file": "dfir_search.log"
    }
  ]
}
```

## How It Works

1. **Load template:**
   - The script loads the specified JSON template file(s) and validates the structure.

2. **Search Tasks:**
   - For each template, the script performs search tasks on the specified log files.

3. **Pattern Matching:**
   - The script uses regular expressions to find matches in the log files.

4. **Actions and Alerts:**
   - When a pattern is matched, the script executes defined actions, such as printing alerts.

5. **Logging:**
   - Detailed information about the search and detected patterns is logged to a specified log file.

## Contributions

Contributions are welcome! Feel free to submit a pull request or open an issue to discuss potential improvements or report bugs.

## License

PwnAnalyzer is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.