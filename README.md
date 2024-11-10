# Postman-collection-PET-STORE


Test-cases for CREATE endpoint: https://petstore.swagger.io/#/pet/addPet


1) Validate authorization for CREATE request with API key

Preconditions:
Endpoint URL for CREATE request is available and functioning:
	POST https://petstore.swagger.io/v2/pet
API key details provided: 
	valid api key - api_key:special-key, 
	invalid api key - api_key:invalid-key
Body for CREATE request is prepared with valid data according to the model in swagger.

Scenario 1: CREATE without Authorization
1. Set up a CREATE request without any authorization headers.
2. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: The response status code should be 401 Unauthorized or another failure code, the response body should indicate that authorization is required.

Status: Fail - now I can create new pets in the store without auth.

Scenario 2: CREATE with Invalid API Key
1. Set up a CREATE request with an invalid API key in the authorization header: api_key:invalid-key
2. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: The response status code should be 401 Unauthorized or 403 Forbidden or another failure code, the response body should indicate that authorization is required.

Status: Fail -  now I can create new pets in the store without invalid api key.

Scenario 3: CREATE with Valid API Key
1. Set up a CREATE request with a valid API key in the authorization header: api_key:special-key
2. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: The response status code should be 200 OK Created and the response body should match the structure and data sent in the request, including correctly generated IDs.

Status: Pass





2) Validate authorization for CREATE request with OAuth2: 

Preconditions:
Endpoint URL for CREATE request is available and functioning:
	POST https://petstore.swagger.io/v2/pet
OAuth2 details provided: 
login: xxxxxx, password: xxxxxx
Body for CREATE request is prepared with valid data according to the model in swagger.

Scenario 1: user logged in without read-write permission
1. Authorize user with no selected permission (read: unchecked, write: unchecked)
2. Get a token and set it up in the header of CREATE request. 
3. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: The response status code should be 403 Forbidden or another failure code, the response body should indicate that authorization is required.

Status: Blocked - can not authorize with OAuth2.

Scenario 2: user logged in with read-only permission
1. Authorize user with read-only selected permission (read: checked, write: unchecked)
2. Get a token and set it up in the header of CREATE request. 
3. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: The response status code should be 403 Forbidden or another failure code, the response body should indicate that authorization is required.
Status: Blocked - can not authorize with OAuth2.

Scenario 3: user logged in with write-only permission
1. Authorize user with read-only selected permission (read: unchecked, write:checked)
2. Get a token and set it up in the header of CREATE request. 
3. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: The response status code should be 200 OK Created and the response body should match the structure and data sent in the request, including correctly generated IDs.

Status: Blocked - can not authorize with OAuth2.

Scenario 4: user logged in with read-write permission
1. Authorize user with read-write selected permission (read: checked, write:checked)
2. Get a token and set it up in the header of CREATE request. 
3. Send the request to the CREATE endpoint with correct data in the body.

Expected Result: 
The response status code should be 200 OK Created and the response body should match the structure and data sent in the request, including correctly generated IDs.

Status: Blocked - can not authorize with OAuth2.




3) Validate creation of an item with valid data (all values in the allowed range)

Preconditions:
Endpoint URL for CREATE request is available and functioning
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body for CREATE request is prepared with valid data according to the model in swagger

Steps:
1. Prepare the JSON body for the CREATE request with valid values for all fields,     ensuring they are within the allowed range
Example: 
{
  "id": 12345,
  "category": {
    "id": 1,
    "name": "Cat"
  },
  "name": "Alice",
  "photoUrls": [
    "http://example.com/photo1.jpg"
  ],
  "tags": [
    {
      "id": 101,
      "name": "Tag1"
    }
  ],
  "status": "available"
}


2. Send a POST request to the CREATE endpoint with the above JSON body.

Expected result: 
The response status code should be 200 OK.
The response body should contain the data of the newly created resource, and it should match the sent request.
No errors should occur during the process.
A new item should be created in the system with the values provided in the CREATE request.

Status: Passed




4) Validate the behavior when required fields (name and photoUrls) are missing in the CREATE request

Preconditions:
The CREATE API endpoint is available and functional.
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body without required fields prepared (any other required fields can be left out for this test case)

