# DDay
## Tech Stack
- React Native
- Redux
- React Navigation
- React Saga
- Jest
- Redux Persist
- Styled Component

# Learning Notes
## React Native

### [Setup](https://reactnative.dev/docs/environment-setup)
#### Requirement
- JDK 8 or newer
- NPM 12 or newer
- Android Studio
    - Android SDK
    - Android SDK Platform
    - Android Virtual Device

#### How to Start
- open project on Android Studio
- pastikan AVD for Android 29 exists
- run emulator
- start metro `npx react-native start`
- start your application `npx react-native run-android`

## React Hooks
- React Hooks merupakan fitur dari react yang memungkinkan functional component menjadi stateful dan memiliki lifecycle function. Hooks utama untuk membuat state dalam functional component adalah **useState** dan untuk lifecycle function adalah **useEffect**.
- Penggunaan react hooks dalam hal ini useState dan useEffect memudahkan kita untuk memisahkan unrelated logic. Gunakan bila sebelumnya kita dipaksa menggabungkan logic yang tidak relevant berdasarkan lifecycle, dengan react hooks kita bisa memisahkan setiap state dengan setiap lifecycle, seperti dapat dilihat [disini](https://reactjs.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns).
### State Hooks (useState)
React hooks built in supaya bisa jadi statefull. Merupakan function yang returnnya itu array [state, setState]. state adalah current state, sementara setState adalah function untuk ubah statenya.

### Effect Hooks (useEffect)
- react hooks function yang dipanggil setiap kali component selesai dirender. Punya dua parameter, parameter pertama function yang akan dijalankan, parameter kedua adalah array dependencies. Dependencies yang dimaksud adalah function yang ada pada parameter pertama dijalankan berdasarkan pada variable-variable yang didefinisikan pada array dependencies tersebut. Kalau parameter kedua kosong, artinya function di parameter pertama akan dijalankan setiap kali component tersebut dirender, karena tidak function tersebut tidak memiliki dependecies apapun, sehingga dapat berjalan sesuka hatinya. 
- **componentDidMount** → Kalau parameter kedua diisi empty array, dia jadi mirip componentDidMount, artinya dia akan dipanggil ketika ga ada state apapun yang berubah, artinya itu pas pertama kali component di render.
- **componentDidUpdate** → Kalau parameter kedua diisi dengan sesuatu, misal `[someState]`. Maka function pada parameter kedua hanya akan dijalankan pada saat pertama kali component dirender dan setiap kali `someState` mengalami perubahan. Ini cocok untuk menjaga sideeffect dari perubahan state lainnya. Misal kita tidak membutuhkan function ini berjalan ketika `anotherState` berubah. Maka penting mendefinisikan dependencies ketika tau suatu function hanya digunakan berdasarkan suatu state.
- **componentWillUnmount** → Kalau ada dependenciesnya maka itu akan kayak componentDidUpdated. return dari useEffect selalu dijalankan setiap sebelum useEffect selanjutnya dijalankan. Kalau dalam case array dependencies merupakan empty array maka apa yang direturn merupakan function yang akan dijalankan ketika component di unmount, sehingga seperti componentWillUnmount.
- **ComponentWillMount** → just add function before jsx line. Everything before the jsx will run before component rendered.  

### React Memo
- React.memo(component): merupakan shouldComponentUpdate on functional components, make sure this component will be update when props are changes. 
- **shouldComponentUpdate** merupakan penanda kapan saja component ini perlu diupdate. Supaya ketika yg diluar state yang ada di component ini dan diluar props yang jadi input komponen ini, component ini ga perlu dirender ulang. 
- Bedanya shouldComponentUpdate dengan componentDidUpdate yaitu kalau shouldComponentUpdate ngomongin apa yang ngetrigger component ini dirender ulang berdasarkan perspektif parent, sementara componentDidUpdate ngomongin perubahan didalam component itu sendiri yang mentrigger update di component.
- Nah React.Memo ini secara otomatis rerender hanya ketika input props dari component ini yg berubah. Ketika parent component punya state lain, component ini otomatis hanya berubah ketika input propsnya berubah.  
- Tapi kita dapat menambahkan kostumisasi, dengan menambahkan parameter kedua berupa function yang mereturn boolean, dimana true berarti component tersebut tidak akan dirender, dan return false yang akan membuat component tersebut merender ulang. Kondisi ini berbanding terbalik dengan shouldComponentUpdate, contoh:
    ```js
        export default React.memo(Character, (prevProps, nextProps) => {
            return nextProps.selectedChar === prevProps.selectedChar;
        })
    ```


### Ref Hooks (useRef)
#TODO

### Custom Hooks [original documentation](https://reactjs.org/docs/hooks-custom.html)
- Kita dapat membuat hooks function sendiri, dengan membuat hooks function kita dapat share stateful logic function tanpa perlu membuat component baru.
- Pemanggilan custom hooks function harus ada di top level function (ga boleh ada di dalem if statement, for statement, atau didalam inner function).
- Sesungguhnya custom hooks merupakan javascript function biasa yang menggunakan konvensional nama diawal dengan `use` dan biasanya memanggil hooks lainnya. Sisanya function itu sangat flexible, bisa untuk 
- [Use Your Imagination](https://reactjs.org/docs/hooks-custom.html#useyourimagination) **When to use custom hooks?** -> Ga masalah untuk untuk menolak melakukan abstraksi dengan cepat. Normal kalau code kita mungkin akan jadi lebih panjang (jangan terlalu terburu-buru mengubahnya jadi hooks). Tapi sebisa mungkin dorong diri kita untuk menemukan case-case dimana custom hook dapat: 
   - menyembunyikan complex logic dari UI component yang sebenarnya sederhana
   - membantu kita menguraikan komponen yang berantakan.
- contoh custom hooks:
    ```js
    import { useState, useEffect } from 'react';

    function useFriendStatus(friendID) {
    const [isOnline, setIsOnline] = useState(null);

    useEffect(() => {
        function handleStatusChange(status) {
        setIsOnline(status.isOnline);
        }

        ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
        return () => {
        ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
        };
    });

    return isOnline;
    }
    ```
- Contoh-contoh pemanggilan custom hooks
    - Bisa panggil kayak biasa dan passing props sebagai inputnya
        ```js
        function FriendListItem(props) {
            const isOnline = useFriendStatus(props.friend.id);

            return (
                <li style={{ color: isOnline ? 'green' : 'black' }}>
                {props.friend.name}
                </li>
            );
        }
        ```
    - Bisa langsung oper state sebagai props/input dari hooks
        ```js
        function ChatRecipientPicker() {
        const [recipientID, setRecipientID] = useState(1);
        const isRecipientOnline = useFriendStatus(recipientID);

        return (
            <>
            <Circle color={isRecipientOnline ? 'green' : 'red'} />
            <select
                value={recipientID}
                onChange={e => setRecipientID(Number(e.target.value))}
            >
                {friendList.map(friend => (
                <option key={friend.id} value={friend.id}>
                    {friend.name}
                </option>
                ))}
            </select>
            </>
        );
        }
        ```
        Bisa dilihat kalau recipientID yang merupakan state juga, 

### Reducer Hooks (useReducer)
#TODO

### Additional
- [Hooks FAQ](https://reactjs.org/docs/hooks-faq.html#how-to-create-expensive-objects-lazily)
- [How to fetch data with React Hooks?](https://www.robinwieruch.de/react-hooks-fetch-data)
- [High Order Function](https://reactjs.org/docs/higher-order-components.html)
- [Refs and DOM](https://reactjs.org/docs/refs-and-the-dom.html#:~:text=In%20the%20typical,an%20escape%20hatch) -> need to undestand before useRef

# React-Redux
## [Terminology](https://redux.js.org/tutorials/essentials/part-1-overview-concepts#terminology) 
- Store -> tempat penyimpanan state secara global dalam aplikasi, hanya boleh terdapat 1 store dalam 1 aplikasi
- Reducer -> merupakan sebuah function yang berperan sebagai pengendali dari action-action yang dapat dilakukan, dan melakukan action-action tersebut.
- View/App -> yang akan mengsubscribe store tersebut, sehingga ketika ada perubahan pada state tersebut, 
- Action -> merupakan suatu object yang terdiri dari `{type: "anyType", payload: anyPayload}`. Atau bisa kita anggap sebagai suatu event. view/app akan melakukan dispatch (trigger an action).

## [Flow](https://redux.js.org/tutorials/essentials/part-1-overview-concepts#redux-application-data-flow)
- Initial setup:
    - A Redux store is created using a root reducer function
        - define the store
            ```js
            import { configureStore } from '@reduxjs/toolkit'
            // ...

            const store = configureStore({
            reducer: {
                posts: postsReducer,
                comments: commentsReducer,
                users: usersReducer
            }
            })

            // Infer the `RootState` and `AppDispatch` types from the store itself
            export type RootState = ReturnType<typeof store.getState>
            // Inferred type: {posts: PostsState, comments: CommentsState, users: UsersState}
            export type AppDispatch = typeof store.dispatch
            ```

            for typescript, we need to add this on `app/hooks.ts`, and instead of useDispatch or useSelector, use useAppDispatch and useAppSelector.
            ```js
            import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux'
            import type { RootState, AppDispatch } from './store'

            // Use throughout your app instead of plain `useDispatch` and `useSelector`
            export const useAppDispatch = () => useDispatch<AppDispatch>()
            export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector
            ```
        - define the state and reducer using createSlice
            ```js
            import { createSlice, PayloadAction } from '@reduxjs/toolkit'
            import type { RootState } from '../../app/store'

            // Define a type for the slice state
            interface CounterState {
            value: number
            }

            // Define the initial state using that type
            const initialState: CounterState = {
            value: 0
            }

            export const counterSlice = createSlice({
            name: 'counter',
            // `createSlice` will infer the state type from the `initialState` argument
            initialState,
            reducers: {
                increment: state => {
                state.value += 1
                },
                decrement: state => {
                state.value -= 1
                },
                // Use the PayloadAction type to declare the contents of `action.payload`
                incrementByAmount: (state, action: PayloadAction<number>) => {
                state.value += action.payload
                }
            }
            })

            export const { increment, decrement, incrementByAmount } = counterSlice.actions

            // Other code such as selectors can use the imported `RootState` type
            export const selectCount = (state: RootState) => state.counter.value

            export default counterSlice.reducer
            ```
        
    - The store calls the root reducer once, and saves the return value as its initial state
    - When the UI is first rendered, UI components access the current state of the Redux store, and use that data to decide what to render. They also subscribe to any future store updates so they can know if the state has changed. To subscribe, we use this:
        ```js
        import React, { useState } from 'react'

        import { useAppSelector, useAppDispatch } from 'app/hooks'

        import { decrement, increment } from './counterSlice'

        export function Counter() {
        // The `state` arg is correctly typed as `RootState` already
        const count = useAppSelector(state => state.counter.value)
        const dispatch = useAppDispatch()

        // omit rendering logic
        }
        ```
- Updates:
    - Something happens in the app, such as a user clicking a button
    - The app code dispatches an action to the Redux store, like `dispatch({type: 'counter/increment'})`
    - The store runs the reducer function again with the previous state and the current action, and saves the return value as the new state
    - The store notifies all parts of the UI that are subscribed that the store has been updated
    - Each UI component that needs data from the store checks to see if the parts of the state they need have changed.
    - Each component that sees its data has changed forces a re-render with the new data, so it can update what's shown on the screen

## Additional
- [Redux Thunks in Typescript](https://redux.js.org/usage/usage-with-typescript#type-checking-redux-thunks)


# Common Error
## React Hooks
- Problem: react component selalu kerender sampai infinite loop
    - Solution: itu karena ada useEffect yang salah passing dependencies. Contoh:
        ```js
        const fetchData = () => {
            console.log('Sending Http request for new character with id ' + props.selectedChar);
            setIsLoading(true);
            fetch('https://swapi.co/api/people/' + props.selectedChar)
                .then(response => {
                    if (!response.ok) {
                    throw new Error('Could not fetch person!');
                    }
                    return response.json();
                })
                .then(charData => {
                    const loadedCharacter = {
                    id: props.selectedChar,
                    name: charData.name,
                    height: charData.height,
                    colors: {
                        hair: charData.hair_color,
                        skin: charData.skin_color
                    },
                    gender: charData.gender,
                    movieCount: charData.films.length
                    };
                    setIsLoading(false); // 1. AWARE THIS 
                    setLoadedCharacter(loadedCharacter); // 2. AWARE THIS
                })
                .catch(err => {
                    console.log(err);
                    setIsLoading(false);
                });
            };

        useEffect(() => {
            fetchData();
            return () => {
                console.log('Cleaning up...');
            };
        }, [props.selectedChar]); // 3. AWARE THIS
        ```

        Seperti dapat dilihat pada point 1 dan 2 bahwa function fetchdata melakukan update pada state, sehingga itu merupakan side effect yang memungkinkan suatu component melakukan rerender. Apabila dependencies `[props.selectedChar]` tidak ada, maka function fetchData akan terus dijalankan setiap kali component di render, karena tidak ada dependencies kapan useEffect itu dijalankan. Dengan menambahkan dependecies tersebut, function useEffect hanya akan dijalankakn ketika `props.selectedChar` berubah.
        