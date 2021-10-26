#### custom hook
<!-- 1. 
2. useMount -->
1. 作用：减少代码量，复用代码
2. useMount
    1. 引入原因: 经常在初始化时要写许多的以下的类似代码，所以可以考虑用useMount
    ```
      useEffect(()=> {...}, []);
      useEffect(()=> {...}, []);
    ```
    2. 封装
    ```
      export const useMount = (callback) => {
        useEffect(()=> {
          callback()
        }, [])
      }
    ```
    3. 使用
    ```
      useMount(() => {
        // 某段初始化代码
      })
    ```
3. useDebounce
    1. 引入原因：经常需要再某个数据变化时触发某段代码，比如输入框中输入数据，这种情况就会频繁触发某段要执行的代码，开销较大，所以可以用useDebounce(原理类似于防抖)
    ```
      useEffect(()=>{
        // 某段代码
      }, [param])
    ```
    2. 封装
    ```
      export const useDebounce = (value, delay)=> {
        const [debouncedValue, setDebouncedValue] = useState(value);
        useEffect(()=>{
          // 每次在value变化后，设置一个定时器
          const timer = setTimeout(()=> {
            setDebouncedValue(value)
          }, delay);
          // 每次在上一个useEffect处理完以后再运行
          return ()=> clearTimeout(timer)
        }, [value, delay])
      }
    ```
    3. 使用
    ```
      const param = {
        name: '',
        id: ''
      };
      const debouncedParam = useDebounce(param, 2000);
      useEffect(()=>{
        // 某段代码
      }, [debouncedParam])
    ```