# Axios Isomorphic Routing Controller

- Author: [Ganesh Rathinavel](https://www.linkedin.com/in/ganeshrvel "Ganesh Rathinavel")
- License: [MIT](https://github.com/ganeshrvel/axios-isomorphic-routing/blob/master/LICENSE "MIT")
- Repo URL: [https://github.com/ganeshrvel/axios-isomorphic-routing](https://github.com/ganeshrvel/axios-isomorphic-routing "https://github.com/ganeshrvel/axios-isomorphic-routing")
- Contacts: ganeshrvel@outlook.com
- Requirements: Node.js v6+ or higher, npm

##### Advanced Router Controller for Vue.js, React.js & Node.js Isomorphic apps

#### Why **Axios Isomorphic Routing Controller**?
> Axios is a Promise-based HTTP client for JavaScript which can be used for both Frontend and Backend Applications.
> Although the library is very powerful we often run into situations where we wish it'd offered us more and that's the motivation behind this project.

### Features
- Cache for both server and client side.
- LRU for the server and localforage for the client.
- Warm cache which refreshes at a regular interval of time.
- Independent routing for both Server and client.
- Advanced routing with predefined or dynamic endpoints and action methods.
- Caching available for every endpoint, method, query string, parameters and body data.
- Purge and rebuild the cache on the fly.


### Installation
```shell
$ git clone --depth 1 --single-branch --branch master https://github.com/ganeshrvel/axios-isomorphic-routing.git
    
$ cd axios-isomorphic-routing
```
	
### Run
```shell
$ npm install
    
$ npm run start
```

- App launch address: [http://localhost:3001/](http://localhost:3001/ "http://localhost:3001/")

- For Server side Axios routing demo: [http://localhost:3001/demo/server](http://localhost:3001/demo/server "http://localhost:3001/demo/server")

- For Client side Axios routing demo: Fire up the browser console.


### Structure

    |-- axios-isomorphic-routing
        |-- server.js
        |-- express
        |   |-- routes.js
        |   |-- mock-data
        |-- src
            |-- index.html
            |-- index.js
            |-- api
            |   |-- consts.js
            |   |-- end-points.js
            |   |-- index.js
            |   |-- services
            |       |-- client-api.js
            |       |-- index.js
            |       |-- server-api.js
            |-- examples
                |-- client.js
                |-- server.js

### General Information

**server.js**: Node entry point

**express/routes.js**:
Server endpoint router. Codes for GET/POST/PUT/DELETE routing is written here.
The JSON values are fetched from 'express/mock-data/*.json' files.

**src/index.html**: Template file.
**src/index.js**: Client-side entry point.

**src/api/const.js**: Configure the constant values for the app here.
**src/api/end-points.js**: Endpoints are mapped here. The app later fetches the endpoint data from here.
**src/api/end-points.js**: Main controller file.
**src/api/services**: Server and client data flow routing and controller

**src/examples**: Demo for both server and client side Axios routing and method usage.


### Usage
    // import make() method from api
    const {make} = require('../src/api')
    
```javascript
 /**
   * @param request: The end point to hit; Find the endpoint reference from 'end-points.js'
   * @param queryString: Query string or parameters to hit the API,
   * @param data: Body data for POST and PUT action types; No data is accepted by GET or DELETE
   * @param allowCache: Allow the caching of output data; It will be different for different query string parameters. Or read from an already cached record.
   * @param rebuildCache: Purge the old cache and fetch the new data
   * @param allowDataCache: Allow the caching of the POST body data.
   *  Eg: Various combinations of post parameters along with the respective output can be cached for an ecommerce/classified website options filter. This saves a massive amount of API calls by caching previous records of the filter combinations.
   * @param ...args: {customEndPoint}
   * customEndPoint => Dynamically generated end points along with url and method
   * @returns Promise
   */
   
  make({
    request,
    queryString,
    data,
    allowCache,
    rebuildCache,
    allowDataCache,
    customEndPoint,
  }).
  then(response => {
  })
```

**param: allowCache**

```javascript
/**
   * Supported action types: GET/POST/PUT/DELETE
   * */
  
  make({
    request: 'GET_CARS',//will be translated as 'cars/'(go through 'end-points.js' for more)
    queryString: '?start=2008&end=2010',//will be translated as 'cars/?start=2008&end=2010'
    data: {}, //No data is accepted for GET method
    allowCache: true,
    rebuildCache: false,
    allowDataCache: false,//Only availble for POST
    customEndPoint: null,
  }).then(response => {
    console.log(response)
  })
  
  //Identical call to demonstrate the working of cache
  setTimeout(() => {
      make({
        request: 'GET_CARS',
        queryString: '?start=2008&end=2010',
        data: {},
        allowCache: true,
        rebuildCache: false,
        allowDataCache: false,
        customEndPoint: null,
      }).then(response => {
        console.log(response)
      })
    },
    4000)
  
  //Different query string while the other parameters are same as above.
  //The cache will be different for different query string parameters
  setTimeout(() => {
      make({
        request: 'GET_CARS',
        queryString: '?start=1950&end=2000', //Query string is changed here
        data: {},
        allowCache: true,
        rebuildCache: false,
        allowDataCache: false,
        customEndPoint: null,
      }).then(response => {
	  	console.log(response)
      })
    },
    6000)
```

**param: allowDataCache**

```javascript
/**
   * Supported action type: POST
   * */
  
  //Begin caching of body data
  make({
    request: 'POST_TEST2',//will be translated as 'test_cache2/'(go through 'end-points.js' for more)
    queryString: '?param1=12345&param2=abcdef',//will be translated as 'test_cache2/?param1=12345&param2=abcdef'
    data: {'planet1': 'mars', 'planet2': 'jupiter'},
    allowCache: true,
    rebuildCache: false,
    allowDataCache: true,
    customEndPoint: null,
  }).then(response => {
    console.log(response)
  })
  
  // The above data is cached for the particular POST parameters
  // and the below function will refetch the same from the cache generated from the above code
  setTimeout(() => {
    make({
      request: 'POST_TEST2',
      queryString: '?param1=12345&param2=abcdef',
      data: {'planet1': 'mars', 'planet2': 'jupiter'}, //identical post body data as the above
      allowCache: true,
      rebuildCache: false,
      allowDataCache: true,
      customEndPoint: null,
    }).then(response => {
      console.log(response)
    })
  }, 4000)
  
  //Different post body data while the other parameters are same as above.
  //The cache will be different for different post parameters
  setTimeout(() => {
    make({
      request: 'POST_TEST2',
      queryString: '?param1=12345&param2=abcdef',
      data: {'planet1': 'earth', 'planet2': 'venus'}, //different post body data from the above
      allowCache: true,
      rebuildCache: false,
      allowDataCache: true,
      customEndPoint: null,
    }).then(response => {
      console.log(response)
    })
  }, 6000)
```

```javascript
/**
   * Supported action types: GET/POST/PUT/DELETE
   */
   
    //Few random end points
    //Note: apiUrl and method has to be supplied to customEndPoint as an object
  let endPointDictionary = [
      {
        apiUrl: 'try_put',
        method: 'PUT',
      },
      {
        apiUrl: 'try_delete',
        method: 'DELETE',
      },
    ]
  
  make({
    request: 'CUSTOM_ENDPOINT_1',
    queryString: '?param1=12345&param2=abcdef',
    data: {},
    allowCache: true,
    rebuildCache: false,
    allowDataCache: false,
    customEndPoint: endPointDictionary[0],
  }).then(response => {
    console.log(response)
  })
  
  make({
    request: 'CUSTOM_ENDPOINT_2',
    queryString: '?param1=12345&param2=abcdef',
    data: {},
    allowCache: true,
    rebuildCache: false,
    allowDataCache: false,
    customEndPoint: endPointDictionary[1],
  }).then(response => {
   console.log(response)
  })

```

**Warm cache**:
> 
- 'warmCacheList' as defined in: 'src/api/consts.js'
- Warm cache refresh list;
- Allowed Methods: GET
- These are loaded when the server fires up and keeps refreshing every 'WARM_CACHE_REFRESH_TIME' seconds.
- The list will keep getting updated at a regular interval.
- Usage: Website wide data caching.
- Scope: Server only. Client can't access this data.
- To over ride the Warm cache allowCache can be set false while making the API call.
- Important: DO NOT add the APIs used for fetching personal user data.


### Dummy API end-points:

    http://localhost:3001/cars?start=2005&end=2010
    Method type: GET
    
    http://localhost:3001/cities
    Method type: POST
    POST data: {"countries": "China", "limit": 2}
    
    http://localhost:3001/try_put
    Method type: PUT
    POST data: {"param1": "12345", "param2": "abcdefg"}
    
    http://localhost:3001/try_delete
    Method type: DELETE
    POST data: {"param1": "12345", "param2": "abcdefg"}
    
    http://localhost:3001/test_cache1?param1=animals&param2=plants
    Method type: GET
    
    http://localhost:3001/test_cache2?param1=animals&param2=plants
    Method type: POST
    POST data: {"planet1": "mars", "planet2": "jupiter"}

### Changelogs:
##### v1.0:
- Compiled README.md
- Cleaned a few lines of code and documentation.

##### v0.1:
- Development
- Initial commit

### Dependencies
- Axios
- bluebird
- localforage
- lru-cache


 ### Contribute
- Fork the repo and create your branch from master.
- Update the documentation if needed.
- Issue a pull request!

When you submit code changes, your submissions are understood to be under the same [MIT License](https://github.com/ganeshrvel/axios-isomorphic-routing/blob/master/LICENSE "MIT License") that covers the project. Feel free to contact the maintainers if that's a concern.


### Buy me a coffee
Help me keep the app FREE and open for all.
Paypal me: [paypal.me/ganeshrvel](https://paypal.me/ganeshrvel "paypal.me/ganeshrvel")


### Contacts
Please feel free to contact me at ganeshrvel@outlook.com


### License
axios-isomorphic-routing | Advanced Router Controller for Vue.js, React.js & Node.js Isomorphic apps is released under [MIT License](https://github.com/ganeshrvel/axios-isomorphic-routing/blob/master/LICENSE "MIT License").

Copyright © 2018-Present Ganesh Rathinavel
