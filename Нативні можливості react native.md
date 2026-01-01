# 1. Типові інтервʼю-питання + відповіді

### Загальні

**1. Що таке нативні можливості в React Native?**  
Нативні можливості — це доступ до апаратних функцій телефону (камера, GPS, біометрія) через Android/iOS API за допомогою бібліотек.

**2. Як React Native працює з нативними API?**  
Через готові бібліотеки або нативні модулі, які є мостом між JS і нативним кодом.

**3. Expo vs CLI для нативних можливостей?**  
Expo — простіше, швидше стартувати. CLI — більше контролю, складніше налаштування.

### Камера / Галерея

**4. Як отримати доступ до камери?**  
Запросити permission → відкрити камеру → отримати фото/відео.

**5. Що буде, якщо користувач заборонив доступ?**  
Потрібно показати повідомлення або fallback.
### Геолокація

**6. Які типи доступу до геолокації є?**  
Foreground і Background.

**7. Що робити, якщо GPS вимкнений?**  
Повідомити користувача і запропонувати увімкнути.

### Push notifications

**8. Що таке push notifications?**  
Повідомлення, які приходять з сервера навіть коли додаток закритий.

**9. Що таке device token?**  
Унікальний ідентифікатор пристрою для надсилання push-повідомлень.

**10. Push vs Local notifications?**  
Push — з сервера, Local — створюються прямо в додатку.

### Біометрія

**11. Які типи біометрії існують?**  
Face ID, Touch ID, Fingerprint.

**12. Що робити, якщо біометрія недоступна?**  
Зробити fallback на пароль або PIN.

# 3. Permissions — глибше (ДУЖЕ ВАЖЛИВО)

### Що таке permissions

Permissions — це **дозволи користувача** на доступ до нативних можливостей.

###  Основні правила

- завжди запитувати **перед використанням**
- користувач може **відмовити**
- permissions різні на iOS і Android
- Android — через `AndroidManifest.xml`
- iOS — через `Info.plist`
Типовий flow
Запит permission
↓
granted → працюємо
denied → показуємо повідомлення
blocked → ведемо в settings


# 4. Push Notifications — step by step
Як це працює (схема)

App → отримує device token
↓
Backend зберігає token
↓
Backend → Firebase / APNs
↓
Push → телефон → додаток

## Кроки реалізації (логіка)

1️⃣ Запросити permission  
2️⃣ Отримати device token  
3️⃣ Передати token на сервер  
4️⃣ Сервер надсилає push  
5️⃣ Додаток обробляє натискання




# 1. Чому push не приходять на iOS

###  Коротка відповідь (для інтервʼю)

На iOS push-повідомлення не приходять без дозволу користувача і правильно налаштованих сертифікатів та APNs.

---
###  Основні причини 
### 1️⃣ Користувач **не дав permission**
- iOS **не показує push без згоди**
- permission потрібно запросити вручну
`Notifications.requestPermissionsAsync();`

---

### 2️⃣ Немає або неправильний **APNs ключ / сертифікат**
- Apple вимагає APNs
- без нього push **фізично не дійдуть**
---

### 3️⃣ Неправильний **bundleId**
- `com.app.dev` ≠ `com.app.prod`
- токен буде інший → push не доходять
---

### 4️⃣ Додаток запущений з **Expo Go**
- не всі push працюють коректно
- для реального тесту потрібен build
---

### 5️⃣ Push прийшов, але не показався
- foreground режим
- не налаштований handler
---

#  2. Різниця між FCM і APNs

### Що це таке
- **FCM** — Firebase Cloud Messaging (Google)
- **APNs** — Apple Push Notification service
---
### Коротко

| FCM      | APNs        |
| -------- | ----------- |
| Android  | iOS         |
| Google   | Apple       |
| Простішe | Строгіше    |
| Один SDK | Сертифікати |

---

### Як це працює разом
`Backend  ↓ FCM → Android  ↓ APNs → iOS`
Для iOS FCM **все одно використовує APNs**

---
#  3. Foreground vs Background
### Foreground
- додаток **відкритий**
- push **не показується автоматично**
- потрібно обробляти вручну
`Notifications.setNotificationHandler({   handleNotification: async () => ({     shouldShowAlert: true,   }), });`

---

### Background
- додаток **згорнутий**
- push **показується системою**
- код не виконується одразу
---

### Closed / Killed
- додаток повністю закритий
- push відкриває додаток
---
###  Таблиця

| Стан       | Push видно           | JS код |
| ---------- | -------------------- | ------ |
| Foreground | ❌ (за замовчуванням) | ✅      |
| Background | ✅                    | ❌      |
| Closed     | ✅                    | ❌      |

#  4. Як відкрити екран з push

### Ідея
Push містить **дані**, за якими додаток вирішує, **куди перейти**.
Приклад payload
`{   "screen": "Details",   "id": "42" }`

---
###  Логіка в додатку

Notifications.addNotificationResponseReceivedListener(response => {
  const data = response.notification.request.content.data;

  navigation.navigate(data.screen, {
    id: data.id
  });
});
### Типові кейси
- push → відкрився конкретний товар
- push → відкрився чат
- push → відкрився профіль