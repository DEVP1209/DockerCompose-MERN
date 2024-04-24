1. Dockerfile in  ``` client/Dockerfile ```
    ```Dockerfile 
    FROM node:14-slim
    WORKDIR /usr/src/app 
    COPY ./package.json ./ 
    COPY ./yarn.lock ./ 
    RUN yarn install 
    COPY . . 
    EXPOSE 3000 
    CMD [ "yarn", "start" ] 
    ```
        This Dockerfile, located in the client directory, sets up an environment for a client-side application. It begins by using the Node.js 14 slim image as the base. Then, it establishes /usr/src/app as the working directory. Dependencies like package.json and yarn.lock are copied, followed by installing dependencies with yarn install. The application source code is copied over, and port 3000 is exposed. Finally, the default command to start the app is set as yarn start.
    <b>Explanation</b>
    1. **Base Image**: Uses Node.js 14 slim image.
    2. **Working Directory**: Sets work directory to `/usr/src/app`.
    3. **Copy Dependencies**: Copies `package.json` and `yarn.lock`.
    4. **Install Dependencies**: Runs `yarn install`.
    5. **Copy Application**: Copies source code.
    6. **Expose Port**: Exposes port 3000.
    7. **Default Command**: Starts app with `yarn start`.

2. Dockerfile in ``` server/Dockerfile ``` 
    ```Dockerfile 
    FROM node:14-slim
    WORKDIR /usr/src/app
    COPY ./package.json ./
    COPY ./yarn.lock ./
    RUN yarn install
    COPY . .
    EXPOSE 5500
    CMD [ "index.js" ] 
    ```
        This Dockerfile, located in the server directory, configures an environment for a server-side application. Similar to the client Dockerfile, it starts with the Node.js 14 slim image and sets the working directory to /usr/src/app. It copies over package.json and yarn.lock, installs dependencies, and copies the application code. Port 5500 is exposed, and the default command to run is index.js, assuming it's the entry point of the application.
    <b>Explanation</b>
    1. **Base Image**: Utilizes Node.js version 14 slim image.
    2. **Working Directory**: Sets the working directory inside the container to `/usr/src/app`.
    3. **Copy Dependencies**: Copies `package.json` and `yarn.lock` files from the host's current directory to the container's working directory.
    4. **Install Dependencies**: Executes `yarn install` inside the container to install dependencies specified in `package.json`, ensuring consistency using `yarn.lock`.
    5. **Copy Application**: Copies the entire contents of the host's current directory (including application source code) into the container's working directory.
    6. **Expose Port**: Exposes port 5500 on the container, indicating that the application inside the container will listen on this port.
    7. **Default Command**: Specifies `index.js` as the command to run when the container starts. This assumes that `index.js` is the entry point of the application and will be executed using Node.js.

3. ``` docker-compose.yml ``` in the root directory of the project.
    ```yaml
    version: "3"
    services:
    react-app:
        image: react-app
        stdin_open: true
        ports: 
        - "3000:3000"
        networks:
        - mern-app
    api-server:
        image: api-server
        ports:
        - "5500:5500"
        networks:
        - mern-app
        depends_on:
        - mongo
    mongo:
        image: mongo
        ports:
        - "27017:27017"
        networks:
        - mern-app
        volumes:
        - mongo-data:/data/db
    networks:
    mern-app:
        driver: bridge
    volumes:
    mongo-data:
        driver: local
    ```
        The docker-compose.yml file orchestrates the containers for the entire project. It defines three services: react-app, api-server, and mongo. Each service specifies its image, exposed ports, and network configuration. Additionally, it sets up a custom network named mern-app to facilitate communication between containers. A volume named mongo-data is used to persist MongoDB data.
    <b>Explanation</b>
  - **version**: Specifies the version of Docker Compose being used (version 3 in this case).
  
  - **services**: Defines individual containers/services in the application.
  
    - **react-app**: Defines a service named `react-app`. It uses an image named `react-app`, exposes port 3000, and connects it to the `mern-app` network.
  
    - **api-server**: Defines a service named `api-server`. It uses an image named `api-server`, exposes port 5000, and connects it to the `mern-app` network. It also depends on the `mongo` service.
  
    - **mongo**: Defines a service named `mongo`. It uses the official MongoDB image, exposes port 27017, connects it to the `mern-app` network, and mounts a volume named `mongo-data` to persist data.
  
  - **networks**: Defines the network named `mern-app` with the `bridge` driver. This allows containers within the same network to communicate with each other.
  
  - **volumes**: Defines a volume named `mongo-data` with the `local` driver, used to persist MongoDB data outside of the container.