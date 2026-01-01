Навігація- це перехід між екранами і в реакт нейтів є декілька способів як це реалізувати. 
Немає:
href 
window.location
все працює через навігатори
Основна бібліотека: @react-navigation/native


Для навігації в нас повинен бути NavigationContainer
Це корінь навігації без якої нічого не працює

import {NavigationContainer} from '@react-navigation/native'

function default function App(){

return(
<NavigationContainer`>
	 {/* тут всі навігатори */}
</NavigationContainer`>
)
}


---------------------------------------------------------------------------------------------

Stack Navigation(класичний перехід екран->екран як в браузері)
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

function AppStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={HomeScreen} />
      <Stack.Screen name="Details" component={DetailsScreen} />
    </Stack.Navigator>
  );
}

### Перехід між екранами:

`navigation.navigate('Details');`

### Повернутись назад:

`navigation.goBack();`

# Передача параметрів між екранами

### Передати:

`navigation.navigate('Details', {   id: 5,   title: 'Product' });`

### Отримати:

`import { useRoute } from '@react-navigation/native';  const route = useRoute(); console.log(route.params.id);`



# Bottom Tab Navigation
(Нижнє меню (Instagram, Telegram))

`import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';  
const Tab = createBottomTabNavigator();  

function Tabs() {   
return (     
<Tab.Navigator>       
<Tab.Screen name="Home" component={HomeScreen} />       
<Tab.Screen name="Profile" component={ProfileScreen} />     
</Tab.Navigator>   ); 
}`

Зазвичай **Tabs обгортають Stack**, а не навпаки.

---

# Drawer Navigation
Бокове меню (бургер)

import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function DrawerNav() {
  return (
    <Drawer.Navigator>
      <Drawer.Screen name="Home" component={HomeScreen} />
      <Drawer.Screen name="Settings" component={SettingsScreen} />
    </Drawer.Navigator>
  );
}

---

# Комбінація навігаторів (ДУЖЕ ВАЖЛИВО)

Реальна структура:

NavigationContainer
 └── Stack
     ├── AuthStack
     └── MainTabs
         ├── HomeStack
         ├── ProfileStack

**Stack всередині Tab** — стандарт індустрії

---
#  useNavigation

Якщо екран **не отримав `navigation` через props**
import { useNavigation } from '@react-navigation/native';
const navigation = useNavigation();
navigation.navigate('Home');

---
#  Налаштування header

<Stack.Screen
  name="Home"
  component={HomeScreen}
  options={{
    title: 'Головна',
    headerShown: true
  }}
/>


Можна:
- змінювати title
- ховати header
- додавати кнопки
---
# Reset / replace (часто питають на співбесідах)

### replace (без повернення назад):
`navigation.replace('Home');`
### reset (наприклад після логіну):
`navigation.reset({   index: 0,   routes: [{ name: 'Home' }], });`

## Спочатку ідея: що таке navigation stack
Уяви **стек екранів** (як стопка книжок):
`[ Login ]`
Коли ти робиш:
`navigation.navigate('Home');`
стає:
`[ Login ] [ Home ]   ← ти тут`
Кнопка **Back** повертає на `Login`.
## navigation.replace()
###  Що робить

**Замінює поточний екран** іншим  
**без можливості повернутись назад**

### Було:

`[ Login ]`
`navigation.replace('Home');`

###  Стало:
`[ Home ]`
 `Login` **видалений зі стеку**

---

### Реальний кейс

Після успішного логіну:
погано:
`navigation.navigate('Home'); // можна повернутись на Login`

правильно:
`navigation.replace('Home');`
користувач **не зможе повернутись** на логін кнопкою "назад"

### Коли використовувати replace

- login → home
- splash → app
- onboarding → app
---
## navigation.reset()
###  Що робить
**Повністю очищає стек** і створює новий

---

### Було:
`[ Splash ] [ Login ] [ Verify ]`
`navigation.reset({   index: 0,   routes: [{ name: 'Home' }], });`

### Стало:
`[ Home ]`
ВСЕ, що було — **стерто**

---

### Реальний кейс
Після:
- логіну
- реєстрації
- логауту
- зміни ролі користувача
`navigation.reset({   index: 0,   routes: [{ name: 'MainTabs' }], });`
---

## Різниця replace vs reset (коротко)

| replace              | reset                 |
| -------------------- | --------------------- |
| міняє **один** екран | чистить **весь стек** |
| простий              | радикальний           |
| частіше              | рідше                 |

---

# Deep Linking (коротко)
Відкриття екрану через URL:
`myapp://profile/123`
Використовується для:
- email
- push notifications
# Типові помилки новачків

- не загорнули все в `NavigationContainer`
- погана структура (все в одному Stack)
- використовують Tabs як root без Stack
- передають зайві params
- не обробляють reset після логіну

---
# Базові питання

1. Що таке навігація в React Native?
Навігація — це механізм переходу між екранами додатку та керування історією цих переходів.
2. Яку бібліотеку для навігації використовують найчастіше?
React Navigation.
3. Навіщо потрібен `NavigationContainer`?
Він зберігає стан навігації та є коренем для всіх навігаторів.
4. Чим `navigate` відрізняється від `goBack`?
`navigate` переходить на екран, `goBack` повертає на попередній.
5. Що таке navigation stack?
Це стек екранів, де кожен новий екран додається поверх попереднього.
# Stack Navigation

