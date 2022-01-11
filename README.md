# Tasks

## Task 2
```javascript
//task2
!async function () {
    class Provider {
        /**
         * Gets the weather for a given city
         */
        static getWeather(city) {
            return Promise.resolve(`The weather of ${city} is Cloudy`)
        };

        /**
         * Gets the weather for a given city
         */
        static getLocalCurrency(city) {
            return Promise.resolve(`The local currency of ${city} is GBP`)
        };

        /**
         * Given Longitude and latitude, this function returns a city
         */
        static findCity(long, lat) {
            return Promise.resolve(`London`)
        };
    }

    //1) Find and print in console the city located at latitude/longitude 51.5074 and 0.1278 accordingly
    const lat = 51.5074;
    const long = 0.1278;
    const city = await Provider.findCity(long, lat);
    console.log(city);
    //2) Print in console the weather for the city located at lat/long = 51.5074 and 0.1278
    const weather = await Provider.getWeather(city);
    console.log(weather)
    //3) Print in console in one line the weather and currency for a given city (London)
    // I assume we don't have London's weather from the previous code and city is London
    Promise.all([Provider.getWeather(city), Provider.getLocalCurrency(city)])
        .then(([w, c]) => console.log(`${w}, ${c}`));
}()
```


## Task 3

### 1. Frontend part

#### a. Tools, frameworks, npm libraries:

* `Next.js` fast & reliable, nice routing, SSR + SSG, code-splitting/bundling
* `sass` lots of benefits over `css`.
* `i18next` internationalization
* `jest` testing
* `redux` state management
* `Apollo GraphQL` GraphQL client for queries, subscriptions and wonderful caching and offline fallback support.

#### b. Directory structure for source code folder:

* src
    * components
    * hooks
    * utils
    * modules
    * graphql (directory containing schemas and graphql related code)
    * pages
        * api
            * graphql.js (GraphQL server)
        * user
            * [uid].js
        * users.js
        * index.js

#### c. List of components:

* `List` generic list, preferably lazy loaded.
* `ListItem` generic list item.
* `UserProfileBadge` component containing picture, name and email of the user.
* `UserList` using `List`, `ListItem`, `UserProfileBadge`.
* `UserProfile` using `UserProfileBadge` for the upper part + details

+ Additional page components in `/pages` see point `b`

#### d. For one of the components also provide in which directory/-ies component’s file/-es will be located

* src
    * components
        * UserProfileBadge
            * UserProfileBadge.jsx
            * UserProfileBadge.test.jsx

#### e.Benefits/drawbacks comparing with traditional (not-SPA) app approach

#### Drawbacks:

* Traditional (not-SPA) are usually faster on the initial page load, and better for SEO. (This doesn't really apply when
  using SSR)

#### Benefits:

* Better decoupling from the backend.
* Reusable code.
* Subsequent resources are loaded fast.
* Effective use of caching
* Offline support
* Easy to debug (at least on Chrome)

#### f. Any other comments and suggestions

None

### 2. Backend part

#### a. Short description of API – URI, format, why needed

* `[GET] /users`
    * returns `[{avatar: string, givenName: string, familyName: string , email: string}]`
* `[GET] /users/:id`
    * returns additional info for user with `id` in addition
      to `{avatar: string, givenName: string, familyName: string , email: string}`

#### b.Framework

Express.js

#### c. Any other comments and suggestions

In case we don't end up with a GraphQL server in our Next.js project we can make it a part of the server.

