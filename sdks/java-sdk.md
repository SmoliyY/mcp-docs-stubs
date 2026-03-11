# Java/Kotlin SDK

The official Java SDK for MCP provides server and client support for JVM-based applications.

## Installation

### Maven

```xml
<dependency>
  <groupId>io.modelcontextprotocol</groupId>
  <artifactId>mcp-java-sdk</artifactId>
  <version>0.10.0</version>
</dependency>
```

### Gradle

```kotlin
implementation("io.modelcontextprotocol:mcp-java-sdk:0.10.0")
```

## Spring Boot Integration

The SDK integrates natively with Spring Boot via the `spring-ai-mcp` module:

```java
@SpringBootApplication
public class McpServerApp {
    @Bean
    public ToolHandler weatherTool() {
        return new ToolHandler() {
            @Override
            public String getName() { return "get_weather"; }

            @Override
            public String getDescription() {
                return "Get weather for a city";
            }

            @Override
            public ToolResult call(Map<String, Object> args) {
                String city = (String) args.get("city");
                return ToolResult.text("Sunny, 25°C in " + city);
            }
        };
    }
}
```

## Transport Options

The Java SDK supports:
- **stdio** — For local integrations
- **Streamable HTTP** — Using embedded Servlet containers
- **WebSocket** — Additional transport option

## Kotlin DSL

```kotlin
val server = McpServer("weather-server") {
    tool("get_weather", "Get current weather") {
        parameter("city", "string", required = true)
        handler { args ->
            textResult("Weather in ${args["city"]}: Sunny")
        }
    }
}
```

## Repository

`github.com/modelcontextprotocol/java-sdk`
