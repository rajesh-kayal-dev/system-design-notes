# API Design Basics

## 1. What Is an API?

**An API is a way for two software systems to communicate.**

For example, a React app can call a backend API to get a user's tasks. The backend returns the data, and the app displays it.

## 2. How Does an API Request Work?

1. The client sends a request to an API endpoint.
2. The backend checks the request and runs the required logic.
3. The backend may read or save data in a database.
4. The backend sends a response, often in JSON format.

## 3. What Is a REST API?

A REST API commonly uses URLs to identify resources and HTTP methods to describe what action to perform.

For example, a task application might use these endpoints:

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/tasks` | Get all tasks |
| `GET` | `/tasks/123` | Get one task |
| `POST` | `/tasks` | Create a task |
| `PATCH` | `/tasks/123` | Update part of a task |
| `DELETE` | `/tasks/123` | Delete a task |

The ID `123` is an example. Real IDs are assigned by the application.

## 4. What Is Inside a Request?

An API request can include:

- **Method:** The action, such as `GET` or `POST`.
- **URL:** The endpoint being called.
- **Headers:** Extra information, such as an access token or content type.
- **Path parameter:** A value in the URL, such as `123` in `/tasks/123`.
- **Query parameter:** An optional value used to filter or paginate results, such as `/tasks?page=2`.
- **Body:** Data sent with a request, commonly when creating or updating something.

Not every request needs every part.

## 5. What Is a Response?

The server response usually contains:

- **Status code:** Tells whether the request succeeded or failed.
- **Response body:** The returned data or an error message.

Common status codes:

| Code | Meaning |
|---|---|
| `200 OK` | The request succeeded. |
| `201 Created` | A new resource was created. |
| `400 Bad Request` | The request is invalid. |
| `401 Unauthorized` | Authentication is missing or invalid. |
| `403 Forbidden` | The user is not allowed to perform the action. |
| `404 Not Found` | The requested resource does not exist. |
| `500 Internal Server Error` | An unexpected server error occurred. |

## 6. What Is JSON?

JSON is a common format for sending structured data between a client and a server.

Example response:

```json
{
  "id": "123",
  "title": "Learn API design",
  "completed": false
}
```

The client can read these fields and show the task to the user.

## 7. Good API Design Practices

- Use clear, consistent endpoint names, usually nouns such as `/users` and `/tasks`.
- Use HTTP methods for the action instead of putting actions in every URL.
- Validate incoming data on the backend.
- Check authentication and permissions when required.
- Return useful status codes and clear error messages.
- Use pagination when a list can become large.
- Keep request and response formats consistent.

## Quick Revision

- An API lets software systems communicate.
- An endpoint is a URL where a resource or action can be accessed.
- HTTP methods describe the type of operation.
- Status codes describe the result of a request.
- JSON is a common format for API data.
- Good APIs are consistent, validated, secure, and easy to understand.

## Related Notes

- [System Design Basics](./system-design-basics.md)
- [Client-Server Architecture](./client-server.md)
- [API Design](./api-design.md)
- [Networking](./networking.md)
