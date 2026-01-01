Redux- це менеджер стану для js
Використовується коли дані потрібно зберігати централізовано, а не передавати в компоненти (корисно для великих SPA- single page application )

Основні складові Redux:

1. Store- централізоване сховище стану, створюється через createStore або сучасний configureStore з Redux toolkit
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});


2. Action- об'єкт, який описує, що сталося
const incrementAction = { type: 'counter/increment' };

3. Reducer- чиста функція, яка змінює стан в залежності від Action
function counterReducer(state = { value: 0 }, action) {
  switch(action.type) {
    case 'counter/increment':
      return { value: state.value + 1 };
    case 'counter/decrement':
      return { value: state.value - 1 };
    default:
      return state;
  }
}
4. Dispatch
Метод `store.dispatch(action)` відправляє action в store, викликаючи reducer.

5. Selector
Функція щоб дістати дані з Selector
const count = useSelector(state => state.counter.value);
