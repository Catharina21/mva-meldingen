---
icon: "cloud"
title: "Test"
description: ""
---

We are now on 'Test 2'. See further down the page for a complete test plan. We have the validation service ready with some of the validations this service will contain.
The test documentation consists of

- Description of api - https://skatteetaten.github.io/mva-meldingen/documentation/api/
- XSD - https://skatteetaten.github.io/mva-meldingen/documentation/informasjonsmodell/
- Validation rules - https://skatteetaten.github.io/mva-meldingen/documentation/forretningsregler/
- Validation rules that are part of the test - see further down on this page
- Examples of test cases - see further down this page
  In addition, there is a need for a test user. This is distributed directly to those who will be testing.

For early testing with a few solution providers, information will be shared diretly with the involved parties.

The result of the early testing will be documented and information will be made available to all solution providers.

# Valideringsregler

Validation rules ready for test:

- The sum of the calculated VAT from each VAT line shall be equal to the total VAT in the VAT return
- The calculated VAT must be in accordance with the stated grunnlag times the current sats

# Test cases the VAT tax return

[Test cases for the VAT tax retun](Testtilfeller mva-melding.xlsx)

XML examples: https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/eksempler/
Untill further information is given there should not be % sign in the file to be sent in

# Test plan

## Content of the test

The ERP-system must do the following:

1. Establish a customer relation to Digitaliseringsdirektoratet (DigDir) and adopt ID-porten.
2. Log into ID-porten to obtain a session (and token).
3. Develop an application/ client that should send a request to Skatteetaten validation service in order to validate a vat tax return.

Skatteetaten have made available a test application that shows how the steps described above can be done. This is written in [jupyter notbook format](https://jupyter.org/):

1. [Jupyter notebook demo for retrieving, validation and submitting](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/demo.ipynb). Download the folder testinnsendingand run the script demo.ipynb (the script will execute all steps in the process: call ping service to chech the connection and validate the mva tax return.
2. [Pyton script to fetch token](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/hent.py) and [postman script to validate mva tax return](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/MeldingValidering.postman_collection.json). First step is to log in to ID-porten (described later), and store the token in the format "Bearer <em>hentet-token</em>" as an environment variable by the name "test-bearer" in postman, then use the postman script in order to validate the mva vat return.
3. [Example XML files](https://github.com/Skatteetaten/mva-meldingen/tree/master/docs/documentation/test/eksempler)

## Start using ID-porten

The first step to be done by the consumer of the validation API is to establish a customer relationship wiht Digitaliseringsdirektoratet (DigDir). Details on how to start using ID-porten is provided here: https://samarbeid.digdir.no/id-porten/ta-i-bruk-id-porten/94. This process is partially manual so it is advised to start early. When ordering access provide information that you would like to access the skattemeldings-API from Skatteetaten.

A customer relationship with DigDir provides access to their self-service solution, which in turn provides access to the administration of the Customer's use of the ID port. In the self-service solution, the customer can generate a so-called client_id and define a callback url:

- client_id: is unique unique generated identifier for the service.
- callback url: The Uri that the client is allowed to go to after logging in. After a successful login in the ID port, this url will be called.
  If it takes too long to establish a customer relationship with DigDir, the end-user systems can in the meantime use Skatteetaten's client_id. For this test, Skatteetaten has created the following client_id that can be used by the end-user systems:

      - `client_id: 38e634d9-5682-44ae-9b60-db636efe3156`
      	  - Callback URL for this client_id is set to  http://localhost:12345/token  (If there are consumers who want other callback URLs it cna be provided)

**Useful links:**

- The client is using the test environment in DigDir called "verifikasjon 2": https://samarbeid.difi.no/node/232
- OICD integration is described here: https://difi.github.io/felleslosninger/oidc_index.html
- How to create a client in the self service solution: https://minside-samarbeid.difi.no/organization-home/services/service-admin#/

## Login at ID-porten

The client must do the following REST call towards ID-porten:

- Launch the system browser and make authorization calls against ID-porten. Read more about it here: https://difi.github.io/felleslosninger/oidc_protocol_authorize.html
- The user is then sent to ID-porten for login. Existing test users for testing towards Skatteetaten's services today can be used.
- Set up a web server waiting for callback from the browser. After successful login the ID port is sent used to this web server. This web server must be set to listen to callback URLs http://localhost:12345/token (as according to previous section).
- Make a token request. Read more about it here: https://difi.github.io/felleslosninger/oidc_protocol_token.html

The following test environment at ID-porten is used:

- /authorize endpoint: https://oidc-ver2.difi.no/idporten-oidc-provider/authorize
- /token endpoint: https://oidc-ver2.difi.no/idporten-oidc-provider/token

For details on which HTTP parameters must be sent in the call, see the file [hent.py](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/hent.py)

## Call the vat tax return API

When the callback URL is called, the client must extract the JWT token from the response and place it in the Authorization header field and call the VAT Notification API. For details, see [demo klient](https://github.com/Skatteetaten/mva-meldingen/blob/master/docs/documentation/test/demo.ipynb)

URL to the vat tax return API in test is: https://mp-test.sits.no/

[comment]: <> (The first test should be to test that the client arrives, this can be done by calling the ping service:)

[comment]: <> (- `GET https://<env>/api/mva/mva-melding/ping`)

[comment]: <> (- `Example: GET https://mp-test.sits.no/api/mva/mva-melding/ping`)

# Test time line

The time line below outlines when it will be possible to test integrations towards the new solutions from Skatteetaten.

<table align=center>
  <tr><th align=center>What</th><th align=center>When</th><th align=center>Content</th></tr>
  <tr><td>Documentation XSD</td><td>28.09.2020</td><td><ul><li> Updated xsd after feedback from meeting 14.9</li></ul></td></tr>
  <tr><td>Test 1 – techincal verification</td><td>nov. 2020</td><td><ul><li>Validation service -"Dummy"  (Ping from SBS)</li></ul></td></tr> 
  <tr><td>Test 2 – thin validation service and technical verification for VAT return service</td><td>jan. 2021</td>
  <td> 
  <ul>
	<li>Validation service </li>
	<li>Simple validation service with one or a few validations </li>
    <li>VAT return service  - "Dummy"  (Ping from SBS)</li>
  </ul>
  </td></tr>
  <tr><td>Test 3 - more validations from the validation service, simple VAT return service</td><td>mar. 2021</td>
  <td>  
  <ul>
	<li>Validation service - Validation servcie containing a larger number of the validations to be provided. </li>
	<li>VAT return service  - Simple VAT return service that will verify the VAT return and provide back a confirmation message. </li>
  </ul>
  </td></tr>
  <tr><td>Test 4 – complete service</td><td>mai. 2021</td><td>
  <ul>
	<li>Validation service - Complete service, as it will be in the pilot , is available</li>
	<li>VAT return service  - Complete service, as it will be in the pilot , is available</li>
  </ul>
  </td></tr>
  <tr><td>Test 5 - main test before pilot</td><td>aug. 2021</td><td></td></tr>
  <tr><td>Pilot</td><td>aug. 2021</td><td></td></tr>
  <tr><td>Production </td><td>01.01.2022</td><td></td></tr>  
</table>

![Testplan](Testplan.png)
