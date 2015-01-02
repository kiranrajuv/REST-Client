User Guide for REST Client v1.0
===============================

Introduction:
-------------
- This tool is to test the REST Web Service APIs

Features:
---------
- Supports all HTTP requests (Currently developed for GET, POST, PUT)
- Supports XML (application/xml) & JSON (application/json) content types
- Testing the API with parameterization from MS-Excel workbook
- Customized HTML report with Request, Response, Status and Execution Time
- Logs for debugging
- Zip and mail the report

Prerequisites:
--------------
- Java 1.6 or higher
- Eclipse
- TestNG plug-in for eclipse
- Maven plug-in for eclipse

Libraries Used:
---------------
- Apache HTTP Client
- Apache Commons IO
- Apache POI
- TestNG
- Log4J
- Maven
- Maven Surefire Plug-In
- Maven Postman Plug-In (Optional)
- Maven Assembly Plug-In (Optional)

Setup:
------
1. Import the project into eclipse

2. If you get the error, change the JRE Build Path as per your JRE version

Test Data Preparation:
----------------------

- Prepare <Product_Name>.PROPERTIES file, which is unique for a product. File Name should be as your Product Name. This file should be under src/test/resources/<Product_Name> folder
  - Specify common attributes for all APIs:
    - contentType – Used as the content type of the request body
 - Specify API specific attributes:
    - <APIName> should be unique
    - <APIName>.URL – URL for the API
    - <APIName>.methodType – Method Type for the API. E.g., GET, POST, PUT etc.
    - <APIName>.testScope – Testing Scope. E.g., Scrum, E2E, Regression, Sanity etc.
    - <APIName>.reportTitle – Title for the HTML Report
  - You can refer the sample at “src/test/resources/CLMOPP/CLMOPP.PROPERTIES”

- Create Template File with file name as <APIName>_template.txt, which is unique for an API.

- For the Input XML (Request Body): for each dynamic tag (for which parameterization is required):
  - <name>${name}</name>
  - ${name} should be unique in the XML. i.e., If you have same tag multiple times, use as <name>${name1}</name>, <name>${name2}</name> etc..
  - You can refer the sample at “src/test/resources/CLMOPP/CreateQuote_template.txt”

- For the Input JSON (Request Body): for each dynamic key (for which parameterization is required):
  - {“name”:${name}}
  - ${name} should be unique in the JSON. i.e., If you have same key multiple times, use as {“name”:${name1}}, {“name”:${name2}} etc..

- Prepare test data in excel, which is unique for an API. This file should be under src/test/resources/<Product_Name> folder:
  - File Name should be <APIName>.xls
  - Sheet Name should be same as <APIName>.testScope
  - Header names should be same as the dynamic values
  - E.g., if the dynamic value is ‘${name1}’, the header should be ‘name1’

- You can refer the sample test data at “src/test/resources/CLMOPP/CreateQuote.xls”
  - In the sample test data, the fields ‘Test Case Name’ and ‘Expected Response Code’ are not part of the input XML/JSON. The field ‘Test Case Name’ is used in the report. And the field ‘Expected Response Code’ is used to compare with the actual response code, once you fire the request.

API Preparation in eclipse:
---------------------------

- Now create a JAVA class for your API:
  - Class should be created under respective package. i.e., src/test/java/tests/<Product_Name>
  - Class Name should be <APIName> as configured in <Product_Name>.PROPERTIES file
  - Class should extend the base class (BaseTest_<Product_Name>), which is already available for each project.
  - Finally, the class should be as below:
```
package tests.<Product_Name>;
public class <APIName> extends BaseTest_<Product_Name> {
	@Override
	protected void setAPIName() {
		API_NAME = this.getClass().getName().substring(this.getClass().getName().lastIndexOf('.') + 1);
	}
}
```
  - You can refer the sample at “tests.CLMOPP.CreateQuote”

- Configure the JAVA Class in the testng.xml as below:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">

<suite name="Suite" parallel="false">
	<test>
		<classes>
			<class name="tests.<Product_Name>.<APIName>"/>
		</classes>
	</test>
</suite>
```
- Now run your API as below:
Right click on testng.xml and select “Run As -> TestNG Suite”
OR
Right click on pom.xml and select “Run As -> maven test”

- Find the report in target/API-Report/<Product_Name>/<APIName>
Open the API-Report.html in the browser

- If you get any runtime errors, check the LOG file in target/RESTClient.log and TestNG Report at “target/surefire-reports” if run as maven test. If run as TestNG Suite, find at “test-output”.

- If you need the report to be zipped and sent as mail:
  - Configure the mail parameters in pom.xml
  - Open Command Prompt
  - Go to Project root directory
  - Type “mvn test assembly:single postman:send-mail” and press ENTER
