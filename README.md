# LobeChat-MIX
A total of **20 critical vulnerabilities** have been discovered in **LobeChat versions 1.46.7 and lower**. Specifically, multiple endpoints within the application are vulnerable to **Oracle IDoc Injection** ([CVE-2013-3770](https://nvd.nist.gov/vuln/detail/CVE-2013-3770)) and **Spring Cloud Config Path Traversal** ([CVE-2020-5410](https://tanzu.vmware.com/security/cve-2020-5410)). This README provides a detailed analysis of these vulnerabilities, including how they were identified, and recommended actions for mitigation.

## Description of Vulnerabilities

### Oracle IDoc Injection

The Oracle IDoc Injection vulnerability was identified by sending manually crafted HTTP GET requests designed to exploit inadequate input validation. Specifically, payloads were introduced that enabled the execution of server-side commands. 

For example, sending a request like:

```http
GET /discover/search?q=project%20planning&type=%3c$fileName%3d%22..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fetc%2fpasswd%22$%3e%3c$executeService(%22GET_LOGGED_SERVER_OUTPUT%22)$%3e%3c$ServerOutput$%3e HTTP/1.1
```
demonstrated a significant flaw where the insertion of the pattern <$fileName="../etc/passwd"> suggested that the system lacks proper validation and sanitization of inputs, allowing unauthorized access to sensitive files.

### Spring Cloud Config Path Traversal
Further vulnerabilities were identified through path traversal techniques. By manipulating the request paths to include encoded traversal sequences like %252F..%252F.., testers successfully accessed critical files on the server. For instance, requests targeting endpoints such as /discover/models/ were able to retrieve /etc/passwd, exposing user and system data due to insufficient protection measures.

### Example of Path Traversal Request
Here's an example of an HTTP request that illustrates the path traversal vulnerability:
```http
GET /..%252F..%252F..%252F..%252F..%252F..%252F..%252F..%252F..%252F..%252F..%252Fetc%252Fpasswd HTTP/1.1
```
This request demonstrates the ability to traverse directories and gain access to sensitive files on the server.

## Discovery Process
The vulnerabilities were discovered through a manual testing approach, focusing on web application behavior in response to crafted HTTP requests. Key steps in the discovery process included:

- **Identifying Potential Endpoints**</br>
Targeting various endpoints within the LobeChat application.</br>

- **Crafting Payloads**</br>
Creating specific request patterns designed to probe for vulnerabilities.</br>

- **Testing Input Validation**</br>
Evaluating how the application handled user inputs and whether it appropriately filtered or sanitized such inputs.</br>

- **Executing Requests**</br>
Sending crafted requests and observing responses to confirm whether sensitive data could be accessed.</br>
