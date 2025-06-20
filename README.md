Error handling in Mule 4 is robust and flexible, allowing you to handle errors at different levels (flow, application, or global) and for different scenarios (e.g., transformation errors, connectivity errors, custom errors). Below, I’ll describe the main components of error handling in Mule 4, followed by examples for different use cases.

1. Mule 4 Error Handling Basics
Mule 4 introduces a new error handling mechanism based on:

On Error Propagate: Handles the error and rethrows it.
On Error Continue: Handles the error and continues processing.
Global Error Handlers: Error handlers defined at the application level.
Custom Error Types: You can define your own error types.
Try Scope: Provides a way to catch and handle errors within a part of a flow.
2. Common Use Cases & Examples
A. Handling a Transformation Error
Scenario: DataWeave transformation fails due to a mapping issue.

XML
<flow name="transformFlow">
  <error-handler>
    <on-error-continue type="TRANSFORMATION">
      <logger level="ERROR" message="Transformation failed: #[error.description]" />
      <set-payload value="Transformation Error Occurred" />
    </on-error-continue>
  </error-handler>
  <set-payload value="#[payload.nonexistentField]" />
</flow>
B. Handling HTTP Connectivity Error
Scenario: HTTP request fails (e.g., service is down).

XML
<flow name="httpFlow">
  <error-handler>
    <on-error-continue type="HTTP:CONNECTIVITY">
      <logger level="ERROR" message="HTTP connectivity failed: #[error.description]" />
      <set-payload value="HTTP Service Unavailable" />
    </on-error-continue>
  </error-handler>
  <http:request method="GET" url="http://nonexistent.api/service" />
</flow>
C. Global Error Handler
Scenario: Handle all errors in the application at a global level.

XML
<error-handler>
  <on-error-propagate enableNotifications="true">
    <logger level="ERROR" message="Global Error: #[error.errorType]: #[error.description]" />
    <set-payload value="An unexpected error occurred. Please contact support." />
  </on-error-propagate>
</error-handler>
D. Handling Custom Error Types
Scenario: Throw and handle a custom error.

XML
<flow name="customErrorFlow">
  <error-handler>
    <on-error-continue type="CUSTOM:MY_ERROR">
      <logger level="ERROR" message="Custom error handled: #[error.description]" />
      <set-payload value="Custom error response" />
    </on-error-continue>
  </error-handler>
  <raise-error type="CUSTOM:MY_ERROR" description="This is a custom error" />
</flow>
E. Try Scope Error Handling
Scenario: Handle errors only in a specific part of the flow.

XML
<flow name="tryScopeFlow">
  <try>
    <set-payload value="#[1/0]" /> <!-- Will cause a division by zero error -->
    <error-handler>
      <on-error-continue type="EXPRESSION">
        <logger level="ERROR" message="Math error: #[error.description]" />
        <set-payload value="Math Error Handled" />
      </on-error-continue>
    </error-handler>
  </try>
  <logger level="INFO" message="After try scope: #[payload]" />
</flow>
3. Best Practices
Use specific error types for more granular control.
Use global error handlers for unexpected or uncaught errors.
Log all errors with sufficient context for debugging.
Use raise-error to create custom error scenarios as needed.
