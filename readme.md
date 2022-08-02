
## Set-up

-instal react-query
-wrap App component with 

        <QueryClientProvider client={queryClient}></QueryClientProvider>

-creat an instance of the query client; 

        const queryClient = new QueryClient()

---

## Basic utilisation

-import { useQuery } from 'react-query' \
-use the hook useQuery() with at least 2 arguments: first the query key,second fonction with request
ex: 

        useQuery('super-heroes', () => {
            return axios.get('http://localhost:4000/superheroes')
        })

-save it in a const, possibility to use destruturing ex: const { isLoading, data } \
-to have a clearer code, save the request in a variable, as for exemple: 

        const fecthSuperHeroes = () => {
            return axios.get('http://localhost:4000/superheroes')
        }
        const { isLoading, data } = useQuery ('super-heroes', fetchSuperHeroes)

---

## Handle errors
-add isError, and error in the destructuring object and use it

        const { isLoading, data, isError, error } = useQuery ('super-heroes', fetchSuperHeroes)

        if(isError) {
            return <h2>{error.message}</h2>
        }

---

## Query Devtools

-import it:
import {ReactQueryDevTools} from 'react-query/devtools'\

-include it in the component
        `<ReactQueryDevtools initialIsOpen={false} position='bottom-right' />`

-you can find the queries with their keys

---

## Query Cache
-every query results are catch for five minutes in react query (default) to change it:

        const { isLoading, data, isError, error } = useQuery ('super-heroes', fetchSuperHeroes,
        {
            cacheTime: 5000,
        })

-is fetching different with isLoading for these case, if the result change from the last request, isLoading ; false  but isFetching : true; Reactquery check in the background if the state change

---