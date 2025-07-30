# DroneSym Docker Setup & Troubleshooting Guide

## 1. Clone and Prepare the Repository
- Clone the repository and ensure you have all subfolders, especially `dronedb/dronesym/` with BSON files.

## 2. Docker Compose Configuration
- Use the provided `docker-compose.yaml` with services for `mongo`, `node`, `angular`, and `flask`.
- Ensure the `node` service uses the environment variable:
  ```
  MONGO_URL=mongodb://mongo:27017/dronesym
  ```
- MongoDB service should use:
  ```
  entrypoint: ["/usr/bin/mongod", "--bind_ip_all","--replSet", "rs"]
  ```

## 3. Angular Frontend Fixes
- In `dronesym-frontend/package.json`, update the start script to:
  ```
  "start": "ng serve --host 0.0.0.0"
  ```
- Downgrade `@agm/core` to a version compatible with Angular 7:
  ```
  "@agm/core": "1.0.0-beta.2"
  ```

## 4. Build and Start All Services
- Run:
  ```
  docker-compose up --build
  ```

## 5. MongoDB Replica Set Initialization
- Enter the MongoDB container shell:
  ```
  docker exec -it dronesym-mongo-1 mongosh
  ```
- Initiate the replica set:
  ```js
  rs.initiate()
  ```
- Wait until the node becomes PRIMARY (`rs.status()`).

## 6. Import Initial User Data
- In your system terminal (not mongosh), run:
  ```
  docker cp dronedb/dronesym/users.bson dronesym-mongo-1:/users.bson
  docker exec -it dronesym-mongo-1 mongorestore --db dronesym --collection users /users.bson
  ```
- Verify import:
  ```
  docker exec -it dronesym-mongo-1 mongosh
  use dronesym
  db.users.find().pretty()
  ```

## 7. Restart Backend (if needed)
- If the backend had connection issues, restart it:
  ```
  docker-compose restart node
  ```

## 8. Login to the Dashboard
- Access the frontend at http://localhost:4200
- Use default credentials:
  - Admin: `admin` / `admin`
  - User: `icarus` / `icarus`

---

## Troubleshooting Tips

- **White screen on frontend:** Check browser console for errors, ensure correct `@agm/core` version.
- **Login fails with "Invalid username":** Ensure users are imported into the correct database and collection.
- **MongoDB errors about PRIMARY/SECONDARY:** Re-initiate the replica set and wait for PRIMARY state.
- **Backend cannot connect to MongoDB:** Check `MONGO_URL` and ensure both containers are on the same Docker network.

---

Keep this doc for future reference to quickly set up or debug DroneSym with Docker!