Steps:
1. Prepare the JSON body for the CREATE request without the required fields name and photoUrls
2. Send a POST request to the CREATE endpoint with the above JSON body.

Expected result: 
The response status code should be 400 Bad Request or a similar client-side error code. 
The response body should contain an error message indicating the missing required fields

Status: Failed - item was created




5) Validate the behavior when required fields (name and photoUrls) are only added fields in the CREATE request

Preconditions:
The CREATE API endpoint is available and functional.
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body with only required fields prepared

Steps:
1. Prepare the JSON body for the CREATE request without the required fields name and photoUrls
2. Send a POST request to the CREATE endpoint with the above JSON body.

Expected result: 
The response status code should be 400 Bad Request or a similar client-side error code. 
The response body should contain an error message indicating the missing required fields

Status: Failed - item was created but we can not get it by id from response




6)  Validate the behavior with different Id field value in the CREATE request

Note: 
The same test-cases must be written for categoryId and tagId 

Preconditions:
The CREATE API endpoint is available and functional.
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body prepared

Steps:
1. Check min boundary of Id field (value: 1)
Expected Result: created successfully 
Status: passes
2. Check max boundary of Id field (value: 9223372036854775807 - the biggest value for int64 according to model in swagger - hexadecimal 0x7FFFFFFFFFFFFFFF)
Expected Result: created successfully 
Status: failed - entity was created but with wrong Id (9223372036854776000)
3. Check Id value out of range (for example: -1 or 9999999999999999999)
Expected Result: client-side error 
Status: failed - entity created with server-generated id
4. Check duplicate Id value (find Id for test in DB or create it before)
Expected Result: 409 or another client-side error 
Status: failed - record was recreated
5. Check incorrect Id value (contain unsupported symbols)
Expected Result: client-side error 
Status: failed - 500 server error


Status: failed, validation absent




7) Validate the behavior with different Name field value in the CREATE request

Note: 
The same test-cases must be written for categoryName and tagName

Preconditions:
The CREATE API endpoint is available and functional.
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body prepared

Steps:
1. Check min boundary of Name field (value: contain 1 symbol)
Expected Result: created successfully 
2. Check max boundary of Name field (need to be clarify the max size of Name field)
Expected Result: created successfully 
3. Check Name field out of range (value contain more the high-boundary value symbols)
Expected Result: client-side error 
4. Check incorrect Name field (value contain unsupported symbols)
Expected Result: client-side error 
5. Check Name field if it is empty
Expected Result: client-side error OR pet created successfully without name





8) Validate the behavior with different Status field value in the CREATE request

Note: 
Before writing this test-case we need to clarify pet with which status we can create (maybe we can create it only with Available status and then modify status to Pending/Sold)

Preconditions:
The CREATE API endpoint is available and functional.
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body prepared

Steps:
1. Check value: ‘available’ for Status field
Expected Result: created successfully 
2. Check value: ‘pending’ for Status field
Expected Result: need to clarify if we can create pet with this status or only modify available -> pending 
3. Check value: ‘sold’ for Status field
Expected Result: need to clarify if we can create pet with this status or only modify pending -> sold
4. Check unsupported value (for example ‘test’) for Status field
Expected Result: client-side error




9) Validate the behavior with different Tags field value in the CREATE request

Note: 
Before writing this test-case we need to clarify what is the biggest number of tags can be added and also clarify which behavior is correct for duplicated tags)

Preconditions:
The CREATE API endpoint is available and functional.
The necessary authentication (e.g., API key, OAuth2) is successfully set up.
Body prepared

Steps:
1. Check max number of tags in Tags array
Expected Result: created successfully 
2. Check empty Tags array
Expected Result: created successfully 
3. Check out of range number of tags in Tags array
Expected Result: client-side error  - too many tags
4. Check adding duplicate tags in Tags array
Expected Result: need to be clarified







ADDITIONAL NOTES:
- It was better to write some test cases separately (e. g. auth cases), but because the requirement was to write to 10 cases I did it in one test-case(but different scenario).


Some cases that are very important and needed to be perform:
- Check photoUrl validation (array and string format)
- Check order and format of tags and photoUrls in DB and responses
- Performance testing: find out the limit of CREATE pet request and verify it
- Check Swagger (some responses codes are absent etc.)