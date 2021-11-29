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
    public void onMessage(String message) {
        log.info(message);
    }
}
```
