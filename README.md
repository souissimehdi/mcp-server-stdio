# mcpserverstdio

A help desk [MCP](https://modelcontextprotocol.io) server built with Spring Boot and Spring AI. It talks to MCP clients over **stdio** and stores tickets in a local H2 database.

## Tools

| Tool | Parameters | Description |
|---|---|---|
| `createTicket` | `ticketRequest` (`issue`, `username`) | Creates a support ticket and returns its id. |
| `getTicketStatus` | `username` | Returns the tickets of a given user. |

A ticket has an `id`, `username`, `issue`, `status` (e.g. `OPEN`, `IN_PROGRESS`, `CLOSED`), `createdAt` and `eta`.

## Stack

- Java 25
- Spring Boot 4.1.2
- Spring AI 2.0.1 (`spring-ai-starter-mcp-server`)
- Spring Data JPA + H2 (file database)
- Lombok

## Build

```bash
./mvnw clean package
```

`JAVA_HOME` must point to a JDK 25.

## Run

The server speaks JSON-RPC on stdin/stdout, so it is meant to be launched by an MCP client rather than used interactively:

```bash
java -jar target/mcpserverstdio-0.0.1-SNAPSHOT.jar
```

Logging is limited to `ERROR` and the banner is disabled in [application.properties](src/main/resources/application.properties), so nothing but protocol messages is written to stdout.

### Client configuration

Example for a client that uses the `mcpServers` format (e.g. Claude Desktop):

```json
{
  "mcpServers": {
    "helpdesk": {
      "command": "java",
      "args": ["-jar", "C:/path/to/mcpserverstdio/target/mcpserverstdio-0.0.1-SNAPSHOT.jar"]
    }
  }
}
```

## Configuration

Data is stored in an H2 file database at `~/mcpserverstdio` (created on first start, schema managed with `spring.jpa.hibernate.ddl-auto=update`). The datasource credentials in `application.properties` are local development values; change them if you reuse this project.

## Project layout

```
src/main/java/com/demo/mcpserverstdio
├── McpserverstdioApplication.java   entry point
├── tool/        MCP tools (HelpDeskTools)
├── service/     ticket business logic
├── repository/  Spring Data JPA repository
├── entity/      HelpDeskTicket JPA entity
└── model/       TicketRequest input record
```
