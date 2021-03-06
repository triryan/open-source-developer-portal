---
layout: twoColumn
section: Business Verified Customer
type: article
title:  "Handling business verified Customer Statuses"
weight: 2
description: "How to handle the Controller and Business identity verification"
---
# Handling business verified Customer statuses

You have successfully created a business verified Customer; however, there are cases where Dwolla will need more information to fully verify the identity of the Controller and/or Business. Read on to learn more.

**If your Controller and Business are identity verified,** you can skip to the [retrieve beneficial owner status](/resources/business-verified-customer/handling-controller-and-customer-statuses.html#retrieve-beneficial-owner-s-status)  at the bottom of the page to continue with your business verified Customer creation.

## Verification statuses

| Customer status | Event | Description |
|-----------------|-------|-------------|
| verified | customer_verified | The identifying information submitted was sufficient in verifying the Customer account. |
| retry | customer\_reverification\_needed | The initial identity verification attempt failed because the information provided did not satisfy Dwolla’s verification check. You can make one additional attempt by changing some or all the attributes of the existing Customer with a POST request. All fields are required on the retry attempt. If the additional attempt fails, the resulting status will be either `document` or `suspended`. |
| document | customer\_verification\_document\_needed | Dwolla requires additional documentation to identify the Customer in the document status. Once a document is uploaded it will be reviewed for verification. |
| suspended | customer_suspended | The Customer is suspended and may neither send nor receive funds. Contact Account Management for more information. |

## Handling `retry` status

A `retry` status occurs when a Customer’s identity scores are too low during the initial verification attempt. Dwolla will require the **full 9-digits** of the Controller’s SSN on the retry attempt in order to give our identity vendor more information in an attempt to receive a sufficient score to approve the Customer account. The Customer will have one more opportunity to correct any mistakes.

<ol class = "alerts">
    <li class="alert icon-alert-info">
      You need to gather new information if the Customer is placed into the retry status; simply passing the same information will result in the same insufficient scores.
    </li>
</ol>

All fields that were required in the initial Customer creation attempt will be required in the retry attempt, along with the full 9-digit SSN.

#### Request and response

```raw
POST https://api-sandbox.dwolla.com/customers/62c3aa1b-3a1b-46d0-ae90-17304d60c3d5
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer 0Sn0W6kzNic+oWhDbQcVSKLRUpGjIdl/YyrHqrDDoRnQwE7Q

{
    "firstName": "Jane",
    "lastName": "Merchant",
    "email": "accountAdmin@email.com",
    "ipAddress": "143.156.7.8",
    "type": "business",
    "address1": "123 Corrected Address St",
    "city": "Some City",
    "state": "NY",
    "postalCode": "11101",
    "controller": {
        "firstName": "John",
        "lastName": "Controller",
        "title": "CEO",
        "dateOfBirth": "1980-01-01",
        "ssn": "123-45-6789",
        "address": {
            "address1": "1749 18th st",
            "address2": "apt 12",
            "city": "Des Moines",
            "stateProvinceRegion": "IA",
            "postalCode": "50266",
            "country": "US"
        }
    },
    "businessClassification": "9ed3f670-7d6f-11e3-b1ce-5404a6144203",
    "businessType": "llc",
    "businessName":"Jane Corp",
    "ein":"00-0000000"
}
```

```php
<?php
$customersApi = new DwollaSwagger\CustomersApi($apiClient);
$customerUrl = 'https://api-sandbox.dwolla.com/customers/b70c3194-35fa-49e8-9243-d55a30e06d1e';
$retryCustomer = $customersApi->updateCustomer([
  'firstName' => 'Jane',
  'lastName' => 'Merchant',
  'email' => 'accountAdmin@email.com',
  'type' => 'business',
  'address1' => '123 Corrected Address St',
  'city' => 'Some City',
  'state' => 'NY',
  'postalCode' => '11101',
  'controller' =>
  [
      'firstName' => 'John',
      'lastName'=> 'Controller',
      'title' => 'CEO',
      'dateOfBirth' => '1990-10-10',
      'ssn' => '123-456-7890',
      'address' =>
      [
          'address1' => '18749 18th st',
          'address2' => 'apt 12',
          'city' => 'Des Moines',
          'stateProvinceRegion' => 'IA',
          'postalCode' => '50265',
          'country' => 'US'
      ],
  ],
  'phone' => '5554321234',
  'businessClassification' => '9ed3f670-7d6f-11e3-b1ce-5404a6144203',
  'businessType' => 'llc',
  'businessName' => 'Jane Corp',
  'ein' => '00-0000000'], $customerUrl);

?>
```

```ruby
# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
customer_url = 'https://api.dwolla.com/customers/62c3aa1b-3a1b-46d0-ae90-17304d60c3d5'
request_body = {
  :firstName => 'Jane',
  :lastName => 'Merchant',
  :email => 'accountAdmin@email.com',
  :type => 'business',
  :address1 => '123 Corrected Address St',
  :city => 'Some City',
  :state => 'NY',
  :postalCode => '11101',
  :controller => {
      :firstName => 'John',
      :lastName => 'Controller',
      :title => 'CEO',
      :dateOfBirth => '1980-01-01',
      :ssn => '1234'
      :address => {
        :address1 => '1749 18th st',
        :address2 => 'apt 12',
        :city => 'Des Moines',
        :stateProvinceRegion => 'IA',
        :postalCode => '50266',
        :country => 'US'
      }
  },
  :businessClassification => '9ed38155-7d6f-11e3-83c3-5404a6144203',
  :businessType => 'llc',
  :businessName => 'Jane Corp',
  :ein => '12-3456789'
}

customer = app_token.post customer_url, request_body
customer.id # => "62c3aa1b-3a1b-46d0-ae90-17304d60c3d5"
```

```python
# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
customer_url = 'https://api.dwolla.com/customers/62c3aa1b-3a1b-46d0-ae90-17304d60c3d5'
request_body = {
  'firstName': 'Jane',
  'lastName': 'Merchant',
  'email': 'accountAdmin@email.com',
  'type': 'business',
  'address1': '123 Corrected Address St',
  'city': 'Some City',
  'state': 'NY',
  'postalCode': '11101',
  'controller': {
      'firstName': 'John',
      'lastName': 'Controller',
      'title': 'CEO',
      'dateOfBirth': '1980-01-01',
      'ssn': '1234'
      'address': {
        'address1': '1749 18th st',
        'address2': 'apt12',
        'city': 'Des Moines',
        'stateProvinceRegion': 'IA',
        'postalCode': '50266',
        'country': 'US'
      }
  },
  'businessClassification': '9ed38155-7d6f-11e3-83c3-5404a6144203',
  'businessType': 'llc',
  'businessName': 'Jane Corp',
  'ein': '12-3456789'
}

customer = app_token.post('customers', request_body)
customer.body.id # => '62c3aa1b-3a1b-46d0-ae90-17304d60c3d5'
```

```javascript
var customerUrl = 'https://api.dwolla.com/customers/62c3aa1b-3a1b-46d0-ae90-17304d60c3d5';
var requestBody = {
  firstName: 'Jane',
  lastName: 'Merchant',
  email: 'accountAdmin@email.com',
  type: 'business',
  address1: '123 Corrected Address St',
  city: 'Some City',
  state: 'NY',
  postalCode: '11101',
  controller: {
      firstName: 'John',
      lastName: 'Controller',
      title: 'CEO',
      dateOfBirth: '1980-01-01',
      ssn: '1234'
      address: {
        address1: '1749 18th st',
        address2: 'apt 12',
        city: 'Des Moines',
        stateProvinceRegion: 'IA',
        postalCode: '50266',
        country: 'US'
      }
  },
  businessClassification: '9ed38155-7d6f-11e3-83c3-5404a6144203',
  businessType: 'llc',
  businessName: 'Jane Corp',
  ein: '12-3456789'
};

appToken
  .post(customerUrl, requestBody)
  .then(function(res) {
    res.body.id; // => '62c3aa1b-3a1b-46d0-ae90-17304d60c3d5'
  });
```

## Handling `document` status

If the Customer has a status of `document`, the Customer will need to upload additional pieces of information in order to verify the account. Use the [create a document](https://docsv2.dwolla.com/#create-a-document) endpoint when uploading a colored scan of the identifying document. The document(s) will then be reviewed by Dwolla; this review may take up to 1-2 business days to approve or reject.

### Determining verification documents needed

When a business verified Customer is placed in the `document` verification status, Dwolla will return a link in the API response after retrieving a Customer, which will be used by an application to determine if documentation is needed. For business verified Customers, different links can be returned depending on whether or not documents are needed for a Controller, the business, or both the controller and business. Refer to the table below for the list of possible links and their description.

| Link name | Description |
|---------------|----------|
| verify-with-document | Identifies if documents are needed only for a Controller. |
| verify-business-with-document | Identifies if documents are needed only for a business. |
| verify-controller-and-business-with-document | Identifies if documents are needed for both the Controller and business. |

#### Example response

```noselect
{
    "_links": {
        "verify-beneficial-owners": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/beneficial-owners",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "beneficial-owner"
        },
        "beneficial-owners": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/beneficial-owners",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "beneficial-owner"
        },
        "deactivate": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "customer"
        },
        "document-form": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/documents",
            "type": "application/vnd.dwolla.v1.hal+json; profile=\"https://github.com/dwolla/hal-forms\"",
            "resource-type": "document"
        },
        "self": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "customer"
        },
        "verify-business-with-document": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/documents",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "document"
        },
        "certify-beneficial-ownership": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/beneficial-ownership",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "beneficial-ownership"
        },
        "funding-sources": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/funding-sources",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "funding-source"
        },
        "transfers": {
            "href": "https://api-sandbox.dwolla.com/customers/8698bbc8-2d9e-4140-ab1d-1af0487c7660/transfers",
            "type": "application/vnd.dwolla.v1.hal+json",
            "resource-type": "transfer"
        }
    },
    "id": "8698bbc8-2d9e-4140-ab1d-1af0487c7660",
    "firstName": "document",
    "lastName": "AccountAdmin",
    "email": "accountAdmin@email.com",
    "type": "business",
    "status": "document",
    "created": "2018-05-10T19:59:22.643Z",
    "address1": "99-99 33rd St",
    "city": "Some City",
    "state": "NY",
    "postalCode": "11101",
    "businessName": "Jane Corp",
    "controller": {
        "firstName": "John",
        "lastName": "Controller",
        "title": "CEO",
        "address": {
            "address1": "1749 18th st",
            "address2": "apt 12",
            "city": "Des Moines",
            "stateProvinceRegion": "IA",
            "country": "US",
            "postalCode": "50266"
        }
    }
}
```

#### Document Types

##### Controllers

A scanned photo of the Controller’s identifying document can be specified as documentType: `passport`, `license` (state issued driver's license), or `idCard` (other U.S. government-issued photo id card).

##### Businesses

Documents that are used to help identify a business are specified as documentType `other`. Business Identifying documents we recommend uploading can include the following:

* **Partnership, General Partnership**: EIN Letter (IRS-issued SS4 confirmation letter).
* **Limited Liability Corporation (LLC), Corporation**: EIN Letter (IRS-issued SS4 confirmation letter).
* **Sole Proprietorship**: One or more of the following, as applicable to your sole proprietorship: Fictitious Business Name Statement; Certificate of Assumed Name; Business License; Sales/Use Tax License; Registration of Trade Name; EIN documentation (IRS-issued SS4 confirmation letter); Color copy of a valid government-issued photo ID (e.g., a driver’s license, passport, or state ID card).

Other business documents that are applicable includes any US government entity (federal, state, local) issued business formation or licensing exhibiting the name of the business enrolling with Dwolla, or; Any business formation documents exhibiting the name of the business entity in addition to being filed and stamped by a US government entity. Examples include:

* Filed and stamped Articles of Organization or Incorporation
* Sales/Use Tax License
* Business License
* Certificate of Good Standing

### Uploading a document

To upload a photo of the document, you’ll initiate a multipart form-data POST request from your backend server to `https://api.dwolla.com/customers/{id}/documents`. The file must be either a .jpg, .jpeg, .png, .tif, or .pdf. Files must be no larger than 10MB in size.

You’ll also get a webhook with a `customer_verification_document_uploaded` event to let you know the document was successfully uploaded.

#### Request and response

```raw
curl -X POST
\ -H "Authorization: Bearer tJlyMNW6e3QVbzHjeJ9JvAPsRglFjwnba4NdfCzsYJm7XbckcR"
\ -H "Accept: application/vnd.dwolla.v1.hal+json"
\ -H "Cache-Control: no-cache"
\ -H "Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW"
\ -F "documentType=passport"
\ -F "file=@foo.png"
\ 'https://api-sandbox.dwolla.com/customers/132681fa-1b4d-4181-8ff2-619ca46235b1/documents
```

```php
No example for this language yet.
```

```ruby
# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby
customer_url = 'https://api.dwolla.com/customers/132681fa-1b4d-4181-8ff2-619ca46235b1'

file = Faraday::UploadIO.new('mclovin.jpg', 'image/jpeg')
document = app_token.post "#{customer_url}/documents", file: file, documentType: 'license'
document.response_headers[:location] # => "https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0"
```

```python
# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
customer_url = 'https://api.dwolla.com/customers/132681fa-1b4d-4181-8ff2-619ca46235b1'

document = app_token.post('%s/documents' % customer_url, file = open('mclovin.jpg', 'rb'), documentType = 'license')
document.headers['location'] # => 'https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0'
```

```javascript
// Using dwolla-v2 - https://github.com/Dwolla/dwolla-v2-node
var customerUrl = 'https://api.dwolla.com/customers/132681fa-1b4d-4181-8ff2-619ca46235b1';

var requestBody = new FormData();
body.append('file', fs.createReadStream('mclovin.jpg'), {
  filename: 'mclovin.jpg',
  contentType: 'image/jpeg',
  knownLength: fs.statSync('mclovin.jpg').size
});
body.append('documentType', 'license');

appToken
  .post(`${customerUrl}/documents`, requestBody)
  .then(function(res) {
    res.headers.get('location'); // => "https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0"
  });

```

If the document was successfully uploaded, the response will be a HTTP 201 Created with the URL of the new document resource contained in the Location header.

```raw
HTTP/1.1 201 Created
Location: https://api-sandbox.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0
```

### Document review process

Once created, the document will be reviewed by Dwolla. When our team has made a decision to approve or reject, which may take up to 1-2 business days, we’ll create either a `customer_verification_document_approved` or `customer_verification_document_failed` event.

If the document was sufficient, the Customer may be verified in this process. If not, we may need additional documentation. Note: Reference the [determining verification documents needed](/resources/business-verified-customer/handling-controller-and-customer-statuses.html#document-types) section for more information on determining if additional documents are needed after an approved or failed event is triggered.

If the document was found to be fraudulent or doesn’t match the identity of the Customer, the Customer will be suspended.

### Document failure

A document can fail if, for example, the Customer uploaded the wrong type of document or the `.jpg` or `.png` file supplied was not readable (i.e. blurry, not well lit, or cuts off a portion of the identifying image). If you receive a `customer_verification_document_failed` webhook, you’ll need to upload another document. To retrieve the failure reason for the document upload, you’ll retrieve the document by its ID. Contained in the response will be a `failureReason` which corresponds to one of the following values:

* `ScanNotReadable` - The photo was blurry, parts of the image were cut off, or the photo had glares on it preventing information from being read
* `ScanNotUploaded` - A photo was uploaded, but it was not an ID
* `ScanIdTypeNotSupported` - An ID was uploaded, but it is not a form of ID we accept
* `ScanNameMismatch` - The name on the ID does not match the name on the account

#### Request and response

```raw
GET https://api-sandbox.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer tJlyMNW6e3QVbzHjeJ9JvAPsRglFjwnba4NdfCzsYJm7XbckcR

...

{
  "_links": {
    "self": {
      "href": "https://api-sandbox.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0"
    }
  },
  "id": "11fe0bab-39bd-42ee-bb39-275afcc050d0",
  "status": "reviewed",
  "type": "license",
  "created": "2016-01-29T21:22:22.000Z",
  "failureReason": "ScanNotReadable"
}
```

```php
<?php
$aDocument = 'https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0';

$documentsApi = DwollaSwagger\DocumentsApi($apiClient);

$retrieved = $documentsApi->getCustomer($aDocument);
print($retrieved->failureReason); # => "ScanNotReadable"
?>
```

```ruby
# Using DwollaV2 - https://github.com/Dwolla/dwolla-v2-ruby (Recommended)
document_url = 'https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0'

document = app_token.get document_url
document.failureReason # => "ScanNotReadable"
```

```python
# Using dwollav2 - https://github.com/Dwolla/dwolla-v2-python (Recommended)
document_url = 'https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0'

documents = app_token.get(document_url)
documents.body['failureReason'] # => 'ScanNotReadable'
```

```javascript
var documentUrl = 'https://api.dwolla.com/documents/11fe0bab-39bd-42ee-bb39-275afcc050d0';

appToken
  .get(document_url)
  .then(function(res) {
    res.body.failureReason; // => "ScanNotReadable"
  });
```

## Handling status: suspended

If the Customer is `suspended`, there’s no further action you can take to correct this using the API. You’ll need to contact support@dwolla.com or your account manager for assistance.

## Next steps: Beneficial Owners

The successful creation of a business verified Customer and Controller doesn’t necessarily mean the Customer is fully verified and eligible to transfer. After successfully creating your business verified Customer, you will need to check to see if the beneficial ownership requirements apply to you.

To learn how to add beneficial owner(s) to your Customer, read on to our [next article](/resources/business-verified-customer/adding-beneficial-owners.html).

* * *

#### Next steps

* [Adding beneficial owner(s)](/resources/business-verified-customer/adding-beneficial-owners.html)
* [Certifying beneficial owner(s)](/resources/business-verified-customer/handling-beneficial-owner-certification.html)

#### Review previous steps

* [Creating business verified Customer](/resources/business-verified-customer/create-business-verified-customers.html)
