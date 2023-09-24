# react - toastify

1. 在 `main.tsx` 塞入 `ToastContainer` component 並且引入 `css`

   ```tsx
   import React from 'react';
   import ReactDOM from 'react-dom/client';
   import { BrowserRouter } from 'react-router-dom';
   import App from './App.tsx';
   import './index.css';
   import { Provider } from 'react-redux';
   import store from './store/store.ts';
   import { ToastContainer } from 'react-toastify';
   import 'react-toastify/dist/ReactToastify.css';

   ReactDOM.createRoot(document.getElementById('root')!).render(
     <React.StrictMode>
       <BrowserRouter>
         <Provider store={store}>
           <ToastContainer autoClose={2000} />
           <App />
         </Provider>
       </BrowserRouter>
     </React.StrictMode>
   );
   ```

2. 之後引用 `toast` 方法來給文字

   ```tsx
   if (result.error) {
     toast.warn('payment fail!', {
       position: toast.POSITION.TOP_CENTER,
     });
   }
   if (result.paymentIntent?.status === 'succeeded') {
     stripeStore.setIsPaymentOpen(false);
     toast.success('payment success!', {
       position: toast.POSITION.TOP_CENTER,
     });
   }
   ```
