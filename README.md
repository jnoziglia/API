# API

API or Accurate Planetary Information is a web application that allows users to view information about the planets in the Star Wars universe. The application was built using Vue 3 and Vite and it consumes the [SWAPI](https://swapi.dev/) API (specifically the [planets](https://swapi.dev/api/planets/) endpoint).
This application is part of a development challenge, and so the scope of the project is limited to the following:

* Display a list of planets
* Display a planet's details
* Sort the list of planets by name
* Paginate the list of planets

There are improvements that can be made to the application but due to time constraints, they were not implemented. Also, since this is a development challenge, the application is not production-ready. Therefore, in order to run the application, you will need to follow the instructions below.


## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

## Challenge questions:

1. What's a closure? Where in the code is there a closure?

    * A closure is a function that has access to the parent scope, even after the parent function has ended. In the code, closures can be found in the functions that capture reactive properties, such as the updateList function in the PlanetsList component.

2. Which are the potential side-effects in any function? Could you point out any of these cases in your code? Are they expected? Can they be avoided?
    
    * Side effects are changes that are made to the state of the application outside of the function scope. In the code, side effects can be found in the updateList function in the PlanetsList component. The function updates the planets list, which is a reactive property. Side effects are expected in this case, and they cannot be avoided.
