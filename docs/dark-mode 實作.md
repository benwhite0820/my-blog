# Dark Mode 實作技巧

1. 在 react 先準備好開關的狀態按鈕，如果是 true 的話加一個 .dark className

   ```jsx
   import React, { useState } from 'react';
   import './App.scss';

   const App = () => {
     const [theme, setTheme] = useState('light');

     const handleThemeChange = () => {
       setTheme(theme === 'light' ? 'dark' : 'light');
     };

     return (
       <div className={`app ${theme}`}>
         <h1>Hello World</h1>
         <button onClick={handleThemeChange}>切換主題</button>
       </div>
     );
   };

   export default App;
   ```

2. 在 css 裡面寫個 function 來做切換，如果是 true 的話，讓 function 回傳另外一組 map

   ```scss
   $gray: (
     'G1': #ffffff,
     'G8': #000000,
     ,
   );

   $darkGray: (
     'G1': #000000,
     'G8': #ffffff,
   );

   $color: (
     'global': (
       'primary': map-get($gray, 'G1'),
       'grey': map-get($gray, 'G8'),
     ),
   );

   $darkColor: (
     'global': (
       'primary': map-get($darkGray, 'G1'),
       'grey': map-get($darkGray, 'G8'),
     ),
   );

   @function getColorMap($isDarkMode) {
     @if $isDarkMode {
       @return $darkColor;
     } @else {
       @return $color;
     }
   }

   .board__wrapper {
     &.dark {
       $map: getColorMap(true);
       background-color: map-get($map, 'global', 'primary');
     }
     background-color: map-get($map, 'global', 'primary');
   }
   ```
