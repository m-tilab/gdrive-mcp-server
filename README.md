# gdrive-mcp-server

A **Google Drive MCP Server** built with [Spring AI MCP Server Boot Starter](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-server-boot-starter-docs.html).

Exposes Google Drive operations as MCP tools, making your Google Drive accessible to any MCP-compatible AI client (Claude Desktop, GitHub Copilot, Cursor, etc.).

---

## Tools

| Tool | Description |
|---|---|
| `list_files` | List files/folders, optionally filtered by parent folder |
| `search_files` | Search by name or full-text content |
| `get_file_info` | Get metadata for a specific file by ID |
| `read_file` | Read content of a Google Doc, Sheet, or text file |
| `list_folders` | List all folders or sub-folders within a parent |
| `create_document` | Create a new empty Google Doc |
| `create_folder` | Create a new folder |

---

## Prerequisites

- Java 21+
- Maven 3.9+
- A Google Cloud project with the **Google Drive API** enabled
- OAuth2 credentials (Client ID + Client Secret + Refresh Token)

### Getting OAuth2 credentials

1. Go to [Google Cloud Console](https://console.cloud.google.com) → **APIs & Services** → **Credentials**
2. Create an **OAuth 2.0 Client ID** (Desktop application)
3. Enable the **Google Drive API**
4. Use the [OAuth 2.0 Playground](https://developers.google.com/oauthplayground) to get a refresh token:
   - Select `Drive API v3` → `https://www.googleapis.com/auth/drive`
   - Exchange the auth code for tokens and copy the **Refresh Token**

---

## Configuration

Set the following environment variables:

```bash
export GOOGLE_CLIENT_ID=your-client-id
export GOOGLE_CLIENT_SECRET=your-client-secret
export GOOGLE_REFRESH_TOKEN=your-refresh-token
```

---

## Build & Run

```bash
mvn clean package -DskipTests
java -jar target/gdrive-mcp-server-0.0.1-SNAPSHOT.jar
```

---

## Connect to an MCP client

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "google-drive": {
      "command": "java",
      "args": ["-jar", "/path/to/gdrive-mcp-server-0.0.1-SNAPSHOT.jar"],
      "env": {
        "GOOGLE_CLIENT_ID": "your-client-id",
        "GOOGLE_CLIENT_SECRET": "your-client-secret",
        "GOOGLE_REFRESH_TOKEN": "your-refresh-token"
      }
    }
  }
}
```

### GitHub Copilot CLI / Cursor

Add to your MCP config file (e.g. `~/.cursor/mcp.json` or `~/.copilot/mcp.json`):

```json
{
  "mcpServers": {
    "google-drive": {
      "command": "java",
      "args": ["-jar", "/path/to/gdrive-mcp-server-0.0.1-SNAPSHOT.jar"],
      "env": {
        "GOOGLE_CLIENT_ID": "your-client-id",
        "GOOGLE_CLIENT_SECRET": "your-client-secret",
        "GOOGLE_REFRESH_TOKEN": "your-refresh-token"
      }
    }
  }
}
```

---

## Project Structure

```
src/main/java/io/github/mahditilab/gdrivemcp/
  GdriveMcpServerApplication.java   # Spring Boot entry point
  config/
    GoogleDriveConfig.java           # Google Drive API client setup (OAuth2)
  tools/
    GoogleDriveTools.java            # MCP tools (@Tool annotations)
src/main/resources/
  application.yml                    # MCP server config (STDIO transport)
```

---

## Tech Stack

- **Java 21**
- **Spring Boot 3.4**
- **Spring AI MCP Server Boot Starter** (`spring-ai-starter-mcp-server`) — STDIO transport
- **Google Drive API v3** (`google-api-services-drive`)
- **Google Auth Library** (`google-auth-library-oauth2-http`)
