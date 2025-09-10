# 自定义Hooks
1. 状态持久化
    ```
        import React, { useState, useEffect } from 'react'
        function usePersistentState(storageKey, defaultValue) {
            // 1. 初始化 state，尝试从 localStorage 读取
            const [value, setValue] =useState(()=>{
             const storedValue = localStorage.getItem(storageKey);
             
             if(storedValue !== null){
                return JSON.parse(storedValue); //localStorage存储的是字符串
             }
             // 如果是函数，则执行函数获取默认值，否则直接返回默认值
             return typeof defaultValue === 'function' ? defaultValue() : defaultValue
            })
        }
        // 2. 在 value 或 storageKey 变化时将其存入localStorage
        useEffect(()=>{
            localStorage.setItem(storageKey,JSON.stringify(value))
        },[value,storageKey])

        const restState = useCallback(()=>{
            setValue(typeof defaultValue === 'function' ? defaultValue() : defaultValue)
        },[defaultValue])

        return [value, setValue, resetState]; // 返回state、setState、restState

        // 在组件中使用
        function User() {
            const [theme, setTheme, resetTheme] = usePersistentState('appTheme', 'light');

            return (
                <div style={{ padding: '20px' }}>
                <h1>Current Theme: {theme}</h1>
                
                <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
                    Toggle Theme
                </button>
                
                <button onClick={resetTheme} style={{ marginLeft: '10px' }}>
                    Reset Theme
                </button>
                </div>
            );
        }
        export default User;
        ```

