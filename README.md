
# DWP Cinema Tickets Tech Test


This is my version of the DWP 'Cinema Tickets' tech test - acceptance criteria can be found in `ACCEPTANCE_CRITERIA.MD`

### Methodology
Requests are made to the `/tickets` endpoint (from an imaginary external frontend, like a cinema website)

As only one final 'ticket request' is made to the external services, the received requests are merged into a singular object in `combineTicketRequests`

Requests made to the `TicketService` are tested against validation for both `accountID` and the `ticketTypeRequest` itself.

These validators test against the acceptance criteria (AC) detailed in `ACCEPTANCE_CRITERIA.MD`, ensuring that requests meet business criteria and are the correct type / are defined.

If the request / accountID aren't valid, an `HttpError` is thrown, preventing calls being made to external services with invalid parameters. This is a custom error so a more specific status code can be exposed to a consumer

If they are valid - payment amount and seat amount are calculated by helper methods, before calling the respective 'external' services.

I used the `Sinon` testing / dependency injection library to mock these external services, alongside `Mocha` and `Chai` in my test suite. I also used Postman and Newman to perform integration tests. Both of these test types are ran in my  Github Actions CI pipeline.

I also tried to stick (albeit loosely) to a more 'functional' programming style rather than the OO style outlined in the template - as I find OO JavaScript can be a bit finicky without using TypeScript.

I used the `Standard` linting library due to its minimal configuration and quick setup (also because it works better than the `ESLint` setup I initally tried to use, more on that in [Challenges](#challenges))

### Assumptions

- Only one infant can sit on each adults lap (therefore the number of infants cannot exceed the number of adults)

- 'The ticket purchaser declares how many and what type of tickets they want to buy' - I assumed gathering this information would be done by a downstream frontend service, therefore not a responsibility of this application.

## Running the app
### Prerequisites

- Docker
- NodeJS

### How to run locally
The app can be executed using these commands in the root of the project:

```
npm start (to run the service in 'prod' mode)
npm run dev (run the service in dev mode)
```

OR

```

docker compose up -d (run the app using docker compose)

```

## Running the Tests

'End to end' tests can be ran using postman, using  `npm run test:postman` - the app needs to be running for these to execute.

Unit tests can be ran by using `npm test:unit`

Linting can be ran using `npm test:standard`

All of these tests, along with static code analysis via Sonarqube, are ran in my GitHub Actions CI pipeline.

## Challenges

### Linting


I spent *quite a lot of time* trying to import and use the [DWP ESLint Config](https://www.npmjs.com/package/@dwp/eslint-config-base) with ESLint, but ran into lots of errors as, as standard, the config is built for CommonJS. I refactored the imports / exports to use `require` rather than `import`, and as I was reviewing my changes I realised I'd modified the `thirdparty` code. As a result of this, I reverted back to the original template code. This whole process could've been avoided if I was more mindful of the acceptance criteria, and I could've implemented some of the below [improvements](#potential-improvements)

### Using functional (ish) design over object-oriented

My approach with vanilla Node, thus far, is to eschew using Object Oriented design, as I find it makes less sense when loosely typed. This, of course, is remedied by using `TypeScript`, which I didn't use in this solution as it was to be written in `JavaScript`, and I didn't want to give the reviewer the task of reviewing transpiled code. This meant my final solution uses a hodge-podge of more functionally oriented and OO code, and doesn't have a definite style.


## Potential Improvements

- Logging - Simply because of time constraints, I didn't employ a logging library into my solution. I'd ideally use `bunyan` or `npmlogger`.
- Use docker containers rather than a Node process in my CI pipeline while running e2e tests. Slightly pedantic, but it'd be nice to run the e2e tests against the dockerised version of the app, as that's how I run them locally (and how the app would be hosted in a cloud environment).

- Typescript - When I start a new project, I tend to use TypeScript (mainly for readability purposes and better IntelliSense), but this project was to be completed using JavaScript.

- Remove all OO code in favour of functional - Mixing OO and functional detracts from a cohesive style throughout the codebase, ideally I'd refactor to make a.) my existing code align with FP, and b.) refactor all OO code into more functional code

- DWP ESLint config - I spent an inordinate amount of time trying to get the DWP ESLint config, and ESLint working with the ECMAScript. I'd ideally like to use the DWP config to further align with DWP engineering standards.