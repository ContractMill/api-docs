![logo](https://contractmill.com/wp-content/uploads/2017/12/cm-logo-portrait-150.png)
# ContractMill API
8.12.2017 v1.0

API hosts:

 - Production environment [https://app.contractmill.com](https://app.contractmill.com)
 - Staging environment access can be provided by request

All POST data and response content types are `application/json`.

An example [Postman](https://www.getpostman.com/) collection [is here](contractmill-api.postman_collection.json).

## Authentication

#### POST /api/v1/auth/sign_in
Logs in a user and returns user data and authorization headers.
Requires `email` and `password`.

```json
{ "email": "test2@example.com", "password": "topsecret" }
```

Returns a [User](#user-data-response) object and the following headers:

 - `access-token`
 - `client`
 - `expiry`
 - `uid`

**These headers should be used for all API requests except authentication.**


#### DELETE /api/v1/sign_out
Logs out current user.

#### GET /api/v1/api/v1/:provider
Redirects a user to an external authentication provider.
Examples of external auth providers can be:

 - AzureAD: Authenticate to Microsoft AzureAD with OpenID Connect
 - Azure: Authenticate to Windows Azure Active Directory (WAAD) over OAuth2.
 - Microsoft Office 365: Authenticate to Office 365 using its OAuth 2.0 API.

[Complete list of auth providers](https://github.com/omniauth/omniauth/wiki/List-of-Strategies)



## Templates

#### GET /api/v1/api/v1/templates
Returns a list of templates (array of [Template](#contract-template) objects) the current user has access to.

Query string parameters:

  - `published` - return only published contracts
  - `query` - filter templates by keyword
  - `category_id` - filter templates by category ID

#### POST /api/v1/templates
Creates a new [Template](#contract-template)
Example payload:
```json
{
  "contract_template":{
    "name": "API Test",
    "description": "API test",
    "category": null,
    "role": "admin",
    "selected_tags": "hello,world",
    "header_content": "header",
    "footer_content": "footer",
    "update_tags_and_category": true
  }
}
```
Returns the new [Template](#contract-template).

#### PUT /api/v1/templates/:contract_template_id
Updates a [Template](#contract-template) by its ID. Returns template object.


#### GET /api/v1/templates/:contract_template_id
Returns a [Template](#contract-template) by its ID.



## Contracts

#### GET /api/v1/contracts
Returns a list of contracts (array of [Contract](#contract) objects)

Query string parameters:
  - `query` - filter contracts by keyword
  - `category_id` - filter contracts by category ID.


#### POST /api/v1/templates/:contract_template_id/contracts
Creates a new document from a Template (where :contract_template_id is the ID of the template), returns an object with the contract ID.
```json
{
  "contract": {
    "description": "Contract description",
    "parties": "Company A, Company B"
  }
}
```
Returns the new [Contract](#contract).

#### GET /api/v1/contracts/:id
Returns a [Contract](#contract) by contract ID.

#### PUT /api/v1/contracts/:id
Updates a Contract with user supplied data (metadata or answers to template questions).
Example payload:
```json
{
  "contract": {
    "id": 7,
    "user_answers": [
      {
        "body": "Hello world!",
        "question_id": 1
      }
    ]
  }
}
```

#### GET /api/v1/contracts/:id.docx
Downloads a contract as DOCX.

#### POST /api/v1/templates/:contract_template_id/public_contract_links
Creates a new public link to a Contract questionnaire.
Example payload:
```json
{
  "contract": {
    "public_question_ids": [2,3,4],
    "content_is_visible": true
  }
}
```
where:

  - `public_question_ids` - are IDs of the questions that need to be visible to an outside user
  - `content_is_visible` - indicates whether the content of the contract will be visible to an outside user, and whether to allow downloading it as DOCX.

Returns an object with the URL to this public version of a Contract questionnaire:
`{ "url": "https://contractmill.com/contract/ffaa12b2-8a6d-4ad4-bc85-7b802cfb2173" }`


# Example response data

#### User data response
```json
{
  "data": {
    "id": 4,
    "email": "test2@example.com",
    "name": "Test User",
    "uid": "test2@example.com",
    "title": "Lawyer",
    "phone_number": "01234567"
  }
}
```

#### Contract Template

```json
{
  "id": 2,
  "name": "Test",
  "content": "<h1>Hello world</h1><p>This is a test document: #A1</p>",
  "description": "",
  "state": "published",
  "unused_variable_codes": [],
  "used_variable_codes": [
    "#A1"
  ],
  "tag_ids": [],
  "contract_category_id": null,
  "available_tags": [],
  "available_categories": [],
  "role": "admin",
  "selected_tag_names": "",
  "category_name": null,
  "docx_template_id": 7,
  "css_styles": {},
  "questions_count": 1,
  "multiple_documents_enabled": false,
  "header_content": null,
  "footer_content": null,
  "documents": [
    {
      "id": 2,
      "name": "Test",
      "content": "<h1>Hello world</h1><p>This is a test document: #A1</p>"
    }
  ]
}
```

#### Contract
```json
{
  "id": 7,
  "questions": [
    {
      "id": 1,
      "body": "Test input",
      "question_type": "user_input",
      "variable_code": "#A1",
      "contract_template_id": 2,
      "humanised_question_type": "User input",
      "variable_codes": [
        "#A1"
      ],
      "instructions": "",
      "rule": null,
      "position": 1,
      "question_options": []
    }
  ],
  "content": null,
  "preview": false,
  "contract_template_id": 2,
  "answered": true,
  "css_styles": {},
  "contract_template_name": "Test",
  "content_is_visible": true,
  "token": null,
  "documents": [
    {
      "id": 7,
      "name": "Test",
      "content": "<h1>Hello world</h1><p>This is a test document: <span class='preview-highlight missing'>Test input</span></p>"
    }
  ],
  "answers": []
}
```
