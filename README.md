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
## useState
React hooks built in supaya bisa jadi statefull. Merupakan function yang returnnya itu array [state, setState]. state adalah current state, sementara setState adalah function untuk ubah statenya.

## useEffect
- react hooks function yang dipanggil setiap kali component selesai dirender. Punya dua parameter, parameter pertama function yang akan dijalankan, parameter kedua adalah array dependencies (function ini dijalankan berdasarkan variable apa yang berubah). Kalau parameter kedua kosong, artinya function di parameter pertama akan dijalankan setiap kali component tersebut dirender. 
- **componentDidMount** -> Kalau parameter kedua diisi empty array, dia jadi mirip componentDidMount, artinya dia akan dipanggil ketika ga ada state apapun yang berubah, artinya itu pas pertama kali component di render.
- **componentWillUnmount** -> Kalau ada dependenciesnya maka itu akan kayak componentDidUpdated. return dari useEffect selalu dijalankan setiap sebelum useEffect selanjutnya dijalankan. Kalau dalam case array dependencies merupakan empty array maka apa yang direturn merupakan function yang akan dijalankan ketika component di unmount, sehingga seperti componentWillUnmount.
- ComponentWillMount → just add function before jsx line. Everything before the jsx will run before component rendered.  

## React Memo
- React.memo(component): merupakan shouldComponentUpdate on functional components, make sure this component will be update when props are changes. If the return true that props are equals → will not rerender, and the return false → will rerender. 

## Custom Hooks
- Kita dapat membuat hooks function sendiri, dengan membuat hooks function kita dapat share stateful logic function tanpa perlu membuat component baru.
- Pemanggilan custom hooks function harus ada di top level function (ga boleh ada di dalem if statement, for statement, atau didalam inner function).
- 

## Common Error
- react component selalu kerender sampai infinite
    - itu karena ada useEffect yang salah passing dependencies. Contoh:
        ```const fetchData = () => {
    console.log(
      'Sending Http request for new character with id ' + props.selectedChar
    );
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
        setIsLoading(false);
        setLoadedCharacter(loadedCharacter);
      })
      .catch(err => {
        console.log(err);
        setIsLoading(false);
      });
  };

  // componentDidUpdate(prevProps) {
  //   console.log('Component did update');
  //   if (prevProps.selectedChar !== props.selectedChar) {
  //     this.fetchData();
  //   }
  // }

  // useEffect(() => {
  //   fetchData();
  // }, []);

  useEffect(() => {
    fetchData();
    return () => {
      console.log('Cleaning up...');
    };
  }, [props.selectedChar]);
```
