Connects to JIRA from Ballerina. 

# Module Overview

The JIRA connector allows you to view, create, update, and delete projects, project categories, project components, user 
accounts, and issues through the JIRA REST API. It handles basic authentication.

**JIRA Project Operations**

The `ballerina/jira7` module contains operations to create new JIRA projects, update or delete existing projects, and get all 
the information using either the ID or key of the project. It also contains operations for adding or removing users and 
groups related to a project role, viewing user account details of the project lead, viewing assignable issue types for a 
given project, etc.

**JIRA Project Category Operations**

The `ballerina/jira7` module contains operations that get all available project categories, delete existing categories, and 
create new project categories.

**JIRA Project Component Operations**

The `ballerina/jira7` module contains operations that get all details of a given project component, delete existing 
components, create a new project component related to a specific project, etc.

**JIRA Issue Operations**

The `ballerina/jira7` module contains operations that get all the details of a given issue using the issue key, delete existing 
issues, create new issues, etc.

## Compatibility
|                    |    Version     |  
|:------------------:|:--------------:|
| Ballerina Language |    1.2.0       |
| JIRA REST API      |    7.13.0      |  

## Sample
First, import the `ballerina/jira7` module into the Ballerina project and other modules.
```ballerina
import ballerina/jira7;
import ballerina/http;
import ballerina/auth;
import ballerina/config;
```
**Obtaining Credentials to Run the Sample**

1. Visit [Atlassian](https://id.atlassian.com/signup) and create an Atlassian Account.
2. Obtain the following credentials and base URL.
    * Username
    * Password  

You can now enter the credentials in the HTTP client config.
```ballerina
// Create the OutboundBasicAuthProvider
auth:OutboundBasicAuthProvider outboundBasicAuthProvider = new({
username: config:getAsString("JIRA_USERNAME"),
password: config:getAsString("JIRA_PASSWORD")
});

http:BasicAuthHandler outboundBasicAuthHandler = new(outboundBasicAuthProvider);
//Creation of connector endpoint
jira7:JiraConfiguration jiraConfig = {
    baseUrl: config:getAsString("JIRA_URL"),
    clientConfig: {
        auth: {
        authHandler: outboundBasicAuthHandler
    }
}
};

jira7:Client jiraClient = new(jiraConfig);

```

**Executing Sample**
* Request 

```ballerina
import ballerina/http;
import ballerina/jira7;
import ballerina/auth;
import ballerina/config;
import ballerina/io;

// Create the OutboundBasicAuthProvider
auth:OutboundBasicAuthProvider outboundBasicAuthProvider = new({
username: config:getAsString("JIRA_USERNAME"),
password: config:getAsString("JIRA_PASSWORD")
});

http:BasicAuthHandler outboundBasicAuthHandler = new(outboundBasicAuthProvider);
//Creation of connector endpoint
jira7:JiraConfiguration jiraConfig = {
    baseUrl: config:getAsString("JIRA_URL"),
    clientConfig: {
        auth: {
        authHandler: outboundBasicAuthHandler
    }
}
};

jira7:Client jiraConnectorEP = new(jiraConfig);

public function main(string... args) {

    jira7:ProjectCategoryRequest newCategory = { name: "Test-Project Category", description: "new category created from balleirna jira connector" };
    var output = jiraConnectorEP->createProjectCategory(newCategory);

    io:println(output.toString());

}
```

* Response Object

```ballerina
{
  "self": "http://localhost:8080/rest/api/2/projectCategory/10010",
  "id": "10010",
  "description": "new category created from balleirna jira connector",
  "name": "Test-Project Category"
}
```

The `getAllProjectSummaries` remote function returns the project summary of all the projects if successful or an `error` if unsuccessful.
```ballerina
var response = jiraClient->getAllProjectSummaries();
if (response is jira7:ProjectSummary[]) {
    io:println("Project Summary: ", response);
} else {
    io:println("Error: ", response);
}
```

The `createProject` remote function creates a JIRA project with the given name. It returns a `Project` object if successful or an `error` if unsuccessful.
```ballerina
var output = jiraClient->createProject("TST_PROJECT");
if (output is jira7:Project) {
    io:println("Project Details: ", output);
} else {
    io:println("Error: ", output.message);
}
```

The `createIssue` remote function creates an issue with the given issue details. `IssueRequest` is an object that contains all
the data that is required to create the issue. It returns an `Issue` object if successful or an `error` if unsuccessful.
```ballerina
jira7:IssueRequest newIssue = {
    key: "TEST_ISSUE",
    summary: "This is a test issue created for the Ballerina JIRA Connector",
    issueTypeId: "10002",
    projectId: ”1234”,
    assigneeName: “username”
};
var issueResponse = jiraClient->createIssue(newIssue);
if (issueResponse is jira7:Issue) {
    io:println("Issue Details: ", issueResponse);
} else {
    io:println("Error: ", issueResponse.message);
}
```
