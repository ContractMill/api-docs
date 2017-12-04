![logo](https://contractmill.com/wp-content/uploads/2017/12/cm-logo-portrait-150.png)
# ContractMill API
4.12.2017

API hosts:

 - Production environment https://app.contractmill.com
 - Staging environment access can be provided by request

All POST data and response content types are `application/json`.


## Authentication

#### POST /api/v1/auth/sign_in
Logs in a user and returns user data and authorization headers.
Requires `email` and `password`.

```json
{"email": "test2@example.com", "password": "topsecret"}
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
Returns a list of templates the current user has access to.
Response with a array of [Template](#contract-template) objects.

Query string parameters:

  - `search[query]` - filter templates by keyword
  - `search[category_id]` - filter templates by category ID

#### POST /api/v1/templates/new
Creates a new [Template](#contract-template), returns an object with the template ID.
```json
{ "id": 123 }
```

#### GET /api/v1/templates/:contract_template_id
Returns a [Template](#contract-template) by its ID.



## Contracts

#### GET /api/v1/contracts
Returns a list of contracts.
Response with a array of [Contract](#contract) objects.

Query string parameters:
  - `search[query]` - filter contracts by keyword
  - `search[category_id]` - filter contracts by category ID.


#### POST /api/v1/templates/:contract_template_id/contracts/new
Create a new document from a Template (where :contract_template_id is the ID of the template), returns an object with the contract ID.
```json
{ "id": 123 }
```

#### GET /api/v1/templates/:contract_template_id/contracts/:id
Returns a [Contract](#contract) by its template ID and contract ID.

#### POST /api/v1/contracts/:id
Update a Contract with user supplied data (metadata or answers to template questions).
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

#### GET /api/v1/templates/:contract_template_id/contracts/:id.docx
Downloads an unpublished Contract as DOCX.

#### POST /api/v1/templates/2/public_contract_links/new
Create a new public link to a Contract questionnaire.
Example payload:
```json
{ "public_question_ids": [1, 2, 3], "content_is_visible": true }
```
where:

  - `public_question_ids` - are IDs of the questions that need to be visible to an outside user
  - `content_is_visible` - indicates whether the content of the contract will be visible to an outside user, and whether to allow downloading it as DOCX.

Returns an object with the URL to this public version of a Contract questionnaire:
`{ "url": "https://contractmill.com/contract/ffaa12b2-8a6d-4ad4-bc85-7b802cfb2173" }`


#### GET /api/v1/archive
Returns a list of completed Contracts.

#### GET /api/v1/archive/drafts
Returns a list of not-completed Contracts.

#### GET /api/v1/archive/:id
Returns a Contract by ID.

#### GET /api/v1/archive/:id.docx
Downloads a published Contract as DOCX.


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