## Task 4
```javascript
//task4
!async function () {
    /**
     * Error messages
     * @type {Readonly<{NO_STOCK: string, INCORRECT_DETAILS: string}>}
     */
    const ErrorMessages = Object.freeze({
        NO_STOCK: 'No stock has been found',
        INCORRECT_DETAILS: 'Incorrect details have been entered'
    });

    /**
     * Object containing the handler functions for each state
     * @type {Readonly<{success: (function(): Promise<{title: string, message: null}>), processing: (function(): Promise<unknown>), error: (function({errorCode?: *}): Promise<{title: string, message}>)}>}
     */
    const stateHandler = Object.freeze({
        success: async () => ({title: 'Order complete', message: null}),
        processing: () => new Promise((resolve) => setTimeout(resolve, 2000)),
        error: async ({errorCode = null}) => ({title: 'Error page', message: ErrorMessages[errorCode] ?? null})
    });

    /**
     * Helper function to return one value as the output out of an array of data
     * @param input {[{state: string, errorCode?: *}]}
     * @returns {Promise<{title: string, message?: *}>}
     */
    const getProcessingPage = async input => {
        for (const e of input) {
            const result = await stateHandler[e.state](e);
            if (result) return result;
        }
    };

    //examples:
    console.log('sample example:');
    const sampleExample = ' sample example time';
    console.time(sampleExample);
    await getProcessingPage([{state: 'processing'}, {state: 'error'}])
        .then(console.log);
    console.timeEnd(sampleExample);

    console.log('errorCode NO_STOCK example:');
    await getProcessingPage([{state: 'error', errorCode: 'NO_STOCK'}, {state: 'error', errorCode: 'INCORRECT_DETAILS'}])
        .then(console.log);

    console.log('success example:');
    await getProcessingPage([{state: 'success'}])
        .then(console.log);
}()
```

## Task 5

    01:  import React, { useEffect, useState } from 'react';
    02:  
    03:  function App() {
    04:    
    05:      var [fuel, setFeul] = useState(0);
    06:      var [alertText, setAlertTxt] = useState('Processing...');
    07:  
    08:      useEffect(() => {
    09:        fetch('https://new.world.com/fleet/121')
    10:          .then(response => response.json())
    11:          .then(json => {
    12:            setFeul(json);
    13:            console.log(json);
    14:          })
    15:      });
    16:  
    17:      useEffect(() => {
    18:        if (!fuel) setAlertTxt('Processing...'); else
    19:        if (fuel.litres > 0) setAlertTxt('Need to buy more fuel'); else
    20:        setAlertTxt('All is fine');
    21:      }, [fuel])
    22:  
    23:      return (
    24:        <div>
    25:          <h1 style={alertText == 'Need to buy more fuel'? {color:"red"}:{}}>{alertText}</h1>
    26:        </div>
    27:      );
    28:    
    29:  }
    30:  
    31:  export default App;

### Code review

* `03`: `App` ? This should probably be in its component.
* `05`: Use `const` instead of `var`. Fix typo in `setFeul` -> `setFuel`.
* `06`: Use `const` instead of `var`. Fix typo in `setAlertTxt` -> `setAlertText`.
* `09`: Extract api call into a separate function and parameterize the fleet id instead of hardcoding it and put the
  function preferably with the other API related code in the project.
* `13`: Remove console.log as it could pollute the logs for someone else.
* `14`: Handle errors ! Currently  `alertText` will be stuck with `'Processing...'` in case of an error.
* `18`: Could be replaced by `if(!fuel) return;` and
  next `setAlertText(fuel.litres > 0 ? 'Need to buy more fuel' : 'All is fine')`.
* `19`: This doesn't seem to cover the requirements that the consumed fuel should be more than 10k litres during current
  month. It doesn't check that litres > 10k and neither does it check that this is from the current month.
* `25`: Separate logic from the displayed text, instead of using `alertText` use a boolean or if needed (for multiple
  states) some constants instead of having logic based of the text content we display. This way the component will be
  more flexible in case we make further changes e.g. change the text content or display a loading icon instead
  of `Processing...` or any other changes. Also consider using i18n or something similar for getting the right text to
  display. As a side note this could be written more cleanly using https://styled-components.com/ e.g. make different
  styled components for the different states and display the right one based on some logic.
