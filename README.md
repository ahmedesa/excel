## Excel Export Microservice using Golang

### Overview

The  project is to create a microservice in Golang that will handle the exporting of data from a database into Excel sheets . The microservice will be production-ready, incorporating logging, monitoring (e.g., Prometheus, Grafana), benchmarking, and full testing. It will be containerized using Docker and Docker Compose, with the possibility of deployment using Kubernetes. Additionally, the microservice will be open-sourced on GitHub for community use and collaboration.

### Functional Requirements

#### Service Endpoints

**HTTP Endpoint: `/export`**

##### Request

- **Headers:**
  - `Authorization: Bearer <TOKEN>`
  
- **Body:**
  ```json
  {
    "type": "userExport",
    "returnType": "url",
    "fileName": "myexport.xlsx",
    "variables": {
      "x": 1,
      "y": 2
    }
  }
  ```

**Parameters:**
- `type`: The type of export, which corresponds to a predefined SQL query to be run.
- `returnType`: Specifies how the exported file should be returned. Possible values:
  - `url`: Returns the URL of the exported file.
  - `email`: Sends the exported file to a specified email address.
- `fileName`: The name of the exported Excel file.
- `variables`: A JSON object containing the variables needed for the SQL query.

When `returnType` is `email`, an additional key is required:
- `email`: The email address to which the file should be sent.

##### Response
The microservice will respond with different payloads based on `returnType`:
- For `returnType: url`:
  ```json
  {
    "status": "success",
    "url": "http://example.com/path/to/download/myexport.xlsx"
  }
  ```
- For `returnType: email`:
  ```json
  {
    "status": "success",
    "message": "The file has been sent to the provided email."
  }
  ```

### Key Features

#### Rate Limiting and Throttling

Implement rate limiting and throttling to prevent abuse and ensure fair usage of the service:
- Use middleware to limit the number of requests per user/IP within a certain time frame (e.g., `golang.org/x/time/rate` package).
- Throttle requests by queuing them if the service is under heavy load, ensuring a stable response time and preventing system overload.

#### File Storage

- Store generated files on AWS S3 or other cloud storage services for scalability and reliability.
- Ensure the files are publicly accessible via URLs with proper access control mechanisms.

#### Health Checks and Resilience

- Implement health check endpoints, following best practices for liveness and readiness probes, especially if deploying via Kubernetes.
- Design the service to handle retries and gracefully recover from failures:
  - Periodic self-checks on key components.
  - Graceful shutdown and recovery mechanisms.

#### Configuration Management

- Use environment variables for configuration to keep sensitive information secure.
- Employ a secrets management service (e.g., AWS Secrets Manager, HashiCorp Vault) to handle sensitive data like API keys, database credentials, and other configurations securely.

### Technical Considerations

#### Database Access

- The service will require access to the same database as the existing Laravel application to perform the exports.

#### Scalability

- Design the service to handle large datasets efficiently, mitigating timeout issues experienced in the current system.

#### Security Enhancements

- Send the Authorization key in the headers.
- Ensure that sensitive data in the queries is properly sanitized to prevent SQL injection attacks.
- Use HTTPS for secure data transit.
