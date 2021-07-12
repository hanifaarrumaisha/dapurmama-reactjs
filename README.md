# DAPURBUNDA (On Boarding Project with Reactjs)
# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

# Learning Notes
## React Hooks
- React Hooks merupakan fitur dari react yang memungkinkan functional component menjadi stateful dan memiliki lifecycle function. Hooks utama untuk membuat state dalam functional component adalah **useState** dan untuk lifecycle function adalah **useEffect**.
- Penggunaan react hooks dalam hal ini useState dan useEffect memudahkan kita untuk memisahkan unrelated logic. Gunakan bila sebelumnya kita dipaksa menggabungkan logic yang tidak relevant berdasarkan lifecycle, dengan react hooks kita bisa memisahkan setiap state dengan setiap lifecycle, seperti dapat dilihat (disini)[https://reactjs.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns].
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

### Custom Hooks (original documentation) [https://reactjs.org/docs/hooks-custom.html]
- Kita dapat membuat hooks function sendiri, dengan membuat hooks function kita dapat share stateful logic function tanpa perlu membuat component baru.
- Pemanggilan custom hooks function harus ada di top level function (ga boleh ada di dalem if statement, for statement, atau didalam inner function).
- Sesungguhnya custom hooks merupakan javascript function biasa yang menggunakan konvensional nama diawal dengan `use` dan biasanya memanggil hooks lainnya. Sisanya function itu sangat flexible, bisa untuk 
- (Use Your Imagination)[https://reactjs.org/docs/hooks-custom.html#useyourimagination] **When to use custom hooks?** -> Ga masalah untuk untuk menolak melakukan abstraksi dengan cepat. Normal kalau code kita mungkin akan jadi lebih panjang (jangan terlalu terburu-buru mengubahnya jadi hooks). Tapi sebisa mungkin dorong diri kita untuk menemukan case-case dimana custom hook dapat: 
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

        Seperti dapat dilihat pada point 1 dan 2 bahwa function fetchdata melakukan update pada state, sehingga itu merupakan side effect yang memungkinkan suatu component melakukan rerender. Apabila dependencies [props.selectedChar] tidak ada, maka function fetchData akan terus dijalankan setiap kali component di render, karena tidak ada dependencies kapan useEffect itu dijalankan. Dengan menambahkan dependecies tersebut, function useEffect hanya akan dijalankakn ketika `props.selectedChar` berubah.
        
