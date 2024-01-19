# Ultra API Tester

## Overview

This repository contains the source code for testing the Mining Blast Design Microservice API, which provides functionalities for creating, modifying, and managing mining blast designs. The API is built using Express and follows the OpenAPI 3.0.0 specification.

## Actions

- Wake up Ultra
``` Uses a magnet-link to open Ultra App```
- Get supported systems
Retrieves all supported systems ```GET designs/systems```
- Get all verification rules
Retrieves all verification rules for all systems ```GET designs/verification-rules```
- Get verification rules
Retrieves verification rules for specific system ```GET designs/verification-rules/<systemName>```
- Use template to create me a design
Uses an specific object to create a new design ```GET designs/template```
```
const body = {
    name: "QuarryDesign",
    designPattern: "square",
    rowsPerDesign: 1,
    holesPerRow: 5,
    detsPerDeck: 2,
    decksPerHole: 3,
    burden: 0.800,
    spacing: 0.800,
    holeDiameter: 5.637,
    holeDepth: 5.5,
    holeBearing: 2.58,
    holeAngle: 90.5,
  };
```
- Verify design
Using the design id created in /designs/template verifies  ```GET designs/<designId>/verify```
- Start upload design via ultra task
Start the upload process via tasks
- Get tasks status
Return all the tasks status

## Installation

Requires [Node.js](https://nodejs.org/) v18+ to run.

```sh
yarn
yarn dev
```

