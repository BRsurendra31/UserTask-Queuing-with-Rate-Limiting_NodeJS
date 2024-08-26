# UserTask-Queuing-with-Rate-Limiting_NodeJS

## Overview

This project is a Node.js API cluster that implements rate limiting and task queueing to handle user tasks efficiently. The system includes two replica sets to handle requests and utilizes Redis for rate limiting and queue management.

## Features

- **Rate Limiting**: Limits each user to one task per second and up to 20 tasks per minute.
- **Task Queueing**: Queues tasks if rate limits are exceeded and processes them accordingly.
- **Logging**: Logs task completions with timestamps to a file.
- **Clustering**: Uses Node.js clustering to improve performance and reliability.

## Prerequisites

- **Node.js** (v14 or higher)
- **npm** (v6 or higher)
- **Redis** (latest version)

## Setup Instructions

1. **Clone the Repository**:
    ```bash
    git clone <repo_url>
    cd UserTask-Queuing-with-Rate-Limiting_NodeJS
    ```

2. **Install Dependencies**:
    ```bash
    npm install
    ```

3. **Start Redis Server**:
    ```bash
    redis-server
    ```

4. **Start the Node.js Application**:
    ```bash
    node src/index.js
    ```
    - This will start the application on port `3000` and create two worker processes.

## API Endpoint

### **POST** `/api/v1/task`

**Request Body**:
```json
{
  "user_id": "test_user_1"
}
```
<br>

- This endpoint processes a task for the given user_id.
- The task is subject to rate limiting (1 task per second and 20 tasks per minute). If the rate limit is exceeded, the task will be queued.

**Responses:**
- **200 OK:** Task processed successfully.
```json
{
  "message": "Task processed."
}
```
<br>

- **429 Too Many Requests:** Rate limit exceeded, task queued.

```json
{
  "message": "Rate limit exceeded, task queued."
}
```

## Testing

### Single Request

1. **Open Postman**.
2. **Create a New Request**:
   - Click on the "New" button or `+` tab to open a new request tab.
3. **Set the Request Type**:
   - Change the request method to `POST` using the dropdown next to the URL field.
4. **Enter the Request URL**:
   - In the URL field, enter `http://localhost:3000/api/v1/task`.
5. **Set the Headers**:
   - Go to the `Headers` tab below the URL field.
   - Add a new header:
     - **Key**: `Content-Type`
     - **Value**: `application/json`
6. **Set the Request Body**:
   - Go to the `Body` tab below the URL field.
   - Select `raw` as the body type.
   - In the adjacent dropdown, select `JSON (application/json)`.
   - Enter the following JSON:
     ```json
     {
       "user_id": "test_user_1"
     }
     ```
7. **Send the Request**:
   - Click `Send`.
   - **Expected Response**:
     ```json
     {
       "message": "Task processed."
     }
     ```


### Multiple Requests

#### Use Postman Runner

1. **Create a Collection with the Request**:
   - In Postman, create a new collection and add the request to it.

2. **Open the Postman Runner**:
   - Click on the "Runner" button at the top-left under "Runner".

3. **Select Your Collection**:
   - Choose the collection you created and select the request inside it.

4. **Set the Iteration Count**:
   - Set the iteration count to 25 or more to simulate high traffic.

5. **Click Start Run**:
   - Click `Start Run` to send multiple requests in quick succession.

#### Using cURL for Multiple Requests

```bash
for i in {1..25}; do \
  curl -X POST http://localhost:3000/api/v1/task \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test_user_1"}'; \
done
```

### Testing Rate Limiting

#### Send Rapid Requests

**Send Multiple Requests Quickly**:

To test rate limiting, send multiple requests quickly to the same `user_id`.

**Example Using cURL**:
```bash
for i in {1..10}; do \
  curl -X POST http://localhost:3000/api/v1/task \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test_user_1"}'; \
done
```

**Expected Response**:

- **For Requests Within the Rate Limit**:
  ```json
  {
    "message": "Task processed."
  }
  ``` 

**For Requests Exceeding the Limit**:
```json
{
  "message": "Rate limit exceeded, task queued."
}
```








