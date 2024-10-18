
## Overview

MUnit is the MuleSoft testing framework designed to help developers build and automate tests for Mule applications. This document outlines best practices for using MUnit effectively to ensure the reliability and quality of your Mule applications.

## Core Components of MUnit Tests

MUnit test cases are divided into three main sections:

1. **Execution**
   - **Set Event**: Defines the Mule event at the start of the test. Use this to define the initial message sent to the flow under test.
   - **Component Execution**: Executes the code unit being tested (e.g., Flow Reference, Transform Message).

2. **Behavior**
   - **Mock When**: Mocks external calls or internal flow references to isolate the test case from other systems.
   - **Spy**: Observes what happens before and after the execution of an event processor.

3. **Validation**
   - **Assert Equals**: Verifies simple data types or small JSON objects.
   - **Assert That**: Validates complex results, often from JSON or DataWeave scripts.
   - **Verify Call**: Ensures certain components (like mocks or flow references) were invoked.

## Execution Best Practices

### Set Event

- Use the **Set Event** processor to define the Mule event at the beginning of a test.
- Payloads should be stored in separate files (e.g., `.json` or `.dwl`) within the `src/test/resources/` directory. This ensures separation of test data from the code.

#### Loading Test Payloads
- **Option 1**: Use `MunitTools::getResourceAsString` to read a JSON payload.
   ```java
   MunitTools::getResourceAsString("organization/organization-put-request.json")
   ```
- **Option 2**: Use the `read()` function to read and parse the file.
   ```java
   read(MunitTools::getResourceAsString("users/create-user-payload.json"), "application/json")
   ```
- **Option 3**: Use `readUrl()` to reference a classpath file.
   ```java
   readUrl('classpath://updateaccount/set-event_payload.dwl')
   ```

### Set Variables and Attributes

- Use **Set Variables** to define dynamic test data.
  ```xml
  <munit:variable key="errorCode" value="BAD REQUEST" />
  ```

- Similarly, use **Set Attributes** to define attributes in your test event.
  ```java
  readUrl('classpath://commonServices/set-logging_attributes.dwl')
  ```

## Behavior Best Practices

### Mock When

- **Mock When** is essential for isolating external calls, such as HTTP requests or connector references. Focus on mocking:
  - HTTP endpoints
  - Database connectors
  - Other flow references

- Use attributes like `doc::Id` or `doc::name` to ensure you mock the correct event processor. Ensure these values are updated if the test flow is renamed or moved.

### Spy Processor

- The **Spy** processor allows you to monitor what happens before and after an event processor is called. Use this for transformations or validations.
- Similar to **Mock When**, rely on `doc::Id` or `doc::name` to define the spy processor.

## Validation Best Practices

### Assert Equals
- **Assert Equals** should be used to verify simple data types like strings or small JSON objects with 1 to 3 fields.
  ```xml
  <munit:assert-equals expectedValue="expectedValue" actualValue="#[actualValue]" />
  ```

### Assert That
- Use **Assert That** for complex validations, especially when comparing JSON or DataWeave output.
- For example, you can validate the entire JSON response:
  ```java
  MunitTools::equalTo(read(MunitTools::getResourceAsString("users/user-response.json"), "application/json"))
  ```

- **Excluding Fields**: If your test includes fields that change dynamically (e.g., `dateTime`), exclude those fields from validation to avoid unnecessary test failures.

## Testing Scenarios

### Common Scenarios

1. **Request Only**:
   - Use the **Spy** component to validate the transformation of the request before it is sent.

2. **Request & Response**:
   - Use the **Spy** component to validate both the request and response transformations.
   - Use the **Assert** components in the validation section to ensure the correct output is returned.

### Testing Choice Flows

- Provide at least one test for each branch of a **Choice** flow to ensure all conditional logic is covered.

### Testing DataWeave Scripts

- For externalized DataWeave scripts, create tests for each function within the script to validate their behavior in isolation.

## General Best Practices

1. **Use Separate Test Files**: Always externalize payloads and other large data sets into separate files (e.g., `.json`, `.dwl`).
2. **Mock External Calls**: Mock all external HTTP calls, connectors, or flow references to isolate the unit test.
3. **Spy on Critical Components**: Use the **Spy** component to inspect the results of important transformations or processor calls.
4. **Verify Multiple Branches**: Ensure tests cover all conditional logic, including branches in **Choice** processors.
5. **Store Test Data Properly**: Store test data under the `src/test/resources/` folder to maintain a clean project structure.
