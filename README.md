# 🚀 SparkVibe Activity Hub – Low-Level Design (LLD)

---

## 📌 1. Problem Statement

**SparkVibe Activity Hub** is a mobile-first platform designed for college students (aged 18–24) to manage campus events, study sessions, personal reminders, and sponsored ads. Unlike isolated tools like Google Calendar or noisy platforms like Facebook Events, SparkVibe focuses on:

- ✅ **Trust:** Events must be validated with references (e.g., QR codes, URLs).
- ✅ **Engagement:** Unified calendar view, intuitive registration, and real-time notifications.
- ✅ **Community:** Subscriptions, feedback, and structured advertisements.

---

## 🎭 2. Use Case Diagram

### 👥 Actors:
- `Student`
- `Organizer`
- `Business (Ad Sponsor)`
- `System` (Validation/Notification Engine)

### 🧭 Use Cases:
- View Calendar  
- Register for Event  
- Subscribe/Unsubscribe to Event or Organizer  
- Provide Feedback  
- Create/Update Event  
- Add Reference to Event  
- Create Ad  
- Track Ad Views  
- Validate Event References  
- Send Notifications  

---

## 🧱 3. Class Diagram

### 🧍‍♂️ Core Entities:
- `User` (Abstract)  
  - `Student`  
  - `Organizer`  
  - `Business`  

- `Event`  
  - `StudySessionEvent`  
  - `FestivalEvent`  
  - `ReminderEvent`  

- `Ad`  
- `CalendarSlot`  
- `Subscription`  
- `Comment`  

### 🧩 Abstractions:
- `IReferenceValidator`  
  - `URLValidator`  
  - `QRCodeValidator`  

- `INotificationChannel`  
  - `EmailChannel`  
  - `PushChannel`  

### ⚙️ Services:
- `EventService`  
- `NotificationService`  
- `ValidationService`  
- `CalendarService`  
- `AdService`  
- `CommentService`  
- `SubscriptionService`  
- `TrackingService`  

---

## 🧠 4. Interfaces and Abstraction

### ✅ IReferenceValidator
```ts
interface IReferenceValidator {
  isValid(reference: Reference): boolean;
}

---

## 🔧 5. Core Method Logic (Pseudo-code)

### 🟢 Registering for an Event
```python
def register_student(event_id, student_id):
    if RegistrationRepository.exists(event_id, student_id):
        raise DuplicateRegistrationException()
    
    event = EventRepository.get(event_id)
    student = StudentRepository.get(student_id)
    
    if not ValidationService.validateReference(event.reference):
        raise InvalidReferenceException()

    event.attendees.append(student)
    NotificationService.sendNotification(student, f"You are registered for {event.title}")
```

### 🟡 Highlight Calendar Changes
```python
def highlightChanges(calendarSlots, lastViewed):
    for slot in calendarSlots:
        for event in slot.events:
            if event.updatedAt > lastViewed:
                event.highlight = True
    return calendarSlots
```

---

## 📲 6. Feature Flow – Register for Event & Receive Notification

### 🔄 Steps:
1. Student selects **“Attend This Event”** in the UI.
2. `EventService.registerUserForEvent(student, event)` is triggered.
3. ValidationService checks the event’s reference.
4. EventService checks for duplicate registration.
5. Student is added to the attendee list.
6. NotificationService sends confirmation to the student.

### ⚠️ Error Handling:
- `InvalidReferenceException`: Invalid or untrusted event link.
- `DuplicateRegistrationException`: Student is already registered.

---

## 🧩 7. Extensibility – Add Event Comments

### 💬 New Class: `Comment`
```ts
class Comment {
  id: UUID
  content: string
  user_id: UUID
  event_id: UUID
  timestamp: DateTime
}
```

### ⚙️ New Service: `CommentService`
```ts
class CommentService {
  def addComment(user_id, event_id, content):
      comment = Comment(user_id, event_id, content, now())
      CommentRepository.save(comment)

  def getCommentsForEvent(event_id):
      return CommentRepository.findByEvent(event_id)
}
```

> 📌 No modifications are made to `Event`, `User`, or `EventService`. This follows **Open/Closed** and **Single Responsibility Principles**.

---

## 🧱 8. SOLID Principles Mapping

| Principle | Application |
|-----------|-------------|
| **S – Single Responsibility** | Each service does only one thing (e.g., commenting, notification) |
| **O – Open/Closed**          | New features added through new services, not by modifying existing ones |
| **L – Liskov Substitution**  | Validators (URL, QR) can be used interchangeably |
| **I – Interface Segregation**| Interfaces are kept minimal and separate |
| **D – Dependency Inversion** | Services depend on abstractions (interfaces) not concrete classes |

---

## ✅ 9. Summary

- ✅ Trusted events via reference validation
- ✅ Seamless event registration and updates
- ✅ Modular, testable service classes
- ✅ Easy to extend with new features (comments, emoji reactions, analytics)

### 🔮 Future Enhancements:
- Emoji reactions & replies to comments  
- Real-time chat in event threads  
- Feedback analytics per event  
- Organizer dashboards  
- Push notification scheduling  

---

> 💡 Designed using clean OOP principles and service abstraction to ensure scalability, testability, and flexibility for feature evolution.

```

---

