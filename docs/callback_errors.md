When an error happens the error will be sent to the callback endpoint with the following parameters:

- `error`
- `error_description`

### Error examples

**Abort**
error=access_denied
error_description=End-User%20aborted%20interaction

**General Error**
error=general_error
error_description=General%20error

**Invalid Request**
error=invalid_request_uri
error_description=request_uri%20is%20invalid%20or%20expired