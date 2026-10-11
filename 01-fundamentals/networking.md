# Networking Basics

## 1. What Is Networking?

**Networking is how computers and servers communicate and share information.**

For example, when you open a website, your browser sends a request to a server. The server sends a response back.

## 2. How Does a Website Request Work?

A simple flow looks like this:

1. You enter a website address in your browser.
2. DNS finds the server's IP address.
3. The browser connects to the server.
4. The browser sends an HTTP request.
5. The server processes the request and sends a response.
6. The browser displays the result.

## 3. Important Networking Terms

| Term | Simple meaning |
|---|---|
| **IP address** | An address used to identify a device on a network. |
| **DNS** | Finds the IP address for a domain name, such as `google.com`. |
| **Client** | The device or app that sends a request. |
| **Server** | The computer or service that receives and handles requests. |
| **Protocol** | A set of rules that devices follow to communicate. |
| **Port** | A numbered entry point used by a service on a computer. |

## 4. HTTP and HTTPS

**HTTP** is a protocol used by clients and servers to exchange web requests and responses.

**HTTPS** is HTTP protected with TLS encryption. It helps protect information while it travels between the client and server.

Use HTTPS for websites and APIs, especially when sending login details or personal data.

## 5. TCP and UDP

Both TCP and UDP help devices send data across a network, but they work differently.

- **TCP:** Checks that data arrives in order and handles lost data by sending it again. It is used by common web traffic.
- **UDP:** Sends data with less delivery checking. It can be useful when speed matters, such as some live audio or video traffic.

Remember: TCP focuses on reliable delivery; UDP has less delivery overhead.

## 6. Latency and Bandwidth

- **Latency:** How long data takes to travel between two points.
- **Bandwidth:** How much data a connection can carry in a given amount of time.

A connection can have high bandwidth but still have high latency. They describe different things.

## 7. Why Networking Matters in System Design

Networking affects how quickly users receive responses, how services communicate, and how securely data travels.

When designing a system, consider network delays, connection failures, HTTPS, and communication between services.

## Quick Revision

- DNS finds the IP address for a domain name.
- HTTP defines how web requests and responses are exchanged.
- HTTPS protects HTTP traffic using TLS encryption.
- TCP provides reliable, ordered delivery; UDP has less delivery overhead.
- Latency is delay; bandwidth is data-carrying capacity.

## Related Notes

- [System Design Basics](./system-design-basics.md)
- [Client-Server Architecture](./client-server.md)
- [API Design](./api-design.md)
- [Networking](./networking.md)