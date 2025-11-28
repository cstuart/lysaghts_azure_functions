# Azure Functions - Lysaght

A serverless Azure Function that extracts text from PDF and DOCX documents by downloading them from URLs.

## Features

- Extract text from PDF files using `pypdf`
- Extract text from DOCX files using `python-docx`
- Download documents from URLs
- RESTful HTTP API endpoint
- Serverless Azure Functions runtime

## API Endpoint

### POST `/api/doc_to_text`

Extracts text from a document at the provided URL.

**Request Body:**
```json
{
  "url": "https://example.com/document.pdf"
}
```

**Supported File Types:**
- PDF files (`.pdf`)
- Microsoft Word documents (`.docx`)

**Response:**
- **200 OK**: Returns the extracted text
- **400 Bad Request**: Invalid URL, unsupported file type, or download error

## Local Development Setup

### Prerequisites

- **Python 3.8+** (Azure Functions supports Python 3.8, 3.9, 3.10, and 3.11)
- **Azure Functions Core Tools** v4.x
- **Virtual environment** (recommended)

### Installation

1. **Clone the repository** (if applicable) or navigate to the project directory:
   ```bash
   cd azure_functions
   ```

2. **Create and activate a virtual environment:**
   ```bash
   # Create virtual environment
   python3 -m venv .venv
   
   # Activate virtual environment
   source .venv/bin/activate  # On macOS/Linux
   # or
   .venv\Scripts\activate     # On Windows
   ```

3. **Install Azure Functions Core Tools** (if not already installed):
   ```bash
   # Using npm
   npm install -g azure-functions-core-tools@4 --unsafe-perm true
   
   # Or using Homebrew on macOS
   brew tap azure/functions
   brew install azure-functions-core-tools@4
   ```

4. **Install Python dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

### Running Locally

1. **Start the Azure Functions runtime:**
   ```bash
   func start
   ```
   
   Or use the VS Code task:
   - Open Command Palette (`Cmd+Shift+P` on macOS)
   - Run "Tasks: Run Task"
   - Select "func: host start"

2. **The function will be available at:**
   ```
   http://localhost:7071/api/doc_to_text
   ```

### Testing the Function

You can test the function using curl, Postman, or any HTTP client:

```bash
curl -X POST http://localhost:7071/api/doc_to_text \
  -H "Content-Type: application/json" \
  -d '{"url": "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"}'
```

Requests to `localhost` can not be made in a docker containers, so instead to use `host.docker.internal`. For example:

```bash
http://host.docker.internal:7071/api/doc_to_text
```

### Key Files

- **`function_app.py`**: Contains the main Azure Function with the HTTP trigger that handles document text extraction
- **`host.json`**: Azure Functions host configuration with logging and extension bundle settings
- **`local.settings.json`**: Local environment settings for development
- **`requirements.txt`**: Python package dependencies

## Dependencies

- `azure-functions`: Azure Functions Python library
- `requests`: HTTP library for downloading documents
- `pypdf`: PDF text extraction library
- `python-docx`: Microsoft Word document text extraction library

## Development Tips

1. **Hot Reload**: The Azure Functions runtime supports hot reload during development. Changes to `function_app.py` will automatically restart the function.

2. **Logging**: Use the built-in logging module. Logs will appear in the terminal when running locally:
   ```python
   import logging
   logging.info('Your log message here')
   ```

3. **Environment Variables**: Add any additional configuration to `local.settings.json` under the `Values` section.

4. **Debugging**: You can attach a debugger to the running function process for step-through debugging.

## Deployment

To deploy to Azure:

1. **Login to Azure:**
   ```bash
   az login
   ```

2. **Deploy the function:**
   ```bash
   func azure functionapp publish <YOUR_FUNCTION_APP_NAME>
   ```

## Error Handling

The function includes error handling for:
- Invalid JSON in request body
- Missing URL parameter
- Network errors when downloading documents
- Unsupported file types
- Document parsing errors

## Security Notes

- The function is configured with `AuthLevel.ADMIN` for security
- Consider implementing additional validation for production use
- Be mindful of document size limits and processing timeouts