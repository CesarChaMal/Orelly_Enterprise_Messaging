# CLAUDE.md - Enterprise Messaging with JMS

## Repository Overview

This repository contains educational code samples for O'Reilly's Enterprise Messaging courses:
- **Enterprise Messaging** (http://shop.oreilly.com/product/0636920034698.do)
- **Enterprise Messaging with JMS** (http://shop.oreilly.com/product/0636920034865.do)

The codebase demonstrates Java Message Service (JMS) concepts, patterns, and implementations using Apache ActiveMQ and Spring Framework.

## Project Structure

### Directory Organization

The repository is organized into **independent, self-contained modules**, each demonstrating specific JMS concepts:

```
Orelly_Enterprise_Messaging/
├── 01_04_JMS_1.1_Fundamentals_Code/          # JMS 1.1 basic sender/receiver
├── 01_05_JMS_2.0_Fundamentals_Code/          # JMS 2.0 simplified API
├── 01_06_JMS_2.0_New_Features_Code/          # Async send, shared subs, delivery count
├── 01_07_Message_Headers_Code/               # Message header manipulation
├── 01_10_Request_Reply_Code/                 # Request-reply pattern
├── 01_11_Pub_Sub_Code/                       # Publish-subscribe messaging
├── 01_12_Message_Filtering_Code/             # Message selectors
├── 01_13_Acknowledgement_Modes_Code/         # AUTO, CLIENT, DUPS_OK modes
├── 02_02_Transacted_Sessions_Code/           # Transaction management
├── 02_03_Embedded_Broker_Code/               # Embedded ActiveMQ broker
├── 02_04_Browsing_Queues_Code/               # Queue browsing, metadata
├── 02_05_Sending_Documents_Code/             # Binary message handling
├── 02_06_Restful_JMS_Code/                   # REST API over JMS
├── 02_07_Performance_Code/                   # Performance testing, concurrency
├── 02_08_Spring_JMS_Intro_Code/              # Spring JMS setup
├── 02_09_Spring_JMSTemplate_Code/            # Spring JmsTemplate usage
├── 02_10_Spring_MDP_Code/                    # Message-Driven POJOs
├── apache-activemq-5.15.10_libs/             # ActiveMQ JAR dependencies
└── openmq_libs/                              # OpenMQ JAR dependencies
```

### Module Structure Pattern

Each module follows a consistent structure:

```
XX_YY_Module_Name_Code/
├── src/
│   └── main/
│       ├── java/
│       │   └── oreilly/video/messaging/
│       │       ├── JMSSender.java          # Message producer
│       │       ├── JMSReceiver.java        # Message consumer
│       │       └── [Other classes]
│       └── resources/
│           ├── jndi.properties             # JNDI configuration
│           ├── activemq.xml                # Broker configuration (if needed)
│           ├── log4j.xml                   # Logging configuration
│           └── app-context.xml             # Spring config (Spring modules only)
└── README.txt                              # Module-specific setup instructions
```

## Technology Stack

### Core Technologies
- **Java**: JDK 8+ (uses javax.jms package)
- **JMS**: Java Message Service 1.1 and 2.0 APIs
- **Apache ActiveMQ**: 5.15.10 (primary message broker)
- **OpenMQ**: Alternative JMS provider (imq.jar)
- **Spring Framework**: 4.0+ (Spring JMS modules)
- **SLF4J/Log4j**: Logging framework

### Key Dependencies

Located in `apache-activemq-5.15.10_libs/`:
- `activemq-client-5.15.10.jar` - ActiveMQ client library
- `jms-1.1.jar` - JMS API
- `hawtbuf-1.11.jar` - ActiveMQ serialization
- `geronimo-j2ee-management_1.1_spec-1.0.1.jar` - J2EE management spec
- `commons-logging-1.2.1.1.jar` - Commons logging
- `log4j-1.2.17.jar` - Log4j logging
- `slf4j-api-1.7.25.jar`, `slf4j-log4j12-1.7.25.jar` - SLF4J logging

Located in `openmq_libs/`:
- `imq.jar` - OpenMQ client
- `jms.jar` - JMS API

## Build System

### Important: No Standard Build Tool

This repository **does not use Maven, Gradle, or Ant**. Each module is designed to be:
1. Imported into an IDE (IntelliJ IDEA, Eclipse, NetBeans)
2. Dependencies manually added to classpath
3. Run as standalone Java applications with `main()` methods

### IDE Setup

**IntelliJ IDEA** (`.iml` files present):
1. Open module directory as project
2. Add JARs from `apache-activemq-5.15.10_libs/` to project libraries
3. Mark `src/main/java` as sources root
4. Mark `src/main/resources` as resources root
5. Run any class with `main()` method

**Eclipse**:
1. Import as existing Java project
2. Add external JARs from `apache-activemq-5.15.10_libs/`
3. Configure build path
4. Run as Java Application

## Common Patterns and Conventions

### Package Structure
All code uses the package: `oreilly.video.messaging`

### Naming Conventions

| Pattern | Purpose | Example |
|---------|---------|---------|
| `JMSSender` | JMS 1.1 message producer | Point-to-point sender |
| `JMSReceiver` | JMS 1.1 message consumer | Synchronous receiver |
| `JMSAsyncReceiver` | JMS 1.1 async consumer | MessageListener-based |
| `JMS2Sender` | JMS 2.0 message producer | Simplified API sender |
| `JMS2Receiver` | JMS 2.0 message consumer | JMSContext-based |
| `JMSPublisher` | Pub/sub message producer | Topic publisher |
| `JMSSubscriber` | Pub/sub message consumer | Topic subscriber |
| `Bootstrap` | Broker initialization | Embedded broker startup |
| `TradeProcessor` | Business logic handler | Spring MDP listener |

### Connection Patterns

**JMS 1.1 Pattern** (verbose):
```java
// Direct connection
ActiveMQConnectionFactory cf = new ActiveMQConnectionFactory("tcp://localhost:61616");
Connection connection = cf.createConnection();
connection.start();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
Queue queue = session.createQueue("TRADE.Q");
```

**JMS 1.1 with JNDI** (container-managed):
```java
Context ctx = new InitialContext();
ConnectionFactory cf = (ConnectionFactory) ctx.lookup("ConnectionFactory");
Connection connection = cf.createConnection();
connection.start();
Queue queue = (Queue) ctx.lookup("TRADE.Q");
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
```

**JMS 2.0 Pattern** (simplified):
```java
ActiveMQConnectionFactory cf = new ActiveMQConnectionFactory("tcp://localhost:61616");
JMSContext context = cf.createContext();
Queue queue = context.createQueue("TRADE.Q");
```

### JNDI Configuration

Standard `jndi.properties` pattern:
```properties
java.naming.factory.initial = org.apache.activemq.jndi.ActiveMQInitialContextFactory
java.naming.provider.url = tcp://localhost:61616
java.naming.security.principal = system
java.naming.security.credentials = manager

connectionFactoryNames = ConnectionFactory
queue.TRADE.Q = TRADE.Q
topic.TRADE.T = TRADE.T
```

### Spring Configuration

**Spring Bean Configuration** (`app-context.xml`):
- JNDI template for connection factory lookup
- CachingConnectionFactory for connection pooling
- JndiDestinationResolver for queue/topic lookup
- JmsTemplate or MessageListenerContainer for messaging

### Message Types
- `TextMessage` - String content (most common in examples)
- `BytesMessage` - Binary data (document sending)
- `ObjectMessage` - Serialized Java objects
- `MapMessage` - Key-value pairs
- `StreamMessage` - Stream of primitives

## Development Workflow

### Running Examples

1. **Start ActiveMQ Broker** (unless using embedded broker):
   ```bash
   # Download and extract ActiveMQ 5.15.10
   cd apache-activemq-5.15.10/bin
   ./activemq start
   ```
   Default URL: `tcp://localhost:61616`
   Web Console: `http://localhost:8161` (admin/admin)

2. **Run Sender/Receiver Pairs**:
   - Each module typically has a sender and receiver
   - Run receiver FIRST (it waits for messages)
   - Run sender SECOND (sends message and exits)
   - Observe console output in both

3. **Module-Specific Setup**:
   - Always read the `README.txt` in each module
   - Some modules require additional configuration
   - RESTful JMS requires copying webapps and jetty.xml

### Common Connection URLs

- **TCP**: `tcp://localhost:61616` (standard)
- **Embedded VM**: `vm://localhost` (in-process)
- **Embedded TCP**: `tcp://localhost:61888` (embedded broker)

### Testing Strategy

**Manual Testing**:
- Examples are designed for manual execution
- Run each class as standalone Java application
- Verify output in console
- Check ActiveMQ web console for queue/topic stats

**Performance Testing**:
- See `02_07_Performance_Code/ConcurrentTest.java`
- Tests concurrent producers/consumers
- Uses TradeProcessor for message handling

## Key Concepts by Module

### JMS 1.1 vs JMS 2.0
- **JMS 1.1**: Verbose API with Connection → Session → Producer/Consumer chain
- **JMS 2.0**: Simplified with JMSContext (auto-closeable), method chaining

### Messaging Patterns
- **Point-to-Point**: Queue-based, single consumer per message
- **Publish-Subscribe**: Topic-based, multiple subscribers
- **Request-Reply**: Correlation ID + temporary queues

### Delivery Guarantees
- **AUTO_ACKNOWLEDGE**: Message acknowledged automatically
- **CLIENT_ACKNOWLEDGE**: Manual acknowledgment required
- **DUPS_OK_ACKNOWLEDGE**: Lazy acknowledgment, allows duplicates
- **Transacted Sessions**: All-or-nothing delivery

### Advanced Features
- **Message Selectors**: SQL-like filtering on message properties
- **Shared Subscriptions**: JMS 2.0 feature for load balancing on topics
- **Asynchronous Send**: JMS 2.0 non-blocking sends with callbacks
- **Queue Browsing**: Peek at messages without consuming
- **Embedded Broker**: Run ActiveMQ in-process

### Spring Integration
- **JmsTemplate**: Synchronous send/receive operations
- **Message-Driven POJOs (MDP)**: POJO listeners with container management
- **Connection Caching**: Pooling for performance

## AI Assistant Guidelines

### When Analyzing This Codebase

1. **Module Independence**: Each module is INDEPENDENT. Don't expect shared dependencies or cross-module imports.

2. **No Build Files**: Don't look for `pom.xml`, `build.gradle`, or `build.xml`. Manual dependency management is intentional.

3. **Execution Model**: All classes are runnable via `main()` methods. No application servers or complex deployment.

4. **Configuration First**: Always check `jndi.properties`, `activemq.xml`, and `app-context.xml` before analyzing Java code.

5. **Version Awareness**:
   - `JMS*.java` classes use JMS 1.1 API
   - `JMS2*.java` classes use JMS 2.0 API
   - Spring examples use Spring 4.0+

### When Making Changes

1. **Preserve Module Isolation**: Changes to one module should NOT affect others.

2. **Maintain Patterns**:
   - Keep JNDI configuration in `jndi.properties`
   - Keep logging config in `log4j.xml`
   - Follow existing naming conventions

3. **Connection URL Consistency**:
   - Default: `tcp://localhost:61616`
   - Document any changes to connection URLs
   - Check all files in module if changing URL

4. **Resource Cleanup**:
   - Always close connections, sessions, contexts
   - JMS 2.0 use try-with-resources for JMSContext
   - JMS 1.1 use explicit `connection.close()`

5. **Error Handling**:
   - Examples use simple try-catch with printStackTrace()
   - Production code should use proper logging
   - Don't over-complicate example error handling

### When Adding New Examples

1. **Create New Module**: Don't modify existing modules
2. **Follow Naming**: `XX_YY_Concept_Name_Code/`
3. **Include README.txt**: Document setup and dependencies
4. **Add JNDI Properties**: Even if using direct connections
5. **Test Both APIs**: Consider both JMS 1.1 and 2.0 versions

### Common Pitfalls

1. **Broker Not Running**: Most examples require ActiveMQ running (except embedded broker)
2. **Classpath Issues**: All JARs from `apache-activemq-5.15.10_libs/` must be on classpath
3. **Run Order**: Receivers must be started before senders in request-reply scenarios
4. **Connection URL Mismatch**: jndi.properties and Java code must match
5. **Spring Context**: Spring examples need all XSD files in resources

### Code Style Conventions

- **Indentation**: Tabs (not spaces)
- **Comments**: Minimal - code is self-documenting
- **Package**: Always `oreilly.video.messaging`
- **Imports**: Star imports generally avoided
- **Exception Handling**: Simple printStackTrace() for demos
- **Main Method**: Always at bottom of class

### Resource Files

- `jndi.properties`: JNDI naming configuration
- `activemq.xml`: Broker configuration (persistence, plugins)
- `log4j.xml`: Logging levels and appenders
- `app-context.xml`: Spring bean definitions

### Testing Checklist

Before committing changes:
- [ ] Broker is running (if needed)
- [ ] All required JARs on classpath
- [ ] Receiver starts successfully
- [ ] Sender connects and sends
- [ ] Messages appear in ActiveMQ console
- [ ] Console output is clear and helpful
- [ ] No connection leaks (resources closed)
- [ ] README.txt updated if setup changed

## Useful Commands

### ActiveMQ Management

```bash
# Start broker
./activemq start

# Stop broker
./activemq stop

# Check status
./activemq status

# View logs
tail -f data/activemq.log
```

### Queue/Topic Inspection

Access web console: `http://localhost:8161/admin`
- View queues, topics, connections
- Purge queues
- Send test messages
- Browse messages

## Additional Resources

- **JMS 1.1 Specification**: https://jcp.org/en/jsr/detail?id=914
- **JMS 2.0 Specification**: https://jcp.org/en/jsr/detail?id=343
- **ActiveMQ Documentation**: https://activemq.apache.org/components/classic/documentation
- **Spring JMS Documentation**: https://docs.spring.io/spring-framework/docs/4.0.x/spring-framework-reference/html/jms.html

## Version Information

- **JMS API**: 1.1 and 2.0
- **ActiveMQ**: 5.15.10
- **Spring Framework**: 4.0.x
- **Java**: 8+
- **Log4j**: 1.2.17
- **SLF4J**: 1.7.25

## Contact and Support

For issues with the course materials or code examples, refer to:
- O'Reilly Media course pages
- ActiveMQ user mailing lists
- Spring Framework forums

---

**Last Updated**: 2025-11-13
**Maintained For**: Educational purposes - O'Reilly Enterprise Messaging courses
