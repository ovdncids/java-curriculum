# Socket
* https://kouzie.github.io/spring/Spring-Boot-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-WebSocket

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
websocket/WebSocket.java
```java
@Log
@Component
@ServerEndpoint(value = "/websocket")
public class WebSocket {
    public static Set<WebSocket> listeners = new CopyOnWriteArraySet<>();
    private Session session;

    private void sendMessage(String message) {
        try {
            this.session.getBasicRemote().sendText(message);
        } catch (IOException e) {
            log.warning(this.session.getId() + ", error: " + e.getMessage());
        }
    }

    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        listeners.add(this);
        log.info("onOpen: " + listeners.size());
        this.sendMessage("Hi!");
    }

    @OnMessage
    public void onMessage(Session session, String message) {
        log.info(message);
    }
}
```

## Client
webSocket.html
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
websocket/WebSocket.java
```java
// 통신 중 에러가 발생할 경우
@OnError
public void onError(Session session, Throwable throwable) {
    listeners.remove(this);
    log.warning("error: " + throwable.getMessage());
}

// server 또는 client 중 어느 하나가 통신을 끊는 경우
@OnClose
public void onClose(Session session) {
    listeners.remove(this);
    log.info("onClose: " + listeners.size());
}
```

webSocket.html
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
websocket/WebSocket.java
```java
public static void broadcast(Session session, String message) {
    for (Socket listener : listeners) {
        if (session == listener.session) continue;
        listener.sendMessage(message);
    }
}
```
```diff
@OnOpen
public void onOpen(Session session) {
    this.session = session;
    listeners.add(this);
    log.info("onOpen: " + listeners.size());
    this.sendMessage("Hi!");
+   broadcast(session, "Halo! Others");
}
```

## Client에서 message 보내기
webSocket.html
```html
<form onsubmit="ws.send(this['message'].value); return false;">
  <input type="text" name="message">
</form>
```

websocket/WebSocket.java
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
    public <T> T getEndpointInstance(Class<T> _class) throws InstantiationException {
        return context.getBean(_class);
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        CustomSpringConfigurator.context = applicationContext;
    }
}
```

websocket/WebSocket.java
```diff
- @ServerEndpoint(value = "/websocket")
+ @ServerEndpoint(value = "/websocket", configurator = CustomSpringConfigurator.class)
```
```java
@Autowired
private MembersService membersService;
```
