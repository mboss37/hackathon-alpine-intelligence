
## Introduction

Designing APIs requires careful consideration of standards and conventions to ensure consistency, usability, and scalability. These guidelines provide best practices for API design, including naming conventions, versioning, pagination, and handling of requests and responses.

## API Design Conventions

### 1. Resources
- Use **nouns** (not verbs) for resource names.
- **Coarse-grained** resources are preferred over fine-grained ones.
- Use **lowercase** for resource names (e.g., `/accounts`).
- For multi-word resources, use either:
  - Lowercase (e.g., `/lineitems`) 
  - Kebab-case (e.g., `/line-items`).

### 2. Resource Types
- **Collection Resources**: Represent a group of resources (e.g., `/users`).
- **Instance Resources**: Represent a single entity (e.g., `/users/{id}`).

### 3. HTTP Methods
- **GET**: Retrieve data.
- **POST**: Create a new resource (not idempotent).
- **PUT**: Replace an existing resource (idempotent).
- **PATCH**: Update partial data of a resource.
- **DELETE**: Remove a resource.

### 4. Media Types
- Use the `Accept` header to specify the expected request format.
- Use the `Content-Type` header to define the response format.

### 5. Base URL and Versioning
- Include `/api/v1` in the base URL for versioning if not part of the subdomain (e.g., `/api/v1/orders`).

### 6. Naming Conventions
- Use **camelCase** for fields (avoid underscores).
  
### 7. Date/Time Representation
- Use **ISO8601** standard for dates (e.g., `2018-05-28T17:42:21Z`).
- Use **UTC** as the time zone.

## RAML Design Conventions

### 1. RAML Version
- Specify **RAML 1.0** for API specifications.

### 2. Schemas
- Separate schemas from the base RAML file to define the format for requests and responses.

### 3. Examples
- Always provide examples for requests and responses to improve development speed and readability.

### 4. Data Types
- Break down the code into smaller reusable components using **data types**. Separate them from the base RAML file.

### 5. Traits
- Use **traits** to define common method properties (e.g., query parameters and responses) and externalize them for reuse.

### 6. Libraries
- Group reusable declarations (e.g., data types or traits) into libraries.

## URI Structure

A well-defined URI structure ensures clarity in how APIs are organized and accessed. The recommended structure is:

```
http://[env]-[access].[company].[region]/[version]/[context]/[resource]
```

### Parts of the URI:
1. **{env}**: Optional. Specifies the API environment (e.g., `dev`, omitted for production).
2. **{access}**: Optional. Indicates public or private access (e.g., `api`).
3. **{company}**: Required. The company name (e.g., `unops`).
4. **{region}**: Required. Defines the region (e.g., `de`).
5. **{version}**: Required. Specifies the API version (e.g., `v1`).
6. **{context}**: Required. Represents the business service (e.g., `orders`).
7. **{resource}**: Optional. Identifies the specific resource (e.g., `orders/123`).

## Pagination

Use the following convention for pagination:

```http
GET /orders?offset=0&limit=50
```

- **offset**: The starting point for fetching data (default `0`).
- **limit**: The number of records to return (default `50`).

## Filtering and Sorting

### Filtering
APIs should support filtering using query parameters. For example:
```http
GET /orders?state=shipped
```

### Sorting
Use a generic `sort` query parameter to define sorting rules. For multiple fields, a comma-separated list is used, with a minus (`-`) prefix for descending order:
```http
GET /orders?sort=-date,product
```

## Partial Resources

Allow clients to request only the necessary fields by providing a `fields` query parameter:
```http
GET /orders/1?fields=date,total
```

## API Versioning Strategy

APIs should be designed with long-term stability in mind. However, changes are inevitable, and a versioning strategy is essential to manage those changes effectively.

### Major vs. Minor Versions:
- **Major versions** (e.g., `v1.0`, `v2.0`) introduce breaking changes and require consumers to adapt.
- **Minor versions** (e.g., `v1.1`, `v1.2`) introduce non-breaking changes and enhancements.

Example URIs:
```http
http://www.api.domain.com/v1.0/customers/1234
http://www.api.domain.com/v2.2/products/1234
```

### Deprecation Policy
- Limit to 2-3 active versions at a time.
- Declare older versions deprecated and provide a migration plan for consumers.

## Request and Response Headers

### Common Headers:
1. **Accept**: Defines acceptable media types for the response (e.g., `application/json`).
2. **Content-Type**: Specifies the MIME type of the request body (e.g., `application/json`).
3. **Authorization**: Includes authentication credentials for HTTP authentication (e.g., `Basic` or `Bearer` token).
4. **ETag**: Identifies the version of a resource (used for caching and versioning).

## HTTP Response Codes

### Standard Response Codes:
- **200 OK**: Successful request.
- **201 Created**: Resource created successfully.
- **204 No Content**: Successful processing with no content in the response.
- **400 Bad Request**: The server couldn't process the request due to client error.
- **401 Unauthorized**: Authentication required.
- **404 Not Found**: The requested resource could not be found.
- **500 Internal Server Error**: The server encountered an error while processing the request.