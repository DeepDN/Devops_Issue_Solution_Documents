# Creact Sample React and nodejs app


<aside>
  create a sample app with ReactJS for the frontend and NestJS for the backend in separate repositories:

</aside>

### **Frontend**

1. Create a new repository on GitHub for the frontend app.
2. Clone the repository to your local machine.
3. Install Node.js and npm on your local machine.
4. Install the Create React App tool globally by running **`npm install -g create-react-app`**.
5. Initialize a new React app using Create React App by running **`npx create-react-app <app-name>`**.
eg:-  
npx create-react-app my-app

6. Change into the app directory by running **`cd <app-name>`**.
7. Install the axios package by running **`npm install axios`**.
8. Update the App.js file to make API calls to the backend server using axios.

```

import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    axios.get('/api/data').then((response) => {
      setData(response.data);
    });
  }, []);

  if (!data) {
    return <div>Loading...</div>;
  }

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.description}</p>
    </div>
  );
}

export default App;

```

1. Add a **`.env`** file to the root of your app and add the following line: **`REACT_APP_API_URL=http://localhost:3001`**.
2. Start the development server by running **`npm start`**.
3. Test the app by visiting [**http://localhost:3000**](http://localhost:3000/) in your web browser.

### **Backend**

1. Clone the repository to your local machine.
2. Install Node.js and npm on your local machine.
3. Install the Nest CLI globally by running **`npm install -g @nestjs/cli`**.
    1. Create a new repository on GitHub for the backend app.
4. Initialize a new Nest app by running **`nest new <app-name>`**.
5. Update the app.module.ts file to enable CORS and add a sample endpoint.

```

import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

```

import { Controller, Get } from '@nestjs/common';

@Controller('api')
export class AppController {
  @Get('data')
  getData() {
    return {
      title: 'Hello, world!',
      description: 'This is a sample response from the backend server.',
    };
  }
}

```

1. Start the development server by running **`npm run start:dev`**.
2. Test the backend endpoint by visiting [**http://localhost:3001/api/data**](http://localhost:3001/api/data) in your web browser.

Now that you have separate repositories for your frontend and backend, you can deploy them separately to your hosting provider of choice. For example, you could use Netlify for the frontend and Heroku for the backend. When you deploy the frontend, you'll need to update the API URL in the **`.env`** file to point to the URL of your deployed backend server.