6. Що таке Stack Navigator?
Навігатор, який керує екранами за принципом стеку (LIFO).
7. Як працює стек екранів?
Новий екран додається зверху, попередній залишається під ним.
8. Що відбувається в стеці при `navigation.navigate()`?
Новий екран додається в стек.
9. Чим `navigate` відрізняється від `push`?
`navigate` може не додавати екран, якщо він уже активний, `push` завжди додає новий.
10. Коли кнопка “Back” зникає?
Коли в стеці немає попередніх екранів.
# navigate / push / replace

11. Чим `navigate` відрізняється від `replace`?
`navigate` додає екран, `replace` замінює поточний.
12. Коли варто використовувати `replace()`?
Після логіну, splash або onboarding.
13. Чому після логіну **не варто** використовувати `navigate()`?
Бо користувач зможе повернутись на екран логіну.
14. Що буде зі стеком після `replace()`?
Поточний екран буде видалений і замінений новим.
15. Чи можна повернутися назад після `replace()`?
НІ!
# reset (ДУЖЕ ВАЖЛИВО)

16. Що робить `navigation.reset()`?
Повністю очищає стек і створює новий.
17. Коли `reset` краще за `replace`?
Коли був складний флоу з кількох екранів.
18. Що станеться зі стеком після `reset()`?
Усі попередні екрани будуть видалені.
19. Навіщо в `reset` потрібен `index`?
Він визначає активний екран у новому стеці.
20. Чи можна використовувати `reset` для logout?
Так, це стандартний підхід.
#  Реальні кейси

21. Як правильно зробити перехід `Login → Home`, щоб користувач не повернувся назад?
Використати `replace` або `reset`.
22. Як очистити стек після реєстрації?
`navigation.reset()`.
23. Що краще використати після Splash Screen?
`replace`, якщо простий флоу, або `reset`.
24. Як реалізувати logout?
Очистити токени і зробити `reset` на Auth Stack.
25. Що буде, якщо після логіну зробити `navigate()`?
Користувач зможе повернутись на логін.
---
#  Params

26. Як передати параметри між екранами?
Через другий аргумент `navigate`.
27. Як отримати `params` на іншому екрані?
Через `route.params` або `useRoute()`.
28. Що станеться, якщо `params` не передати?
`route.params` буде `undefined`.
29. Чи можна передавати функції через `params`?
Не рекомендується.
---

# useNavigation / useRoute

30. Коли `navigation` не приходить через props?
Коли компонент не є screen.
31. Навіщо `useNavigation()`?
Для доступу до навігації з будь-якого компонента.
32. Навіщо `useRoute()`?
Для доступу до параметрів поточного маршруту.

---

# Tabs / Drawer

33. Чим Stack відрізняється від Tab Navigation?
Stack — послідовні переходи, Tabs — паралельні розділи.
34. Чи можна вкладати Stack в Tabs?
Так, це стандартна практика.
35. Яка стандартна архітектура навігації в додатку?
Stack → Tabs → Stack.

Стандартна архітектура навігації
## Stack → Tabs → Stack

Це **де-факто стандарт** у React Native додатках.
Ідея архітектури

- **Root Stack** вирішує: авторизований користувач чи ні
- **Tabs** — основні розділи додатку
- **В кожній вкладці — свій Stack**

## Чому Stack → Tabs → Stack — правильно

### Root Stack

- Splash
- Auth
- Main
### Tabs
- основні секції додатку
### Внутрішні Stack-и
- своя історія в кожній вкладці
 Як в реальних додатках:
- в Home → Details
- переходиш в Profile
- повертаєшся в Home → ти знову в Details
## Реальний приклад переходів

1️⃣ Відкрив додаток → Splash  
2️⃣ Є токен → MainTabs  
3️⃣ Home → Details  
4️⃣ Перемкнув Tab → Profile  
5️⃣ Назад в Home → все збережено

# Помилки і best practices

36. Які помилки новачки роблять з навігацією?
Використання `navigate` замість `replace` після логіну.
37. Чому не варто зберігати навігацію в Redux?
Бо навігація — це UI-стан.
38. Як обробити навігацію після зміни ролі користувача?
Зробити `reset` на новий стек.

---

# Питання з підвохом

39. Що буде, якщо викликати `replace` декілька разів?
Кожен раз замінюється поточний екран.
40. Чи можна зробити `reset` тільки для частини стеку?
Так, вручну формуючи `routes`.
41. Чим `reset` відрізняється від `popToTop`?
`popToTop` повертає до першого екрану, `reset` стирає все.
42. Як відкрити екран із push-повідомлення?
Через deep linking або `navigate` після обробки push.



Splash screen- це **перший екран**, який користувач бачить **під час запуску додатку**.
Зазвичай це:

- логотип
- назва додатку
- простий екран без кнопок
**Навіщо він потрібен**
Splash screen використовується для:
- перевірки, чи користувач залогінений
- перевірки токена
- завантаження базових даних
- визначення теми (dark/light)
- вибору, куди вести користувача далі