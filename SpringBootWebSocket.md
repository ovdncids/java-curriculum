# Socket
* https://kouzie.github.io/spring/Spring-Boot-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-WebSocket
* https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo

## 설치
pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

build.gradle
```gradle
implementation group: 'org.springframework.boot', name: 'spring-boot-starter-websocket'
implementation 'org.projectlombok:lombok'
annotationProcessor 'org.projectlombok:lombok'
```
* `gradle`의 경우 `annotationProcessor`도 추가 시켜줘야 한다.

프로젝트Application.java
```java
@Bean
public ServerEndpointExporter serverEndpointExporter() {
    return new ServerEndpointExporter();
}
```

## Server
websocket/WebSocketServer.java
```java
@Log
@Component
@ServerEndpoint(value = "/websocket")
public class WebSocketServer {
    private static Set<Session> sessions = new CopyOnWriteArraySet<>();

    private static void sendMessage(Session session, String message) {
        try {
            session.getBasicRemote().sendText(message);
        } catch (IOException e) {
            log.warning(session.getId() + ", error: " + e.getMessage());
        }
    }

    @OnOpen
    public void onOpen(Session session) {
        sessions.add(session);
        log.info("onOpen: " + sessions.size());
        sendMessage(session, "Hi!");
    }

    @OnMessage
    public void onMessage(Session session, String message) {
        log.info(message);
    }
}
```

## Client
webSocketClient.html
```html
<script>
const ws = new WebSocket('ws://localhost:8080/websocket');

ws.onopen = function() {
  ws.send('Hello!');
};
    
ws.onmessage = function(event) {
  console.log(event.data);
};
</script>
```

## error, close
websocket/WebSocketServer.java
```java
// 통신 중 에러가 발생할 경우
@OnError
public void onError(Session session, Throwable throwable) {
    sessions.remove(session);
    log.warning("error: " + throwable.getMessage());
}

// server 또는 client 중 어느 하나가 통신을 끊는 경우
@OnClose
public void onClose(Session session) {
    sessions.remove(session);
    log.info("onClose: " + sessions.size());
}
```

webSocketClient.html
```html
// 통신 중 에러가 발생할 경우
ws.onerror = function(event) {
  console.error(event.error);
};

// server 또는 client 중 어느 하나가 통신을 끊는 경우
ws.onclose = function(event) {
  console.warn(event.code);
};
```

## Server broadcast
websocket/WebSocketServer.java
```java
private static void broadcast(Session _session, String message) {
    for (Session session : sessions) {
        if (session == _session) continue;
        sendMessage(session, message);
    }
}
```
```diff
@OnOpen
public void onOpen(Session session) {
    sessions.add(this);
    log.info("onOpen: " + sessions.size());
    sendMessage(session, "Hi!");
+   broadcast(session, "Halo! Others");
}
```

## Client에서 message 보내기
webSocketClient.html
```html
<form onsubmit="ws.send(this['message'].value); return false;">
  <input type="text" name="message">
</form>
```

websocket/WebSocketServer.java
```diff
- broadcast(session, "Halo! Others");
```
```diff
@OnMessage
public void onMessage(Session session, String message) {
    log.info(message);
+   broadcast(session, message);
}
```

## @Autowired service 연결
* ❕ `CustomSpringConfigurator` 적용 전에는 `@Autowired`로 서비스를 불러도 `null`을 반환 한다.

CustomSpringConfigurator.java
```java
@Configuration
public class CustomSpringConfigurator extends ServerEndpointConfig.Configurator implements ApplicationContextAware {

    private static volatile BeanFactory context;

    @Override
    public <T> T getEndpointInstance(Class<T> _class) {
        return context.getBean(_class);
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        CustomSpringConfigurator.context = applicationContext;
    }
}
```

websocket/WebSocketServer.java
```diff
- @ServerEndpoint(value = "/websocket")
+ @ServerEndpoint(value = "/websocket", configurator = CustomSpringConfigurator.class)
```
```java
@Autowired
private MembersService membersService;
```
