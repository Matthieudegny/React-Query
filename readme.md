
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

-include it in the App component \
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

## Stale time
-set up the stale time (the state will be the same for X time):

        const { isLoading, data, isError, error } = useQuery ('super-heroes', fetchSuperHeroes,
                {
                staleTime: 30000,
                })

---

## Refetch defaults
-refetchOnMount set as default is true

        const { isLoading, data, isError, error } = useQuery ('super-heroes', fetchSuperHeroes,
                        {
                        refetchOnMount: ture
                        })

-refetchOnWindowFocus true \
-automatically updated = refetchOnWindowFocus: 'always'

---

## Polling
-refetchInterval (set interval)ex every two seconds will be:

        {
        refetchInterval: 2000,
        })
-and refetchIntervalInBackground when its not in focus

---

## useQuery on click
2 steps to set up:\
-first its to warn query to not fetch at the mount of the componunt


        {
        enabled: false
        })

-fetch at the click of the button (with refechas handler):

         const { isLoading, data, isError, error, refetch } = useQuery ('super-heroes', fetchSuperHeroes,
                        {
                         enabled: false
        })

        <button onClick={reftech}>Fetch heroes<button>

-think about isLoading and isFetching, in case there is a button refresh, because the state will be updated with isFecthing true and isLoading false

---

## Success and Error Callbacks

-in case of query's success (data is directly updated with query, just need to call it):

        const onSuccess = (data) => {
                 console.log('Perform side effect after encountering error', data)
        }

-in case of query's error (error is directly updated with query, just need to call it):

        const onError = (error) => {
                console.log('Perform side effect after encountering error', error)
        }

-and set up query function:

         const { isLoading, data, isError, error, refetch } = useQuery ('super-heroes', fetchSuperHeroes,
                {
                onSuccess: onSuccess,
                onError: onError
        })

---

## Data Transformation

-transform directly data in the query (with select):

         const { isLoading, data, isError, error, refetch } = useQuery ('super-heroes', fetchSuperHeroes,
                {
                onSuccess: onSuccess,
                onError: onError,
                select: (data) => {
                        const superHeroNames = data.data.map((hero) => hero.name)
                        return superHeroNames
                }
        })

        //JSX
        {/*{data?.data.map((hero) => {
                return <div key={hero.name}>{hero.name}</div>
        })}*/}
        {data.map((heroName) => {
                return <div key={heroName}>{heroName}</div>
        })}

it's possible to use filter or anything else at the place of map

---

## Custom Query Hook
reminder:\
query hook= \
-first argument is the unique key (ex: 'super-heroes) \
-second argument is the fetch function 

        const fecthSuperHeroes = () => {
            return axios.get('http://localhost:4000/superheroes')
        }

        const { isLoading, data, isError, error, refetch } = useQuery ('super-heroes', fetchSuperHeroes,

-third argument is an object with options, configuration

        {
        onSuccess: onSuccess,
        onError: onError,
        select: (data) => {
                const superHeroNames = data.data.map((hero) => hero.name)
                return superHeroNames
        }

for a bigger app, its important to use a custom hook to reuse it\
the custom hook is code in a new file \

import { useQuery, useQueryClient } from 'react-query'
import axios from 'axios'

        //in custom query hook file
        const fetchSuperHero = () => {
        return axios.get(`http://localhost:4000/superheroes`)
        }

        export const useSuperHeroData = (onSuccess, onError) => {
        return useQuery('super-hero', fetchSuperHeroes, {
        onSuccess,
        onError,
        select: (data) => {
                const superHeroNames = data.data.map((hero) => hero.name)
                return superHeroNames
        }
        }
        })
        }

        //in App component
        import {useSuperHeroesData} from '../hooks/useSuperHeroesData

        const { isLoading, data, isError, error, isFetching, refetch} = useSuperHeroesData(onSucces, onError)

---

## Query by id
