# Marketing Cloud API Integration: Sending Messages via Message Definition

This script demonstrates how to authenticate with the **Salesforce Marketing Cloud** API using client credentials, retrieve an access token, and use that token to send personalized messages through the **`MessageDefinitionSends`** API endpoint.

## AMPscript Code

Below is the AMPscript code used for the API integration:

```html
%%[
SET @submitted = RequestParameter("submitted")

IF @submitted == "true" THEN
SET @auth_Url_Endpoint = "XXXX/v2/token"
SET @payload = '{"grant_type": "client_credentials", "client_id": "XXXX", "client_secret": "XXXX"}'

    SET @request_Response = HTTPPost(@auth_Url_Endpoint, "application/json", @payload, @response)

    IF @request_Response == 200 THEN
        SET @rows = BuildRowsetFromString(@response, '"')
        SET @rowCount = RowCount(@rows)

        IF @rowCount > 0 THEN
            SET @row = Row(@rows, 4)
            SET @access_token = Field(@row, 1)

            SET @rest_url_row = Row(@rows, 22)
            SET @rest_url = Field(@rest_url_row, 1)

            SET @rest_url_Ts_EP = CONCAT(@rest_url, "/messaging/v1/messageDefinitionSends/key:110535/send")

            SET @header_name = "Authorization"
            SET @headervalue = CONCAT("Bearer ", @access_token)

            SET @firstName = RequestParameter("first_name")
            SET @lastName = RequestParameter("last_name")
            SET @email = RequestParameter("email")

            SET @email_Payload = CONCAT('{"To": { "Address": "', @email, '", "SubscriberKey": "', @email, '", "ContactAttributes": { "SubscriberAttributes": { "First_Name": "', @firstName, '", "Last_Name": "', @lastName, '" } } }}')

            SET @send_response = HTTPPost(@rest_url_Ts_EP, "application/json", @email_Payload, @ts_api_response, @header_name, @headervalue)
        ELSE
            SET @access_token = "No data found"
            SET @rest_url = "No data found"
        ENDIF
    ELSE
        SET @access_token = CONCAT("Error: ", @response)
    ENDIF

ENDIF
]%%

<br><br>
**API Request Status** - %%=v(@request_Response)=%% <br><br>
**API Response** - %%=v(@response)=%% <br><br>
**Access Token** - %%=v(@access_token)=%% <br><br>
**REST URL** - %%=v(@rest_url)=%% <br><br>
**Row Count** - %%=v(@rowCount)=%% <br><br>
**Send Response Status** - %%=v(@send_response)=%% <br><br>

<form action="%%=RequestParameter('PAGE_URL')=%%" method="post">
  <label>First Name</label>
  <input type="text" name="first_name" required="">
  
  <label>Last Name</label>
  <input type="text" name="last_name" required="">
  
  <label>Email</label>
  <input type="text" name="email" required="">
  
  <input type="hidden" name="submitted" value="true">
  <button type="submit" class="form-button">Submit</button>  
</form>
```

## Replacing Credentials:
* Replace XXXX/v2/token with your auth endpoint in the @auth_Url_Endpoint variable.
* Replace client_id (XXX) with your Client ID.
* Replace client_secret (XXXX) with your Client Secret.
