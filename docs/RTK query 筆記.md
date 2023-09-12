---
sidebar_position: 3
---

# RTK query

## 代碼解析

1. `fetchCurrentUserCredits`: 這一支 api 是利用 `get` 方法打去拿 user 資料的，`builder.query` 後面的型別，前面是回傳回來的資料型態，後面是我要放進去的參數型態，因為這邊沒有給，所以就放`void`，另外要特別注意的是，我有多給一個`providesTags` ，這是給 RTK query 拿來判斷說，需不需要重新 fetch 一次這個 data 用的關鍵字
2. `fetchUpdateCredits`: 這一支 api 是利用 `post` 方法拿來傳成功付款後的 status 狀態給後端用的，型別順序跟上面一樣，比較特別的是，body 內要記得再利用物件包住，原因如下。
   - 必須利用 `{}` 包住 `paymentStatus` 的原因
     當您發送請求時，如果使用 **`body: paymentStatus`** 而不是 **`body: { paymentStatus }`**，後端可能會收到一個空的請求主體 (**`{}`**)。這是因為在構建請求時，您直接將 **`paymentStatus`** 作為請求主體的值傳遞，而不是將其放入一個物件中。
     通常情況下，發送請求時，請求主體需要是一個有效的 JSON 物件。如果您的 **`paymentStatus`** 變數不放在物件中，後端可能無法正確解析請求主體，因為它期望接收一個 JSON 物件。
     為了解決這個問題，您應該始終將數據放入物件中，即使用 **`body: { paymentStatus }`**。這將確保請求主體包含有效的 JSON 格式數據，使後端能夠正確解析它。如果您不將數據放入物件中，後端可能會將請求主體視為空物件，因為沒有提供有效的 JSON 結構。
3. 接下來，因為我們這支 api 會更新 user 的 credit 值，所以 `fetchCurrentUserCredits` 也要同步更新，因此就要搭配 `invalidatesTags` 去做更新，我們給定一個特定 array，RTK query 會回去找`providesTags` ，如果有一樣的 tag 的話，他會 invalidate 該 query 的 cache ，讓他 refetching 一次，記得位置要寫對，他不是放在 return 裡面

   - `providesTags` 跟 `invalidatesTags` 的 callback 參數

     1. `result`: response 拿回來的東西，因此我們可以用這個數值來動態給 tags
     2. `error` : 錯誤狀態，剛剛在 stack overflow 上有看到這種寫法，代表成功時會 refetching data

        ```tsx
        return error ? [] : ['Stripe_Payment'];
        ```

     3. `arg`: 以 `fetchUpdateCredits` 來說明的話， arg 會拿到 `paymentStatus`

```tsx
import { UserDataType } from '@/types/userData.types';
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const stripePaymentApi = createApi({
  reducerPath: 'stripePayment',
  baseQuery: fetchBaseQuery({ baseUrl: '/api/' }),
  tagTypes: ['Stripe_Payment'],
  endpoints: (builder) => ({
    fetchPayment: builder.query<{ clientSecret: string }, void>({
      query: () => 'stripe_setup',
    }),
    fetchCurrentUserCredits: builder.query<UserDataType, void>({
      query: () => {
        return 'current_user';
      },
      providesTags: (result) => {
        console.log('providesTags =>', result);
        return ['Stripe_Payment'];
      },
    }),
    fetchUpdateCredits: builder.mutation<
      UserDataType,
      { paymentStatus: string }
    >({
      query: ({ paymentStatus }) => {
        return {
          url: `stripe_successful`,
          method: 'POST',
          // 要注意給{}
          body: { paymentStatus },
        };
      },
      invalidatesTags: (result) => {
        console.log('invalidatesTags =>', result);
        return ['Stripe_Payment'];
      },
    }),
  }),
});

export const {
  useFetchPaymentQuery,
  useFetchUpdateCreditsMutation,
  useFetchCurrentUserCreditsQuery,
} = stripePaymentApi;
```

### `useMutation` 用法

1. `useMutation`回傳一個陣列，第一個是 `trigger function` ，第二個是一個物件，裡面包含，`{ status, error, data }`

   ```tsx
   const [updateCredit, { data }] = useFetchUpdateCreditsMutation();
   ```

   ```tsx
   if (result.paymentIntent?.status === 'succeeded') {
     updateCredit({ paymentStatus: result.paymentIntent?.status });
   }
   ```

   ```tsx
   React.useEffect(() => {
     if (!data?.googleId) return;
     stripeStore.setIsPaymentOpen(false);
     toast.success('payment success!', {
       position: toast.POSITION.TOP_CENTER,
     });
   }, [data]);
   ```

### `selectFromResult` for `useQuery`

1. 這個東西的好處是，可以只拿自己想要的東西出來，不用整坨都丟出來用，像下面這樣，本來拿出來的是 `data` ，但利用 `selectFromResult` 這個關鍵字，我們可以把 data 改叫做 credits，並且只拿 data 裡面的 credits 資料

   ```tsx
   const { credits } = useFetchCurrentUserCreditsQuery(undefined, {
     selectFromResult: ({ data }) => ({
       credits: data ? data.credits : 0,
     }),
   });
   ```
