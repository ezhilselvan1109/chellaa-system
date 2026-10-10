# Notification Provider Abstraction Layer

Java SPI / Spring interface design decoupling channel implementations from business logic.

---

## 1. Provider Interface Definition

```java
public interface NotificationChannelAdapter {
    ChannelType getSupportedChannel();
    
    NotificationDispatchResult send(NotificationRequest request);
}

public record NotificationRequest(
    String messageId,
    String recipient,
    String locale,
    RenderedContent content,
    Map<String, String> metadata
) {}

public record NotificationDispatchResult(
    boolean success,
    String providerMessageId,
    String errorCode,
    String errorMessage,
    DeliveryStatus initialStatus
) {}
```
Concrete adapters (`SesEmailAdapter`, `TwilioSmsAdapter`, `MetaWhatsAppAdapter`, `FcmPushAdapter`) implement this interface, enabling seamless provider swapping via Spring profile configurations.
