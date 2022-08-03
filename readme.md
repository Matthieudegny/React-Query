
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

-new custome hook\
-use it with useparams from react-router\
-add the id in the first parameter of the query hook with an array because key + id :

        import { useQuery } from 'react-query'
        import axios from 'axios'

        export const useSuperHeroData = (heroId) => {
                return useQuery(['super-hero',heroId], () => fetchSuperHero(heroId))
        }

-fecthSuperHeroId need to accept the id as param so arrow function 

-create the const with the request

        const fetchSuperHero = (heroId) => {
                return axios.get('http://localhost:4000/superheroes/${heroId})
        }

-then use the custom hook in the component:

        import { useParams } from 'react-router-dom'
        import { useSuperHeroData } from '../hooks/useSuperHeroData'

        export const RQSuperHeroPage = () => {
        const { heroId } = useParams()
        const { isLoading, data, isError, error } = useSuperHeroData(heroId)

        if (isLoading) {
        return <h2>Loading...</h2>
        }

        if (isError) {
        return <h2>{error.message}</h2>
        }
        return (
        <div>
        {data.data.name} - {data.data.alterEgo}
        </div>
        )
        }

---

## Parallel queries
-possibility to make severals queries in one function: 

        export const ParallelQueriesPage = () => {
        const { data: superHeroes } = useQuery('super-heroes', fetchSuperHeroes)
        const { data: friends } = useQuery('friends', fetchFriends)
        console.log(superHeroes, friends)
        return <div>Parallel Queries</div>
        }

juste remember that you have twos results, so twos data, just redefine them

---

## Dynamic parallele queries
-import {useQueries} from 'react-query' (and not {useQuery})\
-the hookuseQueries has a different syntax:

        const fetchSuperHero = heroId => {
        return axios.get(`http://localhost:4000/superheroes/${heroId}`)
        }

        export const DynamicParallelPage = ({ heroIds }) => {
        const queryResults = useQueries(
        heroIds.map(id => {
        return {
                queryKey: ['super-hero', id],
                queryFn: () => fetchSuperHero(id)
        }
        })
        )

        console.log({ queryResults })
        return <div>Dynamic Parallel Queries</div>
        }

-the hook useQueries give the possibility to loop in an array and fetch at each index\
-the querykey (first params in classic query) is in the object returned, and the function (second params) is in the object too. 
-the result will be an array

---

## Dependent queries
-fetch a request, and use the result to make an other request using the result of previous request

        const fetchUserByEmail = email => {
        return axios.get(`http://localhost:4000/users/${email}`)
        }

        const fetchCoursesByChannelId = channelId => {
        return axios.get(`http://localhost:4000/channels/${channelId}`)
        }

        export const DependentQueriesPage = ({ email }) => {

        //i obtain the result of my first request

        const { data: user } = useQuery(['user', email], () =>
        fetchUserByEmail(email)
        )

        //using the result first request to make my second resquest
        //if user is true and data too, then use user.data.channeliD
        
        const channelId = user?.data?.channelId
        useQuery(['courses', channelId], () => fetchCoursesByChannelId(channelId), {

        //this request is using the result of the first request, so if the first request is not done, the result of this one will be undefined, then i need to set up as enabled if channelId is false

        enabled: !!channelId
        })
        //send directly some JSX
        return <div>DependentQueries</div>
        }

---

## Initial query data
-if you made a request and obtain a result that you can use in an other component, you can do it without making the request, just uploading it from react query \
-import useQueryClient \
-call and save it 

        const queryClient = useQueryClient()

        -then same syntax than classic query, only add initialData in the option object:

        export const useSuperHeroData = heroId => {
        const queryClient = useQueryClient()
        return useQuery(['super-hero', heroId], fetchSuperHero, {
        initialData: () => {

        //if super-heroes result is available, then find + save it

        const hero = queryClient.getQueryData('super-heroes')
                ?.data?.find(hero => hero.id === parseInt(heroId))
        if (hero) {
                return { data: hero }

        //obligation to return undefined if no result

        } else {
                return undefined
        }
        }
        })
        }

---

## Paginated Queries

-limit of result is setup in the request, paginated setting in a dynamic way (just check if its compatible with the back)\
-import { useState } from 'react' (will be used to give the number of pages)

        const [pageNumber, setPageNumber] = useState(1)

-add pageNumber is the array as first parameter, add it to the function too

        
        const fetchColors = pageNumber => {
        return axios.get(`http://localhost:4000/colors?_limit=2&_page=${pageNumber}`)
        }

        const { isLoading, isError, error, data, isFetching } =         useQuery(
        ['colors', pageNumber],
        () => fetchColors(pageNumber),
        {
        keepPreviousData: true
        }
        )

-keepPreviousData will allow react query to keept the result of the last request\
-and find an exemple of JSX buttons to get more or less result on your app\
-the query fecth is done automatically when the state pageNumber change?


        <button
                onClick={() => setPageNumber(page => page - 1)}
                disabled={pageNumber === 1}>
                Prev Page
        </button>
        <button
                onClick={() => setPageNumber(page => page + 1)}
                disabled={pageNumber === 4}>
                Next Page
        </button>

---

## Infinite Queries
-useInfiniteQuery hook

        const fetchColors = ({ pageParam = 1 }) => {
        return axios.get(`http://localhost:4000/colors?_limit=2&_page=${pageParam}`)
        }

        const {
                isLoading,
                isError,
                error,
                data,
                fetchNextPage,
                hasNextPage,
                isFetching,
                isFetchingNextPage
        } = useInfiniteQuery(['colors'], fetchColors, {
        getNextPageParam: (_lastPage, pages) => {
                if (pages.length < 4) {
                        //hasNextPage = true
                        return pages.length + 1
                } else {
                        //hasNextPage = false
                        return undefined
                }
        }
        })

- fetchNextPage and hasNextPage, are usefull for the JSX they indicate a state for hasNextPage and a function to call for fetchNextPage

        <button onClick={() => fetchNextPage()} disabled={!hasNextPage}>
                Load more
        </button>

---

## Mutations
-post request
-import { useMutation } from 'react-query